# Consistency

## Read your writes
- if a operation require the write w, then the same process performs read r then r must observe w's effects.

## Monotonic write
- if a process performs write w1 then w2, then all processes observer w1 before w2.

## Monotonic read
- ensures that if a process performs read r2, then read r2, then r2 cannot observe the state prior to the writes which were reflected in r1; read cannot go backwards.

## PRAM
- pipeline random access memory, attempts to relax existing coherent memory models to obtain better concurrency. 
- enforces that any pair of write executed by a single process are observed in the order the process executed the,; however, writes from different processed may be observed in different order.
- PRAM is exactly equivalent to read your writes, monotonic writes, and monotonic reads.

## write follows read
- AKA session causality, ensures that if a process reads a value v, which cam from a write w1 and later performs a write w2, then w2 must be visible after w1.

## Casual
- casually related operations should appear in the same order on all processes.

## sequential consistency
- safety property for concurrent systems.
- operations appear to take place in some total order and that order is consistent with the order of operations on each individual process.

## Linearizability
- one of the strongest single object consistency models.
- every operation appear to take place atomically, in some order, consistent with real time ordering of those operations.
- if operation A completes before operation B begin, then B should logically take effect after A.

## Read uncomitted
- prohibits dirty writes, where 2 transactions modify the same object concurrently before committing.
- operations can involve several primitive sub-operations performed in order. 
- operations can work on multiple objects in the system.
- does not impose real-time constraints.
- if a process A completes write w, then process B begins read r, r is not necessarily guaranteed to observe w.
- for real time consider strict serializibility. 

## Read committed
- transactions are not allowed to to observe writes from transactions which do not commit.

## Monotonic Atomic view
- strengthens read committed by preventing transactions from observing some, but not all, of a previously committed transaction's effects.
- once write from transaction T1 is observed by transaction t@ then all effects of T1 should be visible to T2. 

## cursor stability
- strengths read committed by preventing lost updates.
- introduces concepts of cursor, which refers to a particular object being accessed by a transactions.
- when a transaction reads an object using a cursor, that object cannot be modified by any other transaction until the cursor is released or transaction commits.

# Snapshot isolation
- each transaction appears to operate on an independent, consistent snapshot of the database.
- its changes are visible only to that transaction until commit time
- if a transaction T! has modified an object x and another transaction T2 committed a write to x after T's snapshot began and before T'commit then T1 must abort.

## Repeatable read
- allows phantom
- if a transaction T1 reads a predicate, like "the set of all people with the name “Dikembe”, then another transaction T2 may create or modify a person with the name “Dikembe” before T1 commits.
-Individual objects are stable once read, but the predicate itself may not be.

## Serializability
- txns appear to have occurred in some total order.
- guarantees that operation take place atomically; txn's sub operations do not appear to interleave with sub-operations from other txns.

## Strict Serializability
- operations appear to have occurred in some order, consistent with real-time ordering of those operations
- if operations A completed before operation B begin then A should appear to precede B in the serialization order.