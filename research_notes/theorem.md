# CAP theorem

## (consistency, availability and partition tolerance) CAP theorem?
- consistency
    - all clients see the same data at the same tine, no matter which node they connect to.
    - whenever data is written to one node, it must be instantly forwarded or replicated to all other nodes in the system, before the write is deemed 'successful'.
- availability
    - client making a request for data get response even if one or more nodes are down.
    - working nodes in distributed system must return a valid response for any request.
- partition tolerance
    - a partition is communications break within a distributed system
    - the cluster must continue to work despite any number of communication breakdown between nodes in the system.

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

# PACELC theorem
- extension of CAP theorem 
- Extends CAP theorem by introducing latency and consistency as additional attributed of distributed systems.
- Theorem states that else (E) even when the system is running normally in the absence of partitions, one has to choose between latency (L) and consistency (C).
- PACELC addresses key limitation of CAP theorem that sis that the system does not makes no provision for performance or latency. e.g., system can return response to query after 30 days but will still be considered available.
