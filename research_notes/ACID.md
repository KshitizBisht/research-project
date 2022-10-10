# Transaction and concurrency control 

### Why acid transactions?
- ensures data reliability and integrity
- ensure data never falls into inconsistent state.

### Data Lakes
- central location that holds large amount of data in native, raw format.
- uses flat arhitecture and object storage to store data.
- stores data with metadata tags and unique identifier which makes it easier to locate and retreive data across regions.
- Data warehouse stores data in files and folder.
- data available to use is faster as it is kept in raw state.

### Delta lake
- brought ACID transactions to data lakes.

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
        - Schedule S is conflict serializable if you can transform S into a serial schedule by swapping consecutive non-conflicting operations of different transactions
    - view serializability (no dbms can do this)

- dependancy graph
    - one node per txn
    - Edge from Ti to Tj if:
        - An operation Oi of Ti conflicts with an operation Oj of Tj and
        - Oi appears earlier in the schedule than Oj
    - also known as precedence graph
    - A schedule is conflict serializable iff its dependency graph is acyclic.

    - ![Alt text](./dependency.png.png?raw=true "Title")

    - view serializability: 
    ![Alt text](./view.png?raw=true "Title")
### D (Durability)
- ensures that the history of transaction will be maintained even incase of system failure.
- changes of committed txn should be persistent.
- no torn updates.
- no changes from failed txns.
- use logging or shadow paging.

## Two phase locking

- locks vs latches
- ![Alt text](./lockVslatch.png?raw=true "Title")

- basic lock types
    - S-Lock : shared locks for reads
    - X-Lock : exclusive locks for writes

### Executing with locks
- trnsactions request locks
- lock manager grants or blocks request
- transactions release locks
- lock manager updates its internal lock-table
    - keeps tracks of txn that holds locks and txns that are waiting for lock.
    - ![Alt text](./locks.png.png?raw=true "Title")
    - lock does not prevent cycles in dependency graphs need something better.

### Two-phase locking Mechanism
- it determines whether a txn can accessan object in the database on the fly.
- does not need to know all about txn ahead of time.
- Phase 1 : Growing
    - txn request lock from manager.
    - manager grants or denies request.
- Phase 2 : Shrinking
    - txn is only allowed to release previously acquired locks. It cannot acquire new locks.
- ![Alt text](./2pl.png?raw=true "Title")
- sufficient to guarantee conflict serializibility
    - precedence graph is acyclic
- subject to cascading aborts.

### Strong strict 2 phase locking (solution to dirty reads)
- txn is allowed to release the lock after it has ended.
- a schedule is strict if the value written by txn is not read or overwritten by other txn until that txn finishes.
- Advantages
    - does not incur cascading abort
    - aborted txn can be undone by just restoring original values of modified tuples.

### Deadlock prevention
- cycle of txn waiting for locks to be released by each other.
- 2 ways of dealing with deadlock
    - deadlock detection 
    - deadlock prevention

### deadlock detections
- DBMS creates wait-for graph to keep track of what lock each txn is waiting to acquire.
    - nodes are txns
    - edge from Ti to Tj if Ti is waiting for Tj to release the lock.
- when dbms detects a deadlock it will select a victim txn that will either restart or abort dependin on how it was envoked.
- selecting a proper victim depends on different variables
    - age (lowest timestamp)
    - progress (least/most queries executed)
    - by the # of items already locked
    - by the # of txns that we have to rollback with it.
- Deadlock handling : Rollback length
    - Completely
    - Minimally
- deadlock prevention
    - DBMS kills one of txn to prevent deadlock.
    - assign priorities based on timestamp. Older timestamp = higher timestamp
- Intention lock
    - allows higher-level node to be locked in shared or exclusive mode without having to check all descendant nodes.
    - Intention shared (IS) - indicates explicit locking at lower level with shared locks.
    - Intention-exclusive (IX) - indicates explicit locking at lower lvel with exclusive locks
    - Shared+Intention-Exclusive (SIX) - The subtree rooted by that node is locked explicitly in shared mode and explicit locking is being done at a lower level with exclusive-mode locks.
- Lock escalation
    - dynamically ask for coarser-grained lock when too many low-level locks required. This reduces number of requests that the lock manager must process.

## Timstamp ordering
- Use timestamp to determine the serializability order of txns.
- each txn is assigned unique fixed timestamp that is increasing.
- multiple implementation statergies
    - system clock
    - logical counter
    - hybrid

### Basic timestamp ordering protocol
- Txns read and write objects without locks
- object is tagged with timestamp of last txn that successfully did read/write.
    - W-TS(X) - write timestamp on X.
    - R-TS(X) - Read timestamp on X.

#### T/O - Reads
- if TS(Ti) < W-TS(X) this violates timestamp order of Ti with regard to writer of X.
    - abort Ti restart with new timestamp.
- Else
    - allow Ti to read X
    - update R-TS(X) to max (R-TS(X), TS(X))
    - Make a local copy of X to ensure repeatable reads for Ti.

#### T/O - Writes
if TS(Ti) < R-TS(X) or TS(Ti) < W-TS(X)
    - abort and restart Ti
- else
    - Allow Ti to write X and update W-TS(X)
    - Also make a local copy of X to ensure repeatable reads.

### Thomas write rule
- ![Alt text](./thomas.png?raw=true "Title")

- Generates a schedule that is conflict serializable if you do not use the Thomas Write Rule

### Recoverable Schedules
- A schedule is recoverable if txns commit only after all txns whose changes they read, commit.

#### Optimistic concurrency control
    - Read Phase : track read/write sets of txns and store their writes in a private workspace
    - validation phase : when a txn commits, check whether it conflicts with other txns
    - write phase : if validation succeeds, aplpy private changes to database, Otherwise abort and restart the txn.

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