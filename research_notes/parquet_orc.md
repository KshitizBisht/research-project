# ORC
## Motivation 
- RCFile has limitations because it treas each column as a binary blob without semantics.
- ORC uses and retains type information from the table definition.
![Alt text](./OrcFileLayout.png?raw=true "Title")

## File tail
- ORC stores top level indexes at the end of the file.
- file's tails consists of 3 parts; file metadata, file footer and postscript.

## Postscript
- provides necessary information to interpret the rest of the file including the length of the file's footer and metadata sections, the version of teh file and kind of compression used.
- postscript is never compressed
- reading an ORC file works backwards through the file.
- ORC reads last 16k bytes with hope of reading footer and postscript section.

message PostScript {
 // the length of the footer section in bytes
 optional uint64 footerLength = 1;
 // the kind of generic compression used
 optional CompressionKind compression = 2;
 // the maximum size of each compression chunk
 optional uint64 compressionBlockSize = 3;
 // the version of the writer
 repeated uint32 version = 4 [packed = true];
 // the length of the metadata section in bytes
 optional uint64 metadataLength = 5;
 // the fixed string "ORC"
 optional string magic = 8000;


enum CompressionKind {
 NONE = 0;
 ZLIB = 1;
 SNAPPY = 2;
 LZO = 3;
 LZ4 = 4;
 ZSTD = 5;
}

## Footer
- footer contains the layout of the body of the file, the type schema information, number of rows and statistics about each of the column.
- three parts of the file - header, body and tail.
- header contains of the bytes to support tools that want to scan the front of the file to determine the type of the file.
- body contains rows and indexes
- tails gives the file information as described.

message Footer {
 // the length of the file header in bytes (always 3)
 optional uint64 headerLength = 1;
 // the length of the file header and body in bytes
 optional uint64 contentLength = 2;
 // the information about the stripes
 repeated StripeInformation stripes = 3;
 // the schema information
 repeated Type types = 4;
 // the user metadata that was added
 repeated UserMetadataItem metadata = 5;
 // the total number of rows in the file
 optional uint64 numberOfRows = 6;
 // the statistics of each column across the file
 repeated ColumnStatistics statistics = 7;
 // the maximum number of rows in each index entry
 optional uint32 rowIndexStride = 8;
}

## Stripe information
- body of the file is divided into stripes.
- each stripe is self contained and may be read using only its own bytes combined with footer ans postscript.
- each stripe contains only entire row so that rows never straddle stripe boundaries.
- stripes have three section : set of indexes for the rows within stipe, data itself and stripe footer.

message StripeInformation {
 // the start of the stripe within the file
 optional uint64 offset = 1;
 // the length of the indexes in bytes
 optional uint64 indexLength = 2;
 // the length of the data in bytes
 optional uint64 dataLength = 3;
 // the length of the footer in bytes
 optional uint64 footerLength = 4;
 // the number of rows in the stripe
 optional uint64 numberOfRows = 5;
}

## Column statistics
- for each column the writer records the count and other useful fields.
- it also stores min, max values, sum.


message ColumnStatistics {
 // the number of values
 optional uint64 numberOfValues = 1;
 // At most one of these has a value for any column
 optional IntegerStatistics intStatistics = 2;
 optional DoubleStatistics doubleStatistics = 3;
 optional StringStatistics stringStatistics = 4;
 optional BucketStatistics bucketStatistics = 5;
 optional DecimalStatistics decimalStatistics = 6;
 optional DateStatistics dateStatistics = 7;
 optional BinaryStatistics binaryStatistics = 8;
 optional TimestampStatistics timestampStatistics = 9;
 optional bool hasNull = 10;
}

- the example below shows storing min, max and sum as column statistics.

message IntegerStatistics {
 optional sint64 minimum = 1;
 optional sint64 maximum = 2;
 optional sint64 sum = 3;
}

## File metdata
- contains column statistics at stripe level granularity.

## Compression
- if generic compression then every part of the file except postscript is compressed with that codec.
- ORC write compressed streams in chunks with header (to skip over compressed parts). 
- if compressed data is larger than original then original data is stored ans isOriginal flag is set.
- each chunks is compressed independently so that as long as decompressor starts at the top of header, it can start decompressing without previous bytes.

![Alt text](./CompressionStream.png?raw=true "Title")

## Stripes
- body of the orc consists of stripes.
- large and independent of each other and are often processed by different tasks.
- data for each column is stored separately.

## Stripe footer
- contains the encoding of each column and the directory of the streams including their location.
- to describe each stream, orc stores the kind of stream, the column id and the stream size.

## Indexes
### row group indexes
- consists of row_index stream for each primitive column that has an entry for each row group.
- each rowindexentry gives the position of each stream for the column and statistics of that row group.

# Parquet 
- Free and open-source column oriented data storage. 
- consists of 2 parts
    - Data
    - Metadata
- data is written first in the file and metadata is written at the end to allow for single pass writing.
- body consists of :-
    - header
    - data body
    - footer
- file format contains a 40byte magic number in the header "PAR1" and in the end of the footer. this number indicates that the file is in parquet format.
- metadata stored in footer.

## Blocks in parquet file
- block is stored in the form of row group.
- data in parquet file is partitioned into multiple row groups. These row groups in turn consists of one or more column chunks which corresponds to column in the dataset.
- data for each column chink is then written in the form of pages.
- each page contains value for a particular column only.
![Alt text](./parquet.jpeg?raw=true "Title")

## Footer
- footer metadata includes version of the format, the schema, key-value paris, metadata for the columns in the file.
- column metadata are type, path encoding, number of values.


# WHY ORC SUPPORTS ACID TRANSACTIONS
"Only ORC file format is supported in this first release.  The feature has been built such that transactions can be used by any storage format that can determine how updates or deletes apply to base records (basically, that has an explicit or implicit row id), but so far the integration work has only been done for ORC."



either take hive and try to integrate parquet with it
Or take hbase and try to integrate orc with it