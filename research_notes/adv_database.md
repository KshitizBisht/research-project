# Advance database system (Andy Pavlo)

## Buffer pool
- very tuple access goes through buffer pool manager.
    - always translate a tuple's record if to its memory location.
    - Worker thread must pin pages that it needs to make sure that they are not swapped to disk.

## Concurrency control
- set locks to provide ACID guarantees for txns
- locks are stored in seperate data structure to avoid being swapped to disk.
- Schemes
    - 2PL : assume txn will conflict so they must acquire locks o ndatabase objects before they are allowed to access them.
    - Timestamp ordereing : assume that conflicts are rare so txn do not need to first acquire locks on datanase objects and instead check for conflicts at commit time.

## Logging + recovery
- Most DBMS use STEAL + NO-FORCE buffer pool policies, so all modifications has be to be flushed to the WAL before a txn can commit.
- Each log entry contains the before and after image of record modified.

## MVCC design decision
-  concurrency control protocol
    - timestamp ordering
        - use read-ts field in the header to keep track of the timestamp of the last txn that reads it.
        - txn can read version if the latch is unset and its Tid is between begin-ts and end-ts
        - txn create a new versio if no other txn holds latch and Tid is greater than read-ts.
    - optimistic concurrency control
    - 2 phase locking
        - txns can use tuple's read-cnt field as shared lock. Use txn-id and read-cnt together as exclusive lock.
        - if txn-id is zero then the txn acuires the shared lock by incrementing the read-cnt field
        - if both txn-d and read-cnt are zero then txn acquires the exclusive lock by setting both of them.

- if dbms reaches max timestamp value then it will have to restart at one, which will make previous version of txn appear as future.
- Postgres txn id wraparound
- set flag in each tuple header that it is frozen in the past. Any new txn will always be newer than a frozen version.

## version storage
- DBMS uses tuple's pointer field to create a latch-free version chain per logical tuple.
    - allows dbms to find the version that is cisible to particular txn at runtime.
    - indexes always point to "head" of the chain.
    - Append-only storage
    - time travel storage
    - delta storage.
- version chain ordering
    - oldest to newest (O2N)
        - append every new version to the endd of the chain
        - must traverse chain on look ups
    - Newest to oldest (N2O)
        - must update index pointers for every new version
        - dont have to traverse chain on look ups

## MVCC indexes
- mvcc dbms (usually) do not store version infromation about tuples with their keys
    - duplicate key problem





