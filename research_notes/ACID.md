# ACID transaction

### A (Atomicity) 
- each statement in  a transaction (write, read, update and delete) treated as single unit. Either entire statement is executed or nothing (prevents data loss). 

### C (Consistency)
- makes sure that transactions made in the table are done in a predictable way.

### I (Integrity)
- enures concurrent transactions dont interfere with each other. 

### D (Durability)
- ensures that the history of transaction will be maintained even incase of system failure.