# hadoop vd hdfs vs hbase vs hive

## Hadoop
- manages distibuted computing and storage.
- does this dividing documents aross several stores and blocks across clusters of machines.
- replicates stores on cluster to achieve fault tolerance.
- performs distibuted processing by dividing job into smaller independent tasks. Task run parallel over clusters of computer.
- Hadoop helps to manage data storing and processing of a large set of data running in clustered systems while HDFS provides high-performance access to data across Hadoop clusters

## HDFS 
- runon two daemon datanode and namenode.
- namenode stores metadata about where the data is stored in datanode.
- datanode stores all the data. Data is replicated among datanodes.

## HBase
- open-source column-oriented data built on top of hadoop file system.
- part of hadoop exosystem that provides read and write access in real-time for data in hadoop file system.
-  HBase uses hash tables internally and then provides random access to indexed HDFS files.

## Hive
- Hive is a data warehouse software that allows users to quickly and easily write SQL-like queries to extract data from Hadoop.
- Hive is a data warehousing package built on the top of Hadoop
- In the case of Hadoop, you can implement SQL queries using MapReduce Java API. In the case of Apache Hive you can easily bypass the Java and simply access data using the SQL like queries

## Summary 
To summarize, Hadoop works as a file storage framework, which in turn uses HDFS as a primary-secondary topology to store files in the Hadoop environment.

HBase then sits on top of HDFS as a column-based distributed database system built like Google’s Big Table — which is great for randomly accessing Hadoop files. Hive, on the other hand, provides an SQL-like interface based on Hadoop to bypass JAVA coding.

