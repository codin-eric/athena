# AWS Athena
A not so brief intro to Amazon Athena.

## Intro
Athena is ~~PrestoDB~~ [Trino](https://trino.io/blog/2020/12/27/announcing-trino.html)  as a service. this means that Athena is serverless, so there is no infrastructure to manage, and you pay only for the queries that you run.
> PrestoDB is a distributed SQL query engine designed to query large data sets distributed over one or more heterogeneous data sources.<br>

Athena also supports Hive DDL, ANSI SQL and works with commonly used formats like JSON, CSV, Parquet etc.

In terms of AWS ecosystem, it fits for the use case of ad-hoc querying with simplified management. Looking at other products, Redshift provides a data store for complex, multiple-joins based business intelligence workloads and EMR provides a method to run highly distributed processing frameworks such as Hadoop and Spark (also PrestoDB). Athena fits in between. It does not require cluster management but is not as flexible. [check this with papa]

## Terminology
- Tables - Tables are essentially metadata that describes your data similar to traditional database tables. One important difference is that there is no relational construct.

- Databases - A logical grouping of tables. Also know as catalog or a namespace.

- SerDe - Serializer/Deserializer, which are libraries that tell Hive how to interpret data formats. Athena uses SerDes to interpret the data read from Amazon S3. The following SerDes are supported
    - Apache Web Logs: `org.apache.hadoop.hive.serde2.RegexSerDe`
    - CSV: `org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe`
    - TSV: `org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe`
    - Custom Delimiters: `org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe`
    - Parquet: `org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe`
    - Orc: `org.apache.hadoop.hive.ql.io.orc.OrcSerde`
    - JSON: `org.apache.hive.hcatalog.data.JsonSerDe` OR `org.openx.data.jsonserde.JsonSerDe`

## Key Points
- It uses an internal data catalog to store information and schemas about the databases and tables that you create for your data stored in Amazon S3. You can modify the catalog using the Hive data definition language (DDL) or directly via the console.

- There is no data loading or transformation required. You can delete table definitions and schema without impacting the underlying data stored.

- When you create, update, or delete tables, those operations are guaranteed ACID-compliant. For example, if multiple users or clients attempt to create or alter an existing table at the same time, only one will be successful.

- You can use Athena to query underlying Amazon S3 bucket data that's in a different region from the region where you initiate the query. This is useful because Athena, as of this article is only available in some regions.

- Athena table names are case-insensitive.

- Amazon Athena is priced per query and charges based on the amount of data scanned by the query. You can store data in a variety of formats on Amazon S3. If you compress your data, partition, or convert it to columnar storage formats, you pay less because less data is scanned. Converting data to a columnar format allows Athena to read only the columns it needs to process the query hence reducing the costs of running queries that only need a few columns.

- In 2019 Athena introduced [federated queries](https://docs.aws.amazon.com/athena/latest/ug/connect-to-a-data-source.html) this allows you to query data outside s3 by running lambdas.

## Use cases
- Query any data in S3. If latency is not critical and queries can be run in the background this fits as well. For e.g analysing ELB log data in S3

- Go serverless across the stack. For example, API Gateway can be used to receive requests which are handled by a Lambda which in turn can leverage Athena for queries. The only persistent service used will be S3. Any static content generated from this can be delivered using S3's static website functionality.

- As with most things, you can mix and match AWS services based on your workloads. One possibility is to use an on-demand EMR cluster to process data and dump results to S3. Then use Athena to create ad-hoc tables and generate reports.

- “Facebook uses Presto for interactive queries against several internal data stores, including their 300PB data warehouse. Over 1,000 Facebook employees use Presto daily to run more than 30,000 queries that in total scan over a petabyte each per day” Source : https://prestodb.io/

## Limitations
- Not all ~~Presto~~ Trino functions are supported.

- There is no support for transactions. This includes any transactions found in Hive or Presto.

- Athena table names cannot contain special characters, other than underscore (_).

- There are also service limits, some of which can be increased by requesting it to AWS:
    - You can only submit one query at a time and you can only have 5 (five) concurrent queries at one time per account.
    - Query timeout: 30 minutes
    - Number of databases: 100
    - Table: 100 per database
    - Number of partitions: 20k per table
    - Amazon S3 buckets are limited to 100 and Athena also needs a separate bucket to log results.

### Fourther reading
- https://docs.aws.amazon.com/athena/latest/ug/other-notable-limitations.html
- https://docs.aws.amazon.com/athena/latest/ug/service-limits.html

## Excesise
You can find a quick example on [exercises](/exercise/README.md)

## Refs
- https://docs.aws.amazon.com/athena/latest/ug/what-is.html
- https://github.com/srirajan/athena
- https://www.youtube.com/watch?v=tzoXRRCVmIQ

Further reading:
- https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/