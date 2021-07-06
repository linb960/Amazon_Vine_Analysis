# Amazon Vine Analysis

## Overview
Companies like SellBy pay a small fee to Amazon and provide products to Amazon Vine members, who are then required to publish a review.  SellBy wants to determine if there is bias toward favorable reviews by Vine members.

## Setup and Tools
There were approximately 50 datasets to choose from for the project. After choosing a dataset of reviews for musical instruments, PySpark and the ETL process were used to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into pgAdmin.<br>

Pyspark was used to then specifically determine if there is any bias toward favorable reviews from the paid Amazon Vine program members.

## ETL - Extract, Transform and Load
For Deliverable 1 the following dataset was read into a dataframe: <br>
https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Musical_Instruments_v1_00.tsv.gz

Four dataframes were then created in this file https://github.com/linb960/Amazon_Vine_Analysis/blob/main/Amazon_Reviews_ETL.ipynb <br>

Here are samples from all four: 
```
Customer Dataframe
+-----------+--------------+
|customer_id|customer_count|
+-----------+--------------+
|   27314089|             1|
|    6711334|             1|
|   44525866|             4|
|   47461997|             1|
|   42560427|             1|

Products Dataframe
+----------+--------------------+
|product_id|       product_title|
+----------+--------------------+
|B003WZXSWM|Grover 205BC Mini...|
|B00ASHQQKI|SUPERNIGHT 5050 1...|
|B000T4BC1I|Aulos A211A/A511B...|
|B005I0HTCY|Neewer Black 3.5m...|
|B00JJ18VIE|Reliable Hardware...|
|B00US77MXI|Pick Geek Guitar ...|

Review ID Dataframe
+--------------+-----------+----------+--------------+-----------+
|     review_id|customer_id|product_id|product_parent|review_date|
+--------------+-----------+----------+--------------+-----------+
| RMDCHWD0Y5OZ9|   45610553|B00HH62VB6|     618218723| 2015-08-31|
| RZSL0BALIYUNU|   14640079|B003LRN53I|     986692292| 2015-08-31|
| RIZR67JKUDBI0|    6111003|B0006VMBHI|     603261968| 2015-08-31|
|R27HL570VNL85F|    1546619|B002B55TRG|     575084461| 2015-08-31|
|R34EBU9QDWJ1GD|   12222213|B00N1YPXW2|     165236328| 2015-08-31|

Vine Table
+--------------+-----------+-------------+-----------+----+-----------------+
|     review_id|star_rating|helpful_votes|total_votes|vine|verified_purchase|
+--------------+-----------+-------------+-----------+----+-----------------+
| RMDCHWD0Y5OZ9|          3|            0|          1|   N|                N|
| RZSL0BALIYUNU|          5|            0|          0|   N|                Y|
| RIZR67JKUDBI0|          3|            0|          1|   N|                Y|
|R27HL570VNL85F|          5|            0|          0|   N|                Y|
|R34EBU9QDWJ1GD|          5|            0|          0|   N|                Y|
|R1WCUI4Z1SIQEO|          5|            0|          0|   N|                N|

```
Each dataframe was then loaded into a preconfigured Postgres SQL database - here is an example of loading the Vine Table into the DB
```
# Write vine_df to table in RDS
vine_df.write.jdbc(url=jdbc_url, table='vine_table', mode=mode, properties=config)
```

## Results of Vine Data Analysis
The Vine Table was recreated in the this file https://github.com/linb960/Amazon_Vine_Analysis/blob/main/Vine_Review_Analysis.ipynb <br>

### Vine Table totals
The total number of reviews are __904663__ <br>
The reviews with total votes equal or greater than 20 is __16516__ <br>
The total number of helpful reviews calculated from the helpful votes divided by total votes that are greater or equal 50% is __14533__ <br>
The total number of 5 Star Ratings from the helpful reviews is __8244__ <br>

### Vine Member Results
The total Vine members paid for reviews is __60__ <br>
The total 5 Star Ratings from Vine members is __34__ <br>
The precentage of 5 Star Ratings / Total Vine members is __56.67%__

### Unpaid Review Results
The total number of unpaid reviews is __14473__ <br>
The total 5 Star Ratings from unpaid reviews is __8210__ <br>
The precentage of 5 Star Ratings / Total unpaid reviews is __56.73%__

## Summary
In conclusion because of the slight difference (.07%) seen in the results we can confidently say there is no bias between the paid and unpaid reviewers.  

### Additional analysis
Additional analysis of the data could include calculating and graphing the mean, mode and median over the entire star rating column for both paid and unpaid reviewers.  Since only 5 Star ratings were considered it would be interesting to see if any of these data points would show a bias.  For example if there were lots of 1's or 2's in the star rating column for unpaid reviews our overall results are affected if the star rating column for paid had more 3's and 4's.

