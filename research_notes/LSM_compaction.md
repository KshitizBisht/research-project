# Different compaction techniques for LSM

## Basic concepts

### Read amplification
- number of disk read a read request causes.

### Write amplification
- bytes of data actually written to the disk when one byte of data needs to be written.

### Space amplification
- the size of space occupied divided by the actual size of data

### Temporary space
- A compaction task requires a temporary space. Old data is deleted only after completing the compaction. The size of the temporary space depends on the sharding granularity of the compaction task

### Size tiered compaction
- suitable for write intensive workloads.
- read and space amplification is high
- this compaction tries to keep the size of the sstable or runs close on the same level.
- when data size of one level reached the limit, the whole level is merged and flushed to the next level to become a larger sstable.

### Level compaction
- reduce space and read amplification
- read optimized 