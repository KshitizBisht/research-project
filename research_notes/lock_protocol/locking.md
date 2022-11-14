# Implementation of locking

- means of concurrency
- mechanism required to manage locking request.
- mechanism known as lock manager.

## Data structure used in lock manager.
- known as lock table
- hash table where the names of data item is used for hashing
- chaining used to avoid collision
- each locked data item has linked list attached to it
- node in that linked list represents txn which requests loc, mode of lock(mutual/exclusive) and current status(waiting/granted)
- new lock request for data item added in the end of the list as new node.

