# Transaction and concurrency control 

## ACID transaction

### A (Atomicity) 
- each statement in  a transaction (write, read, update and delete) treated as single unit. Either entire statement is executed or nothing (prevents data loss). 
- Mechanism for ensuring atomicity
    - Logging
        - DBMS maintains logs of all in memory and disk to undo all aborted actions
    - Shadow Paging
        - DBMS makes copies of pages and transactions make changes to copies. Page is made visible only when the transaction is commited.m  

### C (Consistency)
- makes sure that transactions made in the table are done in a predictable way.
- Database consistency : database models real model and follows integrity constraints. Future transactions see effects of transactions in the past.
- transaction consistency : if database is consistent before transaction it will be consistent afer. Application responsibility.

### I (Integrity/Isolation)
- enures concurrent transactions dont interfere with each other. Each transaction is isolated from another.
- Mechanism for ensuring isolation
    - concurrency control protocol decides how DBMS decides the proper interleaving of operations from multiple transactions.
        - pessimistic : dont let problem arise in first
        - optimistic  : deal with problem when it happens
    - #### Interleaving transactions
        - 

### D (Durability)
- ensures that the history of transaction will be maintained even incase of system failure.

### Why acid transactions?
- ensures data reliabbility and integrity
- ensure data never falls into inconsistent state.

### Data Lakes
- central location that holds large amount of data in native, raw format.
- uses flat arhitecture and object storage to store data.
- stores data with metadata tags and unique identifier which makes it easier to locate and retreive data across regions.
- Data warehouse stores data in files and folder.
- data available to use is faster as it is kept in raw state.

### Delta lake
- brought ACID transactions to data lakes.


## Logging

### Failure classification 
- Transactional failure
    - Logical failure : txn cannot be complete due to some internal error condditions (integrity constraint voliation)
    - Internal state failure : DBMS must terminate an active transaction due to an error condition.
- System failure
    - Software failure : problem with OS or DBMS implementation (caugth zero exception).
    - Hardware failure : computer hosting DBMS crashes (power failure). Fail-stop Assumption: Non-volatile storage contents are
    assumed to not be corrupted by system crash.
- Storage media failure
    - Non-repairable hardware failure : A head crash or similar disk failure destroys all or part
    of non-volatile storage. Destruction is assumed to be detectable (e.g., disk
    controller use checksums to detect failures).

### Steal policy
Whether DBMS allows uncommited txn to overwrite the most recent commited value of an object in an non-volatile storage.
- Steal : is allowed
- No-steal : is not allowed

### Force policy
Whether the DBMS requires that all updates made by a txn are reflected on non-volatile storage before the txn can commit.
- FORCE: Is required.
- NO-FORCE: Is not required.

### No steal + force (ASK)
- <mark style="background-color: #FFFF00">Highlighted text</mark>   no undo of aborted txns required as no changes were commited to disk.
- never redo changes of commited txn as all changea re guaranteed to be writted to disk at commit time. 
