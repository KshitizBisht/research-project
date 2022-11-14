# Transaction Management
- Metastore track read and write transactions that are currently in progress.
- metastore create transactionId for each write operation. 
    - transactionId strictly increasing from 1 and global within given metastore.
- when reading starts
    - provides table it is reading and current set of committed transaction and list of transactionId in flight.
    - each mapReduce job launched as part of query have same set of valid transactionsId.
- write operation
    - provides list of table being read, list of table being written, get a list of valid transactions Ids to read from and write transactionId.
    - all written data will be tagged with write transactionId.
    - 
- In both cases when command is finished it should notify metastore as metastore must identify abandoned transactions. 
- client notify metastore every 10 mins.
-

# Input Format
- directories with base and deltas that have modified the base.
- base file stored in the directory named base_$etid and deltas will be stored in the directories delta_$btid_$etid. where £btid is the first transaction id included in the file and $etid is the first transaction id not included in the file.
- every row in the table is uniquely identified by transactionId of the transaction that inserted it, row id and implicit bucket number. 
- when read query starts, it will get a valid transaction Ids and put them into the mapreduce's JobConf. 

# output format
- 