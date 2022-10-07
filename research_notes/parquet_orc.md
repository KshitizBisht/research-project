# ORC
- self describing type aware columnar file format designed for hadoop workloads.
- support acid transactions but they are not designed to support OLTP transactions.
- It can support millions of rows updated per a transaction, but it can not support millions of transactions an hour.
