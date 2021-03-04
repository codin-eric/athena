# AWS Athena
Example exercise<br>

## Load the data
Go to the S3 section on the AWS console
- load the file `company_funding.csv` into a S3 bucket. For this example I will be using the bucket `muttathenatest`

Now go to the Athena section on the AWS console.
- In the Query editor, create the database using the following.
```
CREATE DATABASE sampledata;
```
- Create the table. Note, you don't need to specify the file name. It automatically picks the CSV files under that folder*. The same logic applies if you have partitioned the data into sub folders. If you have partitioned the data, then you need to create table with the partition information as well.<br>

* Remember that S3 doesn't have a folder structure and is just objects with a common prefix that the UI groups them for better user experience.
```
CREATE EXTERNAL TABLE IF NOT EXISTS sampledata.companyfundingraw (
  permalink	STRING,
  company STRING,
  numEmps INT,
  category STRING,
  city STRING,
  state STRING,
  fundedDate STRING,
  raisedAmt	INT,
  raisedCurrency STRING,
  round STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
WITH SERDEPROPERTIES (
    'serialization.format' = ',',
    'field.delim' = ','
) 
LOCATION 's3://muttathenatest/raw/';
```
- Run a query
```
SELECT * from sampledata.companyfundingraw LIMIT 20;
```

## ETL the (un)fun way
  - create a table with parquet formatting and partitioned by `round`
  - Insert data from our companyfundingraw to the parquet table
  - ???
  - Profit!


### Create table
```
CREATE EXTERNAL TABLE IF NOT EXISTS sampledata.companyfunding (
  permalink	STRING,
  company STRING,
  numEmps INT,
  category STRING,
  city STRING,
  state STRING,
  fundedDate STRING,
  raisedAmt	INT,
  raisedCurrency STRING
)
PARTITIONED BY (year STRING)
STORED AS PARQUET
LOCATION 's3://muttathenatest/parquet/'
tblproperties ("parquet.compression"="SNAPPY");
```

### Insert
```
INSERT INTO sampledata.companyfunding
SELECT permalink,
       company,
       numEmps,
       category,
       city,
       state,
       fundedDate,
       raisedAmt,
       raisedCurrency,
       round
FROM sampledata.companyfundingraw

```

### ??? - Profit!
Congratulations you are now a ~~BI~~ Data Engineer 😌
