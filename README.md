# Amazon Vine Analysis

## Overview
Companies like SellBy pay a small fee to Amazon and provide products to Amazon Vine members, who are then required to publish a review.  SellBy wants to determine if there is bias toward favorable reviews by Vine members.

## Setup and Tools
There were approximately 50 datasets to choose from for the project. After choosing a dataset of reviews for musical instruments and using PySpark the ETL process was used to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into pgAdmin.  Pyspark was used to then specifically determine if there is any bias toward favorable reviews from the paid Amazon Vine program members.

## ETL - Extract, Transform and Load
For Deliverable 1 the following dataset was read into a dataframe: <br>
https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Musical_Instruments_v1_00.tsv.gz

Four dataframes were then created - here are samples from all four: 
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

## Results of 

