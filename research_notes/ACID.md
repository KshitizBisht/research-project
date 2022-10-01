# ACID transaction

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