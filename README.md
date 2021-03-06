# Apache Spark ?
[Apache Spark](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark.html) has two main components - single driver and multiple Executors, each of these run in their own JVM. Driver is idle waiting for requests for executor tasks, Executor usually run in itʼs own JVM. Each executor has slots, these process tasks send by the Driver to the executor. Slots run in their own threads.

- the Driver must  decide how to partition the data so that it 
can be distributed for parallel processing
- the Driver is assigning a Partition of data to each task - in this 
way each Task knows which piece of data it is to process.
- Once started, each Task will fetch from the original data source the Partition
of data assigned to it

![image](https://user-images.githubusercontent.com/52529498/125184695-e88d3880-e1ed-11eb-9d07-5f7b97c18d94.png)


 Similar to Apache Hadoop, Spark is an open-source, distributed processing system commonly used for 
 big data workloads. However, Spark has several notable differences from Hadoop MapReduce. Spark has an
 optimized directed acyclic graph (DAG) execution engine and actively caches data in-memory, 
 which can boost performance, especially for certain algorithms and interactive queries. 
 Spark natively supports applications written in **Scala, Python, and Java**. It also includes several
 tightly integrated libraries for SQL (Spark SQL), machine learning (MLlib), stream processing (Spark streaming),
 and graph processing (GraphX). [More Apache Spark](https://aws.amazon.com/blogs/aws/new-apache-spark-on-amazon-emr/)
 
 > **Spark vs Hadoop performance**
 > By using a directed acyclic graph (DAG) execution engine, Spark can create a more efficient query plan for data transformations. Also, Spark uses in-memory, fault-tolerant resilient distributed datasets (RDDs), keeping intermediates, inputs, and outputs in memory instead of on disk. These two elements of functionality can result in better performance for certain workloads when compared to Hadoop MapReduce, which will force jobs into a sequential map-reduce framework and incurs an I/O cost from writing intermediates out to disk. Spark’s performance enhancements are particularly applicable for iterative workloads, which are common in machine learning and low-latency querying use cases.
> 



**Spark AWS options:**
- EMR
- Databricks

**Note:**
Spark does not provide a distributed file storage system, so it is mainly used for computation, on top of Hadoop. Spark does not need Hadoop to run, but can be used with Hadoop since it can create distributed datasets from files stored in the HDFS.

**Spark Characteristics**
- Spark does lazy load all the way, until it needs to perform an action
- Action - example count, sum
- Transformation - filter(sql example where clause), cache etc are all lazy
- The Dataframes returned/read by spark are all immutable.

**Cache:**
Cache is applied to DF using- .cache, a flag is enabled for spark to know caching 
of DF is enabled. The actual caching happens when an action is performed - show 
or count etc.
Cache should be used carefully because when cache is used the catalyst 
optimizer may not be able to perform its optimization. The optimizer may also not 
be able to do optimization when it has to [shuffle](https://medium.com/swlh/revealing-apache-spark-shuffling-magic-b2c304306142) - because of narrow DF to wider DF - wider operation like groupby.

>
> More on ***[Spark Partitioning](https://sparkbyexamples.com/spark/spark-partitioning-understanding/)***
>
> **Note:** When you want to reduce the number of partitions, It is recommended to use PySpark coalesce() over repartition() 
> as it uses fewer resources due to less number of shuffles it takes.
>

# pyspark sparksql-awsglue
Working wiith pyspark/spark-sql and aws glue using jupyter notebook/windows

I happened to attend one of the data conferences few years back when I came to know the power of spark-sql. I have been working with relational databases and sql
for a long time,and I sure there are many developers like me out there who missed the hadoop boat while working on rdbms based projects. I have spend some time searching the web, reading up on pluralsight, googled into various websites and  cobbled up a spark/sql on jupyter notebook running on windows, so thought it will be useful for folks like me  who want to get into big data via spark-sql.

Spark runs on top of Hadoop, then why do we need Spark? Spark abstracts the hadoop  distributed processing. We work on data as though it were on a single node, but under the hood, the data processing occurs on multiple nodes on your spark cluster. The most interesting thing for me is it allows me to use sql with columnnar storage - parquet, with csv, etc.. and allows me to harness the power of hadoop without going into the details of hadoop/map-reducce.


# AWS Glue Catalog:
WE have tons of data stored in S3, the glue helps us to add a metadata on top our S3 data. You can use glue 
crawler to perform ETL, to pull the data and create metadata in glue catalog. I like to use boto3 api to 
create glue metadata in glue catalog to map data in S3. This S3 data can be accessed 
via sql - using redshift spectrum, accessed via AWS EMR/pyspark/spark-sql, or via  
Aws Athena/Mesos(the sql engine), etc.  

It's easy to create table metadata in Glue catalog via boto3 glue api, you can use DDL SQL to create table, add partitions etc.
We will access this table mapped on S3 using sql via jupyter notebook running on a local pc using [aws data wrangler](https://aws-data-wrangler.readthedocs.io/en/stable/what.html).

# Schema on read/write
- [Schema on read/write](https://luminousmen.com/post/schema-on-read-vs-schema-on-write)
- RDMS - schema on write, here we define the columns, data format, relationships of columns, etc. before the actual data upload.
- AWS Glue - schema on read, with glue we can create the schema at the time were consume/read data, this allows for  fast data ingestion because data shouldn't follow any internal schema — it's just copying/moving files. This type of data handling is more flexible in case of big data, unstructured data, or frequent schema changes.

# Spark sql
- CreateTableView based on dataframe - createOrReplaceTempView creates (or replaces if that view name already exists) a lazily evaluated "view" 
that you can then use like a hive table in Spark SQL. It does not persist to memory unless you cache or persist the 
dataset that underpins the view.
  
- Caching - Spark will read the parquet, csv,etc.., execute the query only once and then cache it.
  Then the code in the loop will use the cached, pre-calculated DataFrame. Imagine that 
  you are working with a lot of data, and you run a series of queries and actions on it 
  without using caching. It runs again and again without you even noticing.
  This can add hours to the job running time or even make the job fail.
   You can also use SQL’s CACHE TABLE [tableName] to cache tableName table in memory. 
   Unlike cache and persist operators, CACHE TABLE is an eager operation which is executed as soon
   as the statement is executed - sql("CACHE TABLE [tableName]"). You could however use LAZY keyword
  to make caching lazy - sql("CACHE LAZY TABLE [tableName]")
- Cache vs Persist - The only difference between cache() and persist() is ,using Cache technique we 
  can save intermediate results in memory only when needed while in Persist() we can save the intermediate
  results in 5 storage levels(MEMORY_ONLY, MEMORY_AND_DISK, MEMORY_ONLY_SER, MEMORY_AND_DISK_SER,
  DISK_ONLY). Without passing argument, persist() and cache() are the same - results in memory.
   
-  Explain Plan/Query Execution Plan - The best way to make sure everything has run as expected is to look
   at the execution plan. You can see in the following execution plan the keywords InMemoryTableScan and 
   InMemoryRelation which indicate that we are working on a cached DataFrame.dataframe_object.explain()
   
# Parquet file Gzip vs Snappy
GZIP compression uses more CPU resources than Snappy or LZO, but provides a higher compression ratio. GZip is often a good choice for cold data, which is accessed infrequently. Snappy or LZO are a better choice for hot data, which is accessed frequently. Snappy often performs better than LZO. ref: google search
Use Snappy if you can handle higher disk usage for the performance benefits (lower CPU + Splittable).
> When Spark switched from GZIP to Snappy by default, this was the reasoning:

Based on our tests, gzip decompression is very slow (< 100MB/s), making queries decompression bound. Snappy can decompress at ~ 500MB/s on a single core.
- Snappy:
  - Storage Space: High
  - CPU Usage: Low
  - Splittable: Yes (1), If you need your compressed data to be splittable, then use BZip2, LZO, and Snappy formats which are splittable, but GZip is not.
  
- GZIP:
  - Storage Space: Medium
  - CPU Usage: Medium
  - Splittable: No
 
 ref: https://stackoverflow.com/questions/35789412/spark-sql-difference-between-gzip-vs-snappy-vs-lzo-compression-formats

# Interactive Jupyter Notebook
Under aws-glue there are 2 ipynb files. 
- [spark-sql](https://github.com/padmaparam/sparksql-awsglue/blob/main/aws-glue/spark-sql-parquet.ipynb) 
- [aws-glue](https://github.com/padmaparam/sparksql-awsglue/blob/main/aws-glue/aws-glue.ipynb) -  using [aws data wrangler](https://aws-data-wrangler.readthedocs.io/en/stable/what.html) seems to be nicely written and easy to use with pandas dataframe

# Notes
- [difference between s3n, s3a and s3](https://stackoverflow.com/questions/33356041/technically-what-is-the-difference-between-s3n-s3a-and-s3)
- [Spark RDD Stages,DAG](https://medium.com/@goyalsaurabh66/spark-basics-rdds-stages-tasks-and-dag-8da0f52f0454)
- [Transformation by Example](https://sparkbyexamples.com/apache-spark-rdd/spark-rdd-transformations/)
- [Apache Spark Interview Questions](https://www.mygreatlearning.com/blog/spark-interview-questions/)
- [Interview Questions](https://www.zeolearn.com/interview-questions/spark)
