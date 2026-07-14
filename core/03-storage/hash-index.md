# Hash Indexes
- key-value data
- Similar to HashMap
- It usually has 2 components
  - In memory HashMap, key = hash(indexed columns) and value = byte offset on disk
  - On disk, write ahead log

Components : 
a. Hash function : 
h(key) → integer

b. Buckets : 
Bucket 42:
→ (user123, offset=1000)
→ (user456, offset=2000)

Each entry points to:
- Value OR
- Offset in data file

Collision :
Chaining : Bucket → Linked list of entries

What is Bitcask?
Core idea:

Store all data in append-only log files, and keep a hash map in memory that points to the latest value.

```
          +-------------------+
          |   Hash Index      |   (in RAM)
          | key → file offset |
          +-------------------+
                    |
                    ↓
          +-------------------+
          |   Data Files      |   (append-only on disk)
          +-------------------+
```

Write :
```
PUT(key, value)

1. Append to log file:
   [key, value]

2. Update hash index:
   key → offset in file
```

Read :
```
GET(x)

1. Look up hash index → offset = 40
2. Seek to file offset
3. Read value
```

Delete : A special marker named tombstone is used

Compaction : 
1. Read old file
2. Keep only latest values
3. Write new compacted file
4. Update index

| Operation   | Cost                      |
| ----------- | ------------------------- |
| Write       | 🚀 Sequential (very fast) |
| Read        | 🚀 O(1) + 1 disk seek     |
| Range query | ❌ not supported           |

Downside : Entire hash of keys to byte offset must fit in memory