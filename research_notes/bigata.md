# hadoop vd hdfs vs hbase vs hive

## Hadoop
- manages distibuted computing and storage.
- does this dividing documents aross several stores and blocks across clusters of machines.
- replicates stores on cluster to achieve fault tolerance.
- performs distibuted processing by dividing job into smaller independent tasks. Task run parallel over clusters of computer.

## HDFS 
- runon two daemon datanode and namenode.
- namenode stores metadata about where the data is stored in datanode.
- datanode stores all the data. Data is replicated among datanodes.