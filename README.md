# Last minute guide to System Design

## Index

1. [Foundation of Data systems](#foundation-of-data-systems)
       
   - [Key characteristics of Systems](#key-characteristics-of-systems)
     - [Reliability](#reliability)
     - [Scalability](#scalability)
     - [Maintainability](#maintainability)
   - [Data model and query languages](#data-model-and-query-languages)
   - [Serialization and Deserialization](#serialization-and-deserialization)
   - [Storage and Retrieval](#storage-and-retrieval)
     - [Hash Indexes](#hash-indexes)
     - [SSTable and LSM tree](#sstable-and-lsm-tree)
     - [B Tree](#b-tree)
 

2. [Distributed Data Concepts](#distributed-data-concepts)
   - [Replication](#replication)
     - [Leaders and followers](#leaders-and-followers)
     - [Multi-leader](#multi-leader)
     - [Leaderless](#leaderless)
   - [Partitioning](#partitioning)
     - [By key range](#by-key-range)
     - [By Hash of key](#by-hash-of-key)
   - [Transactions](#transactions)
     - [ACID](#acid)
     - [BASE](#base)
   - [Consistency](#consistency)
     - [Linerizability](#linerizability)
   - [Distributed transactions](#distributed-transactions)
     - [2 PC](#two-phase-commit)
     - [3 PC](#three-phase-commit)
     - [SAGA](#saga)
   - [Consensus](#consensus)
     - [Paxos](#paxos)
     - [Raft](#raft)

3. Other Building blocks of System Design
   - Front end
   - Load Balancers
   - Reverse proxy
   - Backend
     - Monolith
     - Microservices
   - Caching
   - CDN
   - Message Queues
   - DB
     - RDBMS
     - NoSQL
   - Communication protocols between client and server
     - Long polling
     - Websockets
     - Server Sent Events
   - Logging, metrics and automation
   
4. Important theorems/ short forms
     - RUM Conjecture ( Read, Update and Memory)
     - CAP Theorem
     - PACELC Theorem
     - The scale cube
     - ACID and BASE
     
5. How to approach System Design

6. Memoize these topics!
   - Power of Two
   - Latency numbers every programmer should know
   - Availability numbers

7. How does famous Systems work?
    - Cassandra
    - Memcached, Redis or Ignite
    - Kafka
    - S3
    - Zuul Notification system 
    - Dynamo
    - Bigtable
    - MapReduce
    - Hadoop
    - Consensus like Paxos or Raft
    - Zookeeper

8. System Design questions with solutions
   - API Rate limiter
   - Consistent Hashing
   - Key value store
   - Unique ID generator
   - URL shortening System like TinyURL
   - Pastebin
   - Photo sharing system like Instagram
   - Web Crawler
   - Notification system
   - Twitter
   - News feed system like Facebook newsfeed
   - Chat System like Facebook messenger
   - Search autocomplete/ Typeahead suggestion
   - Google drive or Dropbox
   - Video sharing system like YouTube or Netfilx
   - Search application like twitter search or facebook search
   - Proximity server system like Yelp or Nearby friends 
   - Ride sharing service like Uber, Lyft, Ola, Didi etc
   - Online ticketing system like TicketMaster or BookMyShow
   
## Foundation of Data systems

### Key characteristics of Systems.
#### Reliability
A system should work correctly at a desired performance in case of Adversity. Adversities like, 
##### Hardware Faults
   1. Hard drive crash
   2. RAM crash
   3. Power outage
   4. Someone unplugs the cable 
   
Fix : Add Redundancy to the system at all levels nodes, DB, caches etc

##### Software Faults
   1. Bugs in software
   2. Incorrect data populated from upstream

Symptom : Usually correlated and leads to cascading failures
Fix : No easy solution, It helps to,
- Test thoroughly
- Build a system which crashes and comes back up ( Self healing)
- Measure, Monitoring and Alerting

##### Human Errors
Similar to software issues
Fix : No easy solution, It helps to,
- Minimise opportunities of errors by adding guidelines, canary deployments, PR reviews, tests
- Test thoroughly
- Build a system which crashes and comes back up ( Self healing)
- Measure, Monitoring and Alerting

#### Scalability
As system grows ( in terms of data volume, complexity or traffic) there should be a reasonable way of dealing with growth

##### Measure the current load of the system

Load parameters for different levels,
1. Web server : request per second
2. Batch processing : throughput
3. DB : Ratio of reads vs writes
4. cache : hit ratio
5. chat room : number of active users
6. API : success rate

Example : twitter ( X)
post a tweet : Write heavy 
RPS
Average : 5k/s
Peak : 12k/s

Home timeline  : Read heavy
RPS
300k requests/ s

##### Investigate what happens when load increases
1. Increase load and keep resources same ( CPU, memory, n/w bandwidth), see how system performs
2. Increase load and see how much additional resources are needed

##### Measure performance :
Use percentile
- Mediian / p50 : half way point
- 95th percentile / p95 :  95% of requests faster than thresold
- 99th percentile / p99 :  99% of requests faster than thresold
- 99.9th percentile / p999 :  99.9% of requests faster than thresold

##### Fix 

###### Scaling up/ Vertical scaling
Get heavy resources servers, there are limits
###### Scaling out/  horizontal scaling
Add more servers also known as shared nothing architecture
It could be autscaling ( elastic scaling) or manual scaling

#### Maintainability

##### Operability
Ease of operations users
- Monitor health
- Track problems and causes of problems
- Capacity problems
- define processes that make system predictable
- maintain security
- Config management and more
- Allow ops users to view, track and update system behavior

#### Simplicity
How simple it is for a new engineer to understand the system
- Explosion of state spaces
- Tight coupling of modules
- Tangled dependency
- Inconsistent naming
- Workaround issues

Fix : Abstraction

#### Evolability
Making future changes or feature enhancements easy

- New user requests, new features
- New platform replaces old
- Growth
- Legal requirements

### Data model and query languages

#### SQL (Relational Databases)
- Strongly typed, schema-first design 
- Enforces data integrity via constraints 
- Normalization reduces redundancy 
- Relationships handled via joins

Implication:
- Excellent for systems of record 
- Harder to evolve schema at scale without coordination

Guarantee : ACID Guarantees

Scaling :
Primarily vertical scaling, horizontal scaliang needs partitioning and replication

#### NoSQL (Non-Relational Databases)
Schema-flexible or schema-on-read 
Data modeled around access patterns 
Denormalization is common

Implication:

- Faster iteration and evolution 
- Risk of data inconsistency without discipline

Guarantee : BASE Model

Scaling :
Primarily horizontal scaling, native support for
- Sharding
- Distributed clusters
- Geo distribution

##### Document Stores
Example: MongoDB, rethink db, couch db, Espresso ( linkedIn)
- JSON-like documents
- Best for hierarchical, evolving data
- Good for one to many relationship
- MongoDb uses Map Reduce to perform read only queries across documents

Use Case:
- Product catalogs
- User profiles
- Content systems

##### Key-Value Stores
Example: Redis, DynamoDB
- O(1) lookup

Use Case:
- Caching layer
- Session stores
- Rate limiting

##### Wide-Column Stores
Example: Cassandra, HBase
- Column-family storage
- Optimized for write-heavy workloads

Use Case:
- Time-series data
- Logging
- Analytics pipelines

##### Graph Databases
Example: Neo4j
- Relationship-first modeling
- Good for many to many relationship

Query language 
1. Property graphs ( tables of veritices and edges with index on edge, tail_vertex and head_vertex)
2. Cypher query language 
   1. Neo4j
   2. Ex : (Miami) :WINTIN_IN (USA)
      1. (LUCY) :BORN_IN (MIAMI)
      2. Query : MATCH (person) :BORN_IN (US: location) RETURN person.name
3. Triple stores and SPARQL ( 
   1. info stoed in 3 parts statements
   2. subject, predicate, object 
   3. ex : Jim likes bananas

Use Case:
- Social graphs
- Fraud detection
- Recommendation engines


| Feature | SQL | NoSQL |
|--------|-----|------|
| Schema | Fixed | Flexible / Dynamic |
| Data type | Structured | Structured + Unstructured |
| Scaling | Vertical | Horizontal |
| Transactions | Strong (ACID) | Eventual consistency (BASE) |
| Query language | SQL | Varies (JSON, APIs, etc.) |
| Joins | Supported | Usually not |
| Query Flexibility	| High| Limited
| Operational Complexity | Lower initially | Higher (distributed systems)
| Data Integrity | Enforced | Application-managed |

### Serialization and Deserialization

### Storage and Retrieval
Write → Store data efficiently
Read  → Retrieve data efficiently

tradeoffs between reads vs writes vs space.
#### Two Fundamental Storage Approaches
##### Log-Structured Storage (Append-only)
Write → append to file

```
set x = 1
set x = 2
set x = 3

stored as 
x=1
x=2
x=3  ← latest value
```

Problem : files grow quickly

Solution : Compaction
```
Before:
x=1, x=2, x=3

After:
x=3
```

Real systems:
RocksDB
Apache Cassandra

Key advantages:
Sequential disk writes → very fast
Crash-safe (append-only)

Key drawback:
Reads are harder (need to find latest value)

##### Update-in-place (B-Tree style)
Find location → overwrite

Real systems:
MySQL
PostgreSQL

Tradeoff:
Reads → fast
Writes → slower (random disk I/O)

#### Hash Indexes
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


#### SSTable ( Sorted String Table) and LSM ( Log Structured Merge) tree

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


#### B Tree


#### LSM vs B-Tree vs Hash index


| Feature                   | Hash Index              | LSM Tree                    | B-Tree              |
| ------------------------- | ----------------------- | --------------------------- | ------------------- |
| Point Lookups             | Excellent               | Good                        | Excellent           |
| Writes                    | Very fast               | Very fast                   | Moderate            |
| Reads                     | Excellent (exact match) | Good                        | Excellent           |
| Range Queries             | No                      | Good                        | Excellent           |
| Sequential Write Friendly | No                      | Yes                         | No                  |
| Compaction                | No                      | Required                    | No                  |
| Memory Dependence         | High                    | Moderate                    | Lower               |
| Typical Use               | Cache / KV              | Write-heavy distributed DBs | OLTP relational DBs |


## Distributed Data Concepts
### Replication
#### Leaders and followers
#### Multi-leader
#### Leaderless

### Partitioning
#### By key range
#### By Hash of key

### Transactions
#### ACID
#### BASE

### Consistency
#### Linerizability

### Distributed transactions
#### Two phase commit
#### Three phase commit
#### SAGA pattern

### Consensus
#### Paxos
#### Raft