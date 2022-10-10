# Transaction and concurrency control 

## Concurrency control theory

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
        - interleave txn to maximize concurrency

- properties of schedules
    - Serial schedule : does not interleave actions of different transactions
    - Equivalent schedule : effect of executing the first schedule is identical to effect of executing second schedule.
    - serializable schedule : a schedule that is euivalent to some serial execution of the transactions.
- 2 operations conflict if : they are by same transactions, they areon the same object and at least of them is write.
- interleave execution anomalies
    - read-write conflicts
    ![Alt text](./rw_conflict.png?raw=true "Title")
    - write-read conflicts
    ![Alt text](./wr_conflict.png?raw=true "Title")
    - write-write conflicts
    ![Alt text](./ww_conflict.png?raw=true "Title")

- Different levels of serializability
    - conflict serializability (most dbms try to supprt this) 
    - view serializability (no dbms can do this)

- dependancy graph
    - one node per txn
    - Edge from Ti to Tj if:
        - An operation Oi of Ti conflicts with an operation Oj of Tj and
        - Oi appears earlier in the schedule than Oj
    - also known as precedence graph
    - A schedule is conflict serializable iff its dependency graph is acyclic.

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
- no undo of aborted txns required as no changes were commited to disk.
- never redo changes of commited txn as all changea re guaranteed to be writted to disk at commit time. 

### Shadow paging
Maintain 2 serpate copies of the database
- Master : contains changes from committed txns.
- Shadow : temporary database with changes made fron uncommitted txns.
txn only updates shadow pages and swtiches the shadow to become new master on commit

buffer policy : No-steal + force

### write ahead log
- log file contains details about changes made to database.
- DBMS must write to disk the log files records that corresponds to changes made to database object before it can flush that object to disk.

(q) if the database crashes after writing log files to disk before making changes to database then it starts from the last checkpoint right?

buffer policy : Steal + no-force

#### WAL protocol
- <BEGIN> marks the starting of the txn
- <COMMIT> marks the finish of the txn and DBMS makes sure that the results are flushed.
- Log entry contains information about change to single object
    - transaction id
    - object id
    - before value
    - after value
- Use group commit to batch multiple log flushes together to amortize overhead.
- DBMS takes checkpoint to avoid reading entire log incase of failure.
- <CHECKPOINT> to log and flush to stable storage.
- txn committed before checkpoint is ignored.
- txn commited after checkpoint need to redo
- txn that did not commited after checkpoint (due to failure) needs to be undone.
- checkpoiting too often can degrade the speed of DBMS.

## Recovery
- log record includes globally unique identifier, log sequence number (LSN)
![Alt text](./lsn.png?raw=true "Title")

- each data page contains pageLSN
- system keeps track of flushed LSN.
- Before page x can be written to disk, we must flush log at least to the point where: → pageLSNx ≤ flushedLSN
- when the pageLSN in the buffer pool is greater than the flushedLSN it is safe to unpin or remove from pool because it has not been flushed to disk yet.
- update pageLSN when txn modeifes record in a page.
- update flushed LSN in memory everytime the DBMS writes out the buffer to disk.

### transaction abort
- prevLSN : previous LSN for the txn.
- maintains a linked list for each txn that makes it easy to walk through its record.

### Non-fuzzy checkpointing
- DBMS halts everything when it takes a checkpoint to ensure a consistent snapshot. bad runtime performce but easy recovery.
- (better approach) : pause txn that modifies database, prevent queries from acquiring write latch n table.

### fuzzy checkpointing
- DBMS allows active txn to continue the run while the system writes the logs record for the checkpoint.
- ATT contains active txn started before checkpoint
- DPT contains the dirty pages before checkpoint.

## Recovery phases
- Phase 1 : analysis
    - Read WAL from the last masterrecord to identify dirty pages in the buffer pool and active txn at the time of the crash.
- Phase 2 : Redo
    - repeat all actions starting from an appropiate point in the log
- Phase 3 : undo
    - reverse the actions of txn that did not commit before the crash.

### Analysis phase
- scan log forward from last successful checkpoint.
- if txn-end found remove its corresponding txn.
- other records
    - add txn to ATT with undo status.
    - oncommit change status to commit.
- for update record
    - if page P not in DPT add P to DPT set its recLSN = LSN.
- at the end of analysis phase.
    - ATT identifies txns active at time of crash.
    - DPT identifies which dirty page might not have made to disk.

### Redo Phase
- repeat the history to reconstruct state at the moment of the crash.
- reapply all update (even aborted txn) and redo CLRS.
-