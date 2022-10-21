# CAP theorem

## (consistency, availability and partition tolerance) CAP theorem?
- consistency
    - all clients see the same data at the same tine, no matter which node they connect to.
    - whenever data is written to one node, it must be instantly forwarded or replicated to all other nodes in the system, before the write is deemed 'successful'.
- availability
    - client making a request for data get response even if one or more nodes are down.
    - working nodes in distributed system must return a valid response for any request.
- partition tolerance
    - a partition is communications break (or delay) within a distributed system 
    - the cluster must continue to work despite any number of communication breakdown between nodes in the system.
- theorem formalizes the tradeoff between consistency and availability when there’s a partition.


![Alt text](./cap.png?raw=true "Title")
example where user tries to write and read from different nodes.
when there's a partition the system has two options
    - can fail the request breaking system's availability.
    - execute request but returning old value for read breaking system's consistency.
system cannot do both because of the network failure between two nodes.



## Different types of databases
- CP database
    - delivers consistency and partition tolerance at the expense of availability.
    - when partition occurs between any two nodes, the system has to shut down the non consistent node until partition is resolved.
- AP database
    - delivers availability and tolerance at the cost of consistency. 
    - during partition, all nodes remain available but those at the wrong end of the partition might return an older version of data than others. 
- CA database
    - delivers consistency and availability.
    - fails to deliver this if there's a partition between any two nodes in the system therefore cannot deliver fault tolerance.


# Missing part in CAP
- cannot avoid partition in distributed system.
- RDBMS choose consistency, NO sql choose availability.
- CAP theorem does not state what happens incase of no partition.




# PACELC theorem
![Alt text](./pacelc.png?raw=true "Title")
- extension of CAP theorem 
- - Extends CAP theorem by introducing latency and consistency as additional attributed of distributed systems.
- states that in a system that replicates data
    - if there is a partition (P), a distributed system can tradeoff between availability and consistency (A and C)
    - else (E) when a system is running normally in the absence of partition the system can trade off between latency and consistency.
- PACELC addresses key limitation of CAP theorem that the system does not makes no provision for performance or latency. e.g., system can return response to query after 30 days but will still be considered available.
- dynamo and cassandra are PA/EL system, choose availability when partition else choose lower latency.
- big table and hbase are PC/EC system, choose consistency giving up availability and lower latency.
- MongoDB is PA/EC system in case of network partition choose availability but otherwise guarantees consistency. Alternately, mongoDB is configured to write on majority replicas and read from the primary, it could be categorized as PC/EC