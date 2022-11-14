# Hbase
- column oriented database implementation of google big table
- can manager structured and semi-structured data.
- uses write ahead logging and distributed configuration.
- build on top of hadoop/HDFS.
- low latency operations.
- gives advantage of random access

## Architecture 
- follows master-slave architecture.
- Master known as HMaster and multiple region server known as HRegionServer.
- Each region server contains multiple regions - HRegions.
- data in Hbase stored in tables and these tables are stored in regions.
- when table becomes too big, the table is partitioned into multiple regions.

### HMaster
- performing administration
- managing and monitoring the cluster
- assigning regions to the region server
- controlling the load balancing and failover.

### HRegionServer
- hosting and managing regions
- splitting regions automatically
- handling read/write request
- communicating with clients directly
- contains a write-ahead log (HLog)
- region in turn is made up of memstore and multiple StoreFile


### LSM tree in HBase
- in-memory tree which contains most recent updates
- disk store tree which arranges the rest part of the data in form of immutable B-tree located in hard drive

### Data stored in Hbase
- stores row of data in table
- table split into chunks of rows called "region"
- regions are non-overlapping: single row key belongs to exactly on region at any point in time.
- consistency and partition tolerance


Hbase sits on top HDFS. Hbase is a non-overlapping means unique row key to exactly one server and HDFS maintains the replicated version of the file for durability?
Hbase has only one row data which means client will work on single object.

Hbase supports acid transactions only at row level means does not guarantee acid compliant sif transactions operates with multiple rows.

lock table 
- if maintained inside region server and region server decided to split and the lock that was working on that table might be lost. Solution - keep the lock and row in same data region or keep lock table centralized.

## Hbase synchronization mechanism
- countdownlatch implementation of the mutex and row lock held during row data is updated
- read-write lock based on the reentrantreadwrite implementation, which can add read lock or write lock to the critical resource.