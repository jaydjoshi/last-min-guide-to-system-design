# SSTable ( Sorted String Table) and LSM ( Log Structured Merge) tree

In Hash index order dint matter, SSTable is an evolution of hash index where key value pair is sorted by key

**LSM (Log-Structured Merge Tree)** is a storage engine optimized for fast writes.

Core philosophy:

> Write fast now, organize later.

Instead of updating data in place (like B-Trees), LSM Trees:

* Buffer writes in memory
* Flush sorted files to disk
* Merge files in background

Used in:

* Cassandra
* RocksDB
* LevelDB
* HBase

---

##### Core Components

```text
Write Ahead Log (WAL)
MemTable
SSTables
Compaction
Bloom Filters
Sparse Indexes
```

---

# Write Path

```text
Write Request
   ↓
WAL (durability) (DISK)
   ↓
MemTable (sorted in memory) (MEMORY)
   ↓
Flush when full
   ↓
SSTable on disk (DISK)
```

###### WAL

* Append-only log
* Used for crash recovery

###### MemTable

* In-memory sorted structure
* Often implemented using skip lists, red black tree or AVL tree

---

##### What is an SSTable?

**SSTable = Sorted String Table**

Properties:

* Immutable
* Sorted by key
* Written sequentially

Example:

```text
apple -> 100
banana -> 200
cat -> 300
```

---

##### Read Path

Reads typically do:

```text
1. Check MemTable
2. Check Bloom Filter
3. Use Sparse Index
4. Read correct disk block
```

---

##### Sparse Index

Stores sampled keys and offsets.

Example:

```text
apple -> offset 0
cat   -> offset 400
```

Jump close to target, then scan.

---

##### Bloom Filters

Avoid unnecessary disk reads.

Answers:

* Definitely not present
* Probably present

Helps reduce read amplification.

---

##### Compaction

Merges SSTables.

```text
SST1:
a=1
b=2

SST2:
b=5

Merge:
a=1
b=5
```

pointers in each SStable, SST1 and SST2, compare keys at pointers, 
merge if key is same. Else write the smaller key and increment pointer.

Benefits:

* Removes duplicates
* Removes tombstones
* Improves reads

---

# Tombstones

Deletes are stored as markers:

```text
user123 = TOMBSTONE
```

Removed during compaction.

---

##### Amplification Problems

###### Read Amplification

One read may check many SSTables.

###### Write Amplification

Compaction rewrites data multiple times.

###### Space Amplification

Old and new copies may coexist.

---

##### Compaction Strategies

###### Size-Tiered

* Merge similarly sized files
* Good for write-heavy systems

###### Leveled

```text
L0 → L1 → L2
```

* Better reads
* More write amplification

---

##### Why LSM Trees Are Fast for Writes

They convert:

```text
Random writes → Sequential writes
```

Major reason they scale well.

---

##### Best Use Cases

Use LSM Trees for:

* Write-heavy workloads
* Time-series
* Logging
* Distributed databases
* Event ingestion

---

##### Mental Model

```text
Buffer writes
Flush sorted files
Search across files
Merge files in background
```

That is an LSM Tree.

---

##### One-Line Summary

> LSM Trees use MemTables, immutable SSTables, and compaction to turn random writes into efficient sequential writes while keeping reads fast with Bloom filters and