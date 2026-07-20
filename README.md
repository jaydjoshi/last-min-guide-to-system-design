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

8. System Design questions with solutions()
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


#### B Tree

**B-Tree (more commonly B+ Tree in databases)** is a self-balancing tree structure optimized for disk access.

Core philosophy:

> Keep data organized at all times.

Unlike LSM Trees that write first and organize later, B-Trees maintain sorted structure during every write.

Used in:

* MySQL (InnoDB)
* PostgreSQL
* SQLite
* Oracle

---

# Core Components

```text
Root Node
Internal Nodes
Leaf Nodes
Pages (Disk Blocks)
Buffer Pool
Write Ahead Log (WAL)
Branching factor : Number of branches a node chan have
```

---

##### High-Level Structure

```text
                  Root Page
                     |
         ----------------------------
         |                          |
     Internal Node             Internal Node
         |                          |
      Leaf Pages  <---- linked ----> Leaf Pages
```

Each node stores:

```text
[keys]
[child pointers]
```

Leaf pages store:

* Data rows (clustered)
  OR
* Row pointers (secondary index)

---

##### Why "Tree" but Optimized for Disk?

Each node is sized to match a disk page:

```text
4KB / 8KB / 16KB pages
```

A page can hold many keys.

That keeps tree height small.

Example:

```text
Millions of rows
Often only 3–4 levels deep
Ex : 500 branching factor 4kb pages and 4 level of tree can store 256 TB of data
```

Very few disk reads.

---

# Read Path

```text
                     SEARCH
                       |
                       v
                  Root Page
                       |
               Binary search in node
                       |
                       v
                Follow child pointer
                       |
                     repeat
                       |
                       v
                   Leaf Page
```

Each level:

```text
1 page read
```

Overall:

```text
O(log n)
```

---

##### Example Lookup

Suppose:

```text
Find key = 42
```

Tree:

```text
            [50]
           /    \
      [20,30]  [70,90]
```

42 < 50

Go left.

Find correct leaf.

---

##### Write Path

Insert:

```text
1. Find correct leaf
2. Insert key
3. Split page if full
4. Propagate split upward if needed
```

---

##### Page Split

Before:

```text
[10,20,30,40]
```

Insert 25:

```text
[10,20]  [25,30,40]
```

Middle key promoted upward.

Tree remains balanced.

---

##### B+ Trees (What Real Databases Use)

Most databases use **B+ Trees**, not textbook B-Trees.

Difference:

Internal nodes:

* Keys only

Leaf nodes:

* Actual data or row pointers

Leaves linked:

```text
[10,20] -> [30,40] -> [50,60]
```

Critical for range scans.

---

##### Range Queries

Works naturally:

```sql
WHERE age BETWEEN 20 AND 40
```

Find first key.

Then scan linked leaves.

Very efficient.

---

##### Buffer Pool (Huge Optimization)

Pages are cached in RAM.

```text
Disk Page -> Buffer Pool
```

Hot pages often never hit disk.

Important for performance.

---

##### Write Ahead Log (WAL)

Even B-Trees use WAL.

Write flow:

```text
Update log first
Then update page
```

Provides:

* Crash safety
* Recovery
* Durability

---

##### Clustered vs Secondary Index

###### Clustered Index

Index IS the table.

Rows stored in key order.

Example:

Primary key index in InnoDB.

---

###### Secondary Index

Stores:

```text
secondary key -> row pointer
```

Requires extra lookup.

---

##### Covering Indexes

Sometimes index stores enough data to answer query directly.

```sql
SELECT name FROM users WHERE id=5
```

Can avoid table lookup.

Huge optimization.

---

##### Strengths

###### Excellent Point Lookups

```text
O(log n)
```

---

###### Excellent Range Queries

Because sorted.

---

###### Strong Read Performance

Often better than LSM.

---

##### Tradeoffs

###### Random Writes

Updates touch random pages.

Slower than sequential LSM writes.

---

###### Page Splits

Can cause write amplification.

---

###### Fragmentation

Over time may require reorganization.

---

##### Concurrency (Very Important)

Real databases add:

* Latches on pages
* Row locks
* MVCC

Readers and writers can coexist.

---

##### When B-Trees Win

Use B-Trees for:

* OLTP systems
* Banking
* Relational databases
* Read-heavy workloads
* Ordered access

---

##### When They Struggle

Less ideal for:

* Massive write-heavy ingestion
* Time-series at huge scale
* Append-heavy distributed systems

LSM often wins there.

---

##### Mental Model

```text
Keep keys sorted
Traverse few pages
Split when full
Stay balanced always
```

That is a B-Tree.

---

##### One-Line Summary

> A B-Tree keeps data sorted in balanced disk pages so reads, writes, and range scans all remain efficient with logarithmic lookup cost.

```text
Hash Index  -> jump directly by hash
B-Tree      -> navigate sorted pages
LSM Tree    -> write sequentially, merge later
```



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


### Encoding and Evolution

Encoding means:

```text
Transforming in-memory data into a format
that can be stored or transmitted.
```

Also called:

* Serialization
* Marshaling

Reverse process:

* Deserialization

---

#### Why Encoding Matters

Used whenever data crosses boundaries:

```text
Application -> Disk
Application -> Network
Service A -> Service B
Database -> Client
```

Goals:

* Compact representation
* Fast processing
* Compatibility over time

---

> Data formats must evolve without breaking systems.

This is schema evolution.

---

#### Major Encoding Types

##### 1. Language-Specific Encodings

Examples:

* Java serialization
* Python pickle

Problems:

* Language locked
* Poor interoperability
* Security issues

Usually avoided.

---

##### 2. Text Formats

Examples:

* JSON
* XML
* CSV

Pros:

* Human readable
* Flexible
* Widely supported

Cons:

* Larger payloads
* Weak typing

---

##### 3. Binary Formats

Examples:

* Avro
* Protocol Buffers
* Thrift

Pros:

* Smaller
* Faster
* Strong schemas

Used heavily in distributed systems.

---

#### Schema Evolution

Systems change.

Fields get added.

Schemas evolve.

Need compatibility.

---

##### Backward Compatibility

New code can read old data.

---

##### Forward Compatibility

Old code can read newer data.

---

Both matter.

---

#### How Compatibility Works

Typical rule:

```text
Unknown fields are ignored.
```

Example:

Old schema:

```text
name
age
```

New schema:

```text
name
age
email
```

Old consumers ignore email.

System still works.

---

#### Avro vs Protocol Buffers (High Level)

##### Protocol Buffers / Thrift

Schema IDs on fields.

```text
1 -> name
2 -> age
```

Supports evolution via field numbering.

---

##### Avro

Writer schema + reader schema.

Schemas negotiated during read.

Very flexible.

Popular in data pipelines.

---

#### Dataflow Between Processes

Chapter also covers how encoded data moves.

Three patterns:

---

##### Database Writes

Application writes encoded records.

Later readers may use different schema versions.

Schema evolution required.

---

##### Service Calls (RPC)

```text
Service A <-> Service B
```

Requests and responses serialized.

Versioning matters.

---

##### Message Passing

Events often encoded.

Example:

```text
OrderCreated event
```

Consumers may run different versions.

Compatibility critical.

---

#### Why This Matters in Distributed Systems

Services rarely upgrade simultaneously.

Reality:

```text
Old producers
New consumers
Mixed versions everywhere
```

Encodings must tolerate this.

---

#### Tradeoffs

| Format   | Human Readable | Compact | Schema Support |
| -------- | -------------- | ------- | -------------- |
| JSON     | Yes            | No      | Weak           |
| Protobuf | No             | Yes     | Strong         |
| Avro     | No             | Yes     | Strong         |

---

#### Real System Uses

##### JSON

* REST APIs
* External interfaces

---

##### Protobuf

* gRPC
* Internal services

---

##### Avro

* Kafka pipelines
* Data platforms

---

#### Mental Models

##### Encoding

```text
Objects -> Bytes
```

---

##### Schema Evolution

```text
Change without breaking readers
```

---

##### Compatibility

```text
Old and new versions coexist
```

#### One-Line Summary

> Encoding turns data into bytes for storage or communication, while schema evolution ensures those formats can change over time without breaking distributed systems.


## Distributed Data Concepts
### Replication

Replication means:

```text
Maintaining copies of the same data on multiple machines.
```

Goals:

* High availability
* Fault tolerance
* Lower read latency
* Scalability

Core idea:

> If one node fails, data should still be available.

---

#### Why Replicate Data?

##### 1. Reliability

```text
Primary crashes
Replica can take over
```

---

##### 2. Read Scaling

```text
Many readers
Spread reads across replicas
```

---

##### 3. Geographic Locality

Users read from nearby replicas.

Lower latency.

---

#### Core Replication Models

```text
1. Leader-Follower
2. Multi-Leader
3. Leaderless
```

---

#### 1. Leader-Follower Replication

Also called:

* Primary-replica
* Master-slave (older term)

---

##### Architecture

```text
                 Write
Client ------------------> Leader
                              |
                     Replication Log
                              |
                --------------------------
                |                        |
                v                        v
             Follower1                Follower2

Reads may go to replicas
```

---

##### How It Works

Writes:

```text
All writes go to leader
```

Leader records changes.

Followers replay those changes.

---

##### Replication Log Options

###### Statement-based

Replicate SQL statements.

```sql
UPDATE users SET age=30 WHERE id=1;
```

Problems:

* Non-deterministic behavior

---

##### Write-ahead log shipping

Ship physical WAL records.

Used in PostgreSQL.

---

##### Logical replication

Replicate row-level changes.

More flexible.

---

#### Synchronous vs Asynchronous Replication

##### Synchronous

```text
Write acknowledged
only after replica confirms
```

Pros:

* Stronger durability

Cons:

* Higher latency

---

##### Asynchronous

```text
Leader returns immediately
Replica catches up later
```

Pros:

* Fast writes

Cons:

* Replication lag

---

#### Replication Lag

Major concept.

```text
Write -> Leader updated
Read -> Replica still stale
```

---

##### Problems Caused

###### Read-your-writes violation

User posts message.

Immediately refreshes.

Post missing.

---

###### Monotonic read violation

User sees newer data.
Then older data.

Confusing.

---

##### Common Solutions

* Read from leader after writes
* Sticky sessions
* Route user to same replica

---

#### Failover

If leader dies:

```text
Promote replica to new leader
```

---

##### Problems

###### Split Brain

Two leaders elected.

Data divergence.

Bad.

---

##### Failover can be:

* Manual
* Automatic

Often uses consensus systems.

---

#### Leader-Follower Strengths

Good for:

* Read-heavy systems
* Traditional OLTP
* Simpler replication model

---

#### 2. Multi-Leader Replication

Now multiple leaders accept writes.

---

##### Architecture

```text
Region A Leader  <------> Region B Leader
      |                         |
  Local replicas             Local replicas
```

Each leader replicates to others.

---

#### Use Cases

##### Multi-region writes

Users write locally.

---

##### Offline-first apps

Mobile sync.

---

##### Collaborative systems

Multiple concurrent writers.

---

#### Big Problem: Write Conflicts

Example:

Two leaders update same row.

```text
A writes x=5
B writes x=7
```

Conflict.

---

#### Conflict Resolution

##### Last Write Wins (LWW)

Use timestamps.

Simple.

Can lose data.

---

##### Application Resolution

App merges conflicts.

---

##### CRDT-style approaches ( Conflict-free Replicated Data Types)

Special mergeable structures.

---

#### Tradeoffs

Pros:

* Availability
* Geographic writes

Cons:

* Conflict complexity

---

#### 3. Leaderless Replication

Used by:

* Cassandra
* Dynamo-style systems

---

##### Architecture

```text
Client writes directly to multiple replicas
```

Example:

```text
          Write
            |
     -----------------
     |       |       |
     v       v       v
    N1      N2      N3
```

No single leader.

---

#### Quorum Concept ( IMP)

Suppose:

```text
N = 3 replicas
W = 2 writes required
R = 2 reads required
```

Rule:

```text
R + W > N
```

Can ensure overlap.

---

#### Example

Write succeeds after 2 replicas.

Read checks 2 replicas.

Latest version can be found.

---

#### Read Repair

If one replica stale:

```text
Read discovers stale replica
Repair it
```

---

#### Anti-Entropy Repair

Background replica synchronization.

---

#### Strengths

Very available.

No single leader.

---

#### Challenges

##### Sloppy Quorums and Hinted Handoff

Writes may go to temporary nodes.

Can weaken guarantees.

When real node comes up, data is tranferred  from temporray node to real node. ( Hinted handoff)

---

##### Eventual Consistency

Often not strongly consistent.

---

#### Consistency vs Availability Tradeoff

Huge chapter theme.

```text
More consistency -> less availability
More availability -> weaker consistency
```

---

#### Compare Replication Models

| Feature            | Leader-Follower | Multi-Leader | Leaderless |
| ------------------ | --------------- | ------------ | ---------- |
| Write Simplicity   | High            | Medium       | Medium     |
| Read Scaling       | High            | High         | High       |
| Conflict Risk      | Low             | High         | Medium     |
| Availability       | Good            | Better       | Very High  |
| Strong Consistency | Easier          | Harder       | Hard       |

---

#### Common Failure Scenarios

##### Replica Lag

Stale reads.

---

##### Network Partition

Nodes cannot communicate.

---

##### Leader Crash

Need failover.

---

##### Divergent Replicas

Conflicting data versions.

---

#### Mental Models

##### Leader-Follower

```text
One writer
Many readers
```

---

##### Multi-Leader

```text
Many writers
Must resolve conflicts
```

---

##### Leaderless

```text
Replicas vote via quorum
```

---

#### Real Systems Mapping

##### MySQL Read Replicas

Leader-Follower.

---

##### PostgreSQL Streaming Replication

Leader-Follower.

---

##### Cassandra

Leaderless.

---

##### Dynamo-style Systems

Leaderless.

---

#### When to Use What?

##### Leader-Follower

Use when:

* Most writes centralized
* Read scaling needed
* Simplicity matters

---

##### Multi-Leader

Use when:

* Multi-region writes
* Offline sync
* Collaborative editing

---

##### Leaderless

Use when:

* Extreme availability
* Distributed KV stores
* Eventual consistency acceptable

---

#### One-Line Summary

> Replication keeps multiple copies of data synchronized for availability and scalability, using leader-follower, multi-leader, or leaderless approaches—each trading off consistency, latency, and failure handling.


### Partitioning

Partitioning (sharding) means:

```text
Splitting a large dataset across multiple machines.
```

Instead of storing all data on one node:

```text
Machine 1 -> Part of data
Machine 2 -> Part of data
Machine 3 -> Part of data
```

---

#### Why Partition Data?

Goals:

* Scale beyond one machine
* Increase throughput
* Distribute storage
* Reduce hotspots

Core idea:

> Spread both data and load.

---

#### Big Challenge

Partitioning is easy.

Good partitioning is hard.

Goal:

```text
Balanced partitions
Balanced traffic
Balanced storage
```

---

#### Partitioning Strategies

##### 1. Key-Based Partitioning (Hash Partitioning)

Use hash of key.

```text
partition = hash(key) % N
```

Example:

```text
user123 -> shard 2
user456 -> shard 4
```

---

##### Benefits

Usually distributes load evenly.

---

##### Problem

Loses key ordering.

Range queries harder.

---

#### 2. Range Partitioning

Split by key ranges.

Example:

```text
A-M -> Shard 1
N-Z -> Shard 2
```

Or:

```text
1-1M users -> Shard1
1M-2M -> Shard2
```

---

##### Benefits

Good for:

* Range scans
* Ordered data

---

##### Problem

Can create hotspots.

Example:

Recent timestamps all hit one shard.

---

#### Hotspots

Major chapter theme.

```text
One partition overloaded
Others idle
```

Bad.

---

##### Example

Celebrity account traffic.

All requests hit one shard.

---

##### Mitigations

* Better keys
* Add Random suffixes to celebrity keys and partition those keys. CLient merges all keys
* Composite partition keys

---

#### Secondary Indexes and Partitioning

Hard problem.

---

##### Local Secondary Indexes

Each partition keeps own index.

```text
Index exists per shard
```

Simple.

But global queries touch many shards.

---

##### Global Secondary Indexes

Separate index spanning partitions.

More powerful.

More complex.

---

#### Rebalancing Partitions

When:

* Nodes added
* Nodes removed
* Data skew appears

Need repartitioning.

---

#### Naive Rehashing Problem

Adding one node may move huge data.

Expensive.

---

#### Consistent Hashing (High Level)

Maps nodes onto hash ring.

```text
Add node -> move limited data
```

Less disruption.

Used in distributed systems.

---

#### Dynamic Partitioning

Systems may split partitions automatically.

```text
Large shard
   ↓
Split into two shards
```

Common approach.

---

#### Request Routing

How client finds right partition?

Three models:

---

##### 1. Client Knows Mapping

Client routes directly.

---

##### 2. Routing Tier

```text
Client -> Router -> Correct shard
```
Like zookeeper

---

## 3. Database Handles It

Client sends anywhere.

Cluster routes internally.

Common protocol : Gossip protocol

---

# Partitioning + Replication

Usually combined.

```text
Each shard may also have replicas.
```

Example:

```text
Shard 1:
 Leader
 Replicas

Shard 2:
 Leader
 Replicas
```

This is common real-world design.

---

# Compare Partitioning Approaches

| Feature           | Hash Partitioning | Range Partitioning |
| ----------------- | ----------------- | ------------------ |
| Even Distribution | Good              | Can skew           |
| Range Queries     | Poor              | Excellent          |
| Hotspot Risk      | Lower             | Higher             |
| Ordering          | No                | Yes                |

---

#### Common Failure Modes

##### Data Skew

Some shards hold much more data.

---

##### Load Skew

Some shards get much more traffic.

---

##### Hot Partitions

Single partition overloaded.

---

#### Mental Models

##### Hash Partitioning

```text
Spread keys evenly
```

---

##### Range Partitioning

```text
Preserve order
```

---

##### Rebalancing

```text
Move data without huge disruption
```

---

#### Real System Examples

##### Cassandra

Partition by hashed partition key.

---

##### HBase

Range partitioning.

---

##### Dynamo-style systems

Consistent hashing.

#### Choosing Partition Keys

Critical decision.

Good partition key:

* High cardinality
* Evenly distributed
* Matches access pattern

Bad key:

* Causes hotspots
* Skews load

---

#### When to Use What?

##### Hash Partitioning

Use when:

* Uniform load important
* Key lookups dominate

---

##### Range Partitioning

Use when:

* Ordered access important
* Range scans common

---

#### One-Line Summary

> Partitioning scales databases by distributing data across machines using hash or range strategies, while managing hotspots, rebalancing, routing, and interaction with replication.


### Transactions
A transaction is:

```text
A group of operations that execute
as one logical unit.
```

Core idea:

> Either all operations happen, or none do.

---

#### Why Transactions Exist

They simplify correctness.

Protect against:

* Partial failures
* Concurrency bugs
* Data corruption

---

#### ACID Properties

```text
Atomicity
Consistency
Isolation
Durability
```

---

##### Atomicity

All-or-nothing.

Example:

Bank transfer:

```text
Debit A
Credit B
```

If second step fails:

Rollback.

---

##### Consistency

Transaction preserves database invariants.

Example:

```text
Balance cannot go negative
```

---

##### Isolation

Concurrent transactions should not interfere.

---

##### Durability

Committed data survives crashes.

Typically via WAL.

---

#### Isolation Problems (Major Theme)

Without proper isolation:

Bad things happen.

---

##### Dirty Reads

Transaction reads uncommitted data.

Bad.

---

##### Dirty Writes

Two transactions overwrite each other.

Bad.

---

##### Read Skew

A transaction sees part old state and part new state.

```text
Acc1 : $500
Acc2 : $500
Total balance : $1000

Transfer from Acc1 to Acc2
Mid way
Acc1 : $400
Acc2 : $500 ( Credit did not happen yet, it is in progress)
Total balance in another transaction : $900

```

---

##### Lost Updates

Two writes.
One disappears.

---

##### Write Skew

Each write looks safe alone
Unsafe together

```text
Doctor on call , Atleast 1 doctor must be on call.

Doc1 on call = true
Doc2 on call = true

In 2 transactions, both read data for doct1 and doc2 and decide to take break since 1 will be  on call
Doc1 on call = false ( takes break)
Doc2 on call = false ( takes break)

None are on call
```

---

#### Isolation Levels

---

##### Read Committed

Prevents:

* Dirty reads
* Dirty writes

Common default.

###### Implementation Approach 1: Lock-Based

Writes

Writers hold write locks until commit.

```text
T1 updates row X
T1 holds write lock

T2 tries to write X
Blocked until T1 commits
```

Prevents dirty writes.

---
Reads

Transactions can only see committed data.

```text
Committed balance = 1000
Uncommitted update = 500

Reader sees:
1000
```

Never sees uncommitted 500.

---

###### Implementation Approach 2: MVCC (Multi version concurrency control)

Used in PostgreSQL and MySQL.

Each **statement** gets its own snapshot.

```text
Every query sees committed data
at statement start.
```

---

 Example

```text
T1:
SELECT balance -> 100

T2:
UPDATE balance=200
COMMIT

T1:
SELECT balance -> 200
```

Result changed inside transaction.

Allowed under Read Committed.

---

##### Snapshot Isolation

Transactions read consistent snapshot.

Prevents:

* Read skew
  n- Some anomalies

Often implemented with MVCC.

###### Implementation: MVCC

Rows keep versions.

```text
Row X:

V1 created by txn10
V2 created by txn22
```

Each transaction gets:

```text
snapshot timestamp
```

---

Read Visibility

Suppose:

```text
T1 starts at time 100
```

T1 sees only versions committed before 100.

Later commits are invisible.

---

Reads

No read locks needed.

```text
Readers don't block writers
```

Huge concurrency benefit.

---

Writes

Typically use:

```text
First-committer-wins
```

If two transactions update same row:

```text
T1 writes A
T2 writes A
```

One gets aborted.

Prevents lost updates.



---

##### Serializable Isolation

Strongest level.

Guarantee:

```text
Concurrent execution behaves
like serial execution.
```

Gold standard.

###### Implementation Option A: Two-Phase Locking (2PL)

Lock Types

- Shared locks for reads
- Exclusive locks for writes

---

Rule

Growing phase:

Acquire locks

Shrinking phase:

Release after commit

```text
Acquire...Acquire...Commit...Release
```

That is two-phase locking.

---

Why It Works?

Conflicts force serial order.

---

Downsides

- Blocking
- Deadlocks
- Lower concurrency

---

###### Implementation Option B: Serializable Snapshot Isolation (SSI)

Used in PostgreSQL.

Uses MVCC plus anomaly detection.

---

 Dependency Tracking

Example:

```text
T1 reads A
T2 modifies A
```

Dependency:

```text
T1 -> T2
```

Build serialization graph.

---

Dangerous Cycle

```text
T1 -> T2
T2 -> T1
```

Cycle means anomaly possible.

Abort one transaction.

Prevents write skew.

---

---

#### Distributed Transactions

Harder.

Spans multiple systems.

---

##### Two-Phase Commit (2PC)

```text
Phase 1: Prepare
Phase 2: Commit
```

All participants agree.

---

##### Problem

Coordinator failure can cause trouble.

Expensive.

---

#### Tradeoff Theme

Huge chapter message:

```text
Strong correctness
vs
Performance and availability
```

---

#### Isolation Levels Comparison

| Level              | Prevents                  | Implementation
| ------------------ | ------------------------- | -----------------
| Read Committed     | Dirty reads/writes        | Locks or MVCC
| Snapshot Isolation | Read skew, many anomalies | MVCC + version visibility
| Serializable       | All anomalies             | 2PL ( Strict write and read locking) / SSI (MVCC + dependency graph)

---

#### Real System Examples

##### PostgreSQL

MVCC + Serializable Snapshot Isolation.

---

##### MySQL InnoDB

MVCC + locking.

---

##### Distributed Systems

Often avoid distributed transactions when possible.

Use events instead.


#### One-Line Summary

> Transactions provide correctness under failures and concurrency through ACID guarantees, isolation levels, MVCC, and serializability, trading stronger guarantees against performance and complexity.

### The Trouble with Distributed Systems


```text
Distributed systems fail partially,
not completely.
```

Single machines usually:

- Work
- Crash

Distributed systems can be in weird intermediate states.

---

#### Partial Failures

```text
Some nodes work
Some don't
Network may lie
```

Hardest distributed systems problem.

---

#### Unreliable Networks

Messages may:

- Be delayed
- Be lost
- Arrive twice
- Arrive out of order

---

##### Timeout Problem

If a request times out:

```text
Did it fail?

Or was it just slow?
```

You often cannot know.

Major chapter insight.

---

#### Network Partitions

Nodes may stop communicating.

```text
Node A can't see Node B
```

System may split.

Now you must choose:

- Wait
- Keep serving requests
- Risk inconsistency

---

#### Unreliable Clocks

Physical clocks can lie.

Problems:

- Clock drift
- Clock skew
- NTP failures
- Leap seconds

---

##### Rule

```text
Never trust wall-clock time
for correctness.
```

Use ordering, not timestamps, for correctness.

---

#### Process Pauses

Machines can appear dead but are not.

Examples:

- Garbage collection pause
- VM freeze
- Scheduler delays

Can trigger false failure detection.

---

#### Failure Detection Is Uncertain

Failures are inferred, not observed.

Typically using:

- Timeouts
- Heartbeats
- Quorums

But these are guesses.

---

#### Local Knowledge Can Be Wrong

A node may believe:

```text
I am leader
```

And be wrong.

Important mental shift:

```text
Local knowledge may be false
```

---

#### System Models

##### Synchronous

Bounded delays.

Mostly unrealistic.

---

##### Asynchronous

No timing guarantees.

Closer to reality.

---

##### Reality

Usually somewhere messy in between.

---

#### Logical Time

Use ordering rather than physical clocks.

Examples:

- Lamport timestamps
- Version vectors

Helps reason about causality.

---

#### Byzantine Faults (High Level)

Nodes may behave arbitrarily or maliciously.

Separate, harder fault model.

---

#### Core Mental Models

```text
Networks are unreliable

Clocks are unreliable

Failure detection is uncertain
```


#### One-Line Summary

> Distributed systems are hard because networks, clocks, and failure detection are unreliable, so systems must be designed assuming uncertainty and partial failure.

### Consistency and Consensus 

```text
How do multiple machines agree
despite failures?
```

That is consensus.

---

#### What is Consensus?

Consensus means:

```text
Multiple nodes agree on one decision.
```

Examples:

- Who is leader?
- What value gets committed?
- What is the correct order of operations?

---

#### Why Consensus Matters

Needed for:

- Leader election
- Distributed locks
- Metadata coordination
- Configuration changes
- Fault-tolerant replication

---

#### Core Requirements

Consensus needs:

##### Agreement

```text
All healthy nodes agree
on same decision.
```

---

##### Safety

```text
Never make conflicting decisions.
```

Example:

Never two leaders for same term.

---

##### Liveness

```text
Eventually make progress.
```

---

#### Quorums

Suppose:

```text
5 nodes
Majority = 3
```

Need majority agreement.

Key idea:

```text
Majorities overlap.
```

This preserves safety.

---

#### Leader-Based Consensus

Common model:

```text
Leader proposes decisions

Followers replicate log

Quorum commits them
```

---

#### Raft (High Level)

Three main parts:

---

##### 1. Leader Election

Nodes vote.

One becomes leader.

```text
Followers -> Candidates -> Leader
```

---

##### 2. Log Replication

Commands appended to replicated log.

```text
Write
 ↓
Leader log
 ↓
Followers copy
```

---

##### 3. Commit Rule

Entry committed after majority acknowledges.

---

#### Log as Source of Truth

Important idea:

```text
State machine = replayed log
```

Consensus often means agreeing on log order.

Not just agreeing on values.

---

#### Split Brain

Danger:

```text
Two nodes think they are leader.
```

Consensus prevents this.

---

#### Strong Consistency

Consensus often gives:

```text
Linearizable behavior
```

Feels like single machine.

Read after write sees latest value.

---

#### Safety vs Availability

Tradeoff:

```text
Prefer refusing progress

over returning wrong answers
```

Huge chapter theme.

---

#### Failure Handling

Consensus handles:

- Node crashes
- Delayed messages
- Network partitions
- Leader failures

---

#### Compare Concepts

| Concept | Purpose |
|--------|---------|
| Replication | Copy data |
| Quorum | Majority agreement |
| Consensus | Agree under failures |
| Raft | Protocol implementing consensus |

---

#### Real Systems

Uses consensus:

- etcd
- ZooKeeper
- Kafka metadata quorum
- Kubernetes control plane


# One-Line Summary

> Consensus lets distributed systems agree safely under failures using quorums and protocols like Raft, typically by agreeing on an ordered replicated log.

### Distributed transactions -  2PC, 3PC, and SAGA

A distributed transaction spans multiple services or databases.

Example:

```text
Order Service
Payment Service
Inventory Service
```

One business operation may touch all three.

Problem:

```text
How do we keep all systems consistent
if failures happen mid-operation?
```

Three classic approaches:

```text
1. Two-Phase Commit (2PC)
2. Three-Phase Commit (3PC)
3. Saga Pattern
```

---

#### 1. Two-Phase Commit (2PC)

##### Core Idea

All participants agree to commit before anyone commits.

Atomic commit across nodes.

```text
Either everyone commits
or everyone aborts
```

---

##### Roles

Two roles:

* Coordinator (Transaction Manager)
* Participants (Resource Managers)

Example:

```text
Coordinator
   |
----------------------
|         |          |
DB1      DB2       DB3
```

---

##### Two Phases

##### Phase 1 — Prepare (Voting)

Coordinator asks:

```text
Can you commit?
```

Sends:

```text
PREPARE
```

Participants:

* Execute transaction locally
* Write changes to log
* Lock resources
* Vote:

```text
YES
or
NO
```

---

###### Example

```text
Coordinator → Payment : PREPARE
Coordinator → Inventory : PREPARE

Payment → YES
Inventory → YES
```

All vote yes.

Move to phase 2.

---

##### Phase 2 — Commit

Coordinator decides:

```text
GLOBAL COMMIT
```

Sends commit to all.

```text
Coordinator -> Commit
Participants -> Commit locally
```

Done.

---

##### Abort Case

If even one votes NO:

```text
GLOBAL ABORT
```

Everyone rolls back.

---

#### 2PC Flow

```text
Begin Transaction
   ↓
Prepare Request
   ↓
All vote yes?
  /   \
No     Yes
 |      |
Abort  Commit
```

---

##### Why Logging Matters

Participants persist:

```text
Prepared state
```

If crash occurs:

Recovery can continue.

Critical for durability.

---

##### Strengths

* Atomicity
* Strong consistency
* Simple model conceptually

---

##### Problems with 2PC

##### Blocking Problem

Huge weakness.

Suppose:

```text
Everyone prepared
Coordinator crashes
```

Participants sit in:

```text
uncertain state
```

Cannot commit.
Cannot abort.

Blocked.

Classic problem.

---

##### Coordinator is Single Point of Failure

Even replicated coordinators are tricky.

---

##### Performance Cost

* Multiple network round trips
* Locks held longer
* Lower availability

---

##### Where Used

Traditional distributed databases:

* XA transactions
* Some RDBMS clusters

Less common in microservices.

---

#### 2. Three-Phase Commit (3PC)

Attempt to reduce blocking.

Adds extra phase.

---

##### Idea

Split commit into:

```text
1. CanCommit
2. PreCommit
3. DoCommit
```

---

##### Phase 1 — CanCommit

Like prepare.

Ask:

```text
Can you commit?
```

Participants vote.

---

##### Phase 2 — PreCommit

Coordinator says:

```text
Prepare to commit
```

Participants enter safe precommit state.

Important:

```text
No uncertainty about whether commit possible.
```

---

##### Phase 3 — DoCommit

Final commit.

```text
Commit now.
```

---

##### Flow

```text
CanCommit
  ↓
PreCommit
  ↓
DoCommit
```

---

##### Why It Helps

If coordinator fails after precommit,
participants can often decide safely.

Less blocking.

---

##### Problems

Still has issues:

* Assumes timing bounds
* Complicated
* Rarely used in practice

Most systems prefer consensus instead.

---

#### 2PC vs 3PC

| Feature        | 2PC      | 3PC     |
| -------------- | -------- | ------- |
| Phases         | 2        | 3       |
| Blocking Risk  | Yes      | Reduced |
| Complexity     | Lower    | Higher  |
| Real-world Use | Commoner | Rare    |

---

#### 3. Saga Pattern

Very different model.

Instead of one global atomic transaction:

```text
Sequence of local transactions
plus compensations
```

Huge idea.

---

##### Example

Order workflow:

```text
1 Create Order
2 Charge Payment
3 Reserve Inventory
```

---

Suppose step 3 fails.

Use compensations:

```text
Undo Payment
Cancel Order
```

---

##### Concept

Forward steps:

```text
T1 → T2 → T3
```

Compensations:

```text
C3 ← C2 ← C1
```

Reverse undo.

---

#### Orchestration Saga

Central coordinator drives flow.

```text
Saga Orchestrator
    |
Payment
Inventory
Shipping
```

Like workflow engine.

---

##### Example

```text
Order Created
 ↓
Charge Payment
 ↓
Reserve Inventory
 ↓
Ship
```

If reserve fails:

```text
Refund Payment
```

---

#### Choreography Saga

No central orchestrator.

Services react to events.

```text
OrderCreated event
  ↓
Payment processes
  ↓
Inventory reacts
```

Event-driven.

---

##### Pros

* Decentralized
* Looser coupling

---

##### Cons

* Harder debugging
* Complex event chains

---

#### Important Truth

Sagas are NOT atomic.

They provide:

```text
Eventual consistency
```

Very different from 2PC.

---

##### Compensation Is Hard

Compensating action is not always simple rollback.

Refunding payment ≠ erase payment.

Semantic undo.

Very important.

---

#### Saga Example Timeline

```text
T1 Create Order        Success
T2 Charge Card         Success
T3 Reserve Inventory   Fail

Compensate:
C2 Refund Card
C1 Cancel Order
```

---

#### When Sagas Work Well

Good for:

* Microservices
* Long-running workflows
* Business processes

Examples:

* Travel booking
* E-commerce order flow
* Payment workflows

---

#### When Sagas Are Bad

Bad when hard invariants needed.

Example:

* Core banking ledger

Use stronger transaction models.

---

#### Compare All Three

| Feature       | 2PC       | 3PC     | Saga          |
| ------------- | --------- | ------- | ------------- |
| Atomic Commit | Yes       | Yes     | No            |
| Blocking      | Yes       | Reduced | No            |
| Availability  | Lower     | Medium  | Higher        |
| Consistency   | Strong    | Strong  | Eventual      |
| Complexity    | Medium    | High    | High          |
| Typical Use   | Databases | Rare    | Microservices |

---

#### One-Line Summary

> 2PC provides atomic distributed commit through voting and coordinated commit, 3PC reduces blocking with an extra phase but is rarely used, while Sagas trade atomicity for availability by using sequences of local transactions with compensating actions.


## System Design Interview Vol. 1 & Vol. 2 - Categorized by Domain and Difficulty

### 1. Foundations & Core System Design Concepts

#### Easy
- Scale From Zero To Millions Of Users
  ![Image](./images/Zero to millions scaling.png)
- Back-of-the-envelope Estimation
  ##### Common Power-of-Two Conversions
| Unit | Value | 
|--------|--------|
| 1 KB | 10^3 bytes |
| 1 MB | 10^6 bytes |
| 1 GB | 10^9 bytes |
| 1 TB | 10^12 bytes |
| 1 PB | 10^15 bytes |

| Unit | Value | 
|--------|--------|
| 1 thousand | 10^3 |
| 1 million | 10^6 |
| 1 billion | 10^9 |
| 1 trillion | 10^12 |
| 1 Quadrillion | 10^15 |

##### Latency Numbers Every Engineer Should Know

| Operation | Time |
|------------|-------|
| L1 Cache Reference | 0.5 ns |
| Branch Mispredict | 5 ns |
| L2 Cache Reference | 7 ns |
| Mutex Lock/Unlock | 100 ns |
| Main Memory Reference | 100 ns |
| SSD Random Read | 150 μs |
| Disk Seek | 10 ms |
| Network Request (same datacenter) | 0.5 ms |
| Network Request (different datacenter) | 100 ms+ |

##### Key Takeaway

Memory is much faster than disk.

Memory/ L caches/ mutex seek : nano-seconds
SSD seek : micro-seconds
Disk seek : milli-seconds

```text
CPU Cache << RAM << SSD << HDD << Network
```

##### Availability Numbers

Availability is commonly measured using "nines".

| Availability | Downtime per Year |
|--------------|-------------------|
| 99% | 3.65 days |
| 99.9% | 8.76 hours |
| 99.99% | 52.6 minutes |
| 99.999% | 5.26 minutes |

Total secods in a day = 24 * 2600 = 86400 ~= 100,000

#### A Framework For System Design Interviews (IMP)

###### 1. Gather and Define Requirements

Ask Clarifying Questions
- Understand a similar system.
- Identify the key features to build.
- Understand the expected scale.

Define Functional Requirements
- List the main user flows.
- Map the required features to each flow.

Define Non-Functional Requirements
- List the non-functional requirements.
- Define the expected SLAs.

Do Back-of-the-Envelope Calculations
- Estimate storage requirements.
- Estimate read and write traffic.

---

###### 2. Propose a High-Level Design

1. Design a system that satisfies all functional requirements. Ignore scale and non-functional requirements at this stage.
2. Walk through each user flow and verify that all functional requirements are covered.
3. Identify the databases to use.
4. Define the APIs and events.
5. Define the database schema.
6. Refine the design to handle scale.

---

###### 3. Deep Dive

Pick the important non-functional requirements and discuss them in detail.

Availability
- Higher availability targets (for example, p999 to p9999).
- Multi-region and multi-AZ deployment.
- Technologies such as EKS and Aurora.

Reliability
- Handling software failures.
- Handling hardware failures.
- Handling human errors.

Scalability
- Horizontal scaling.

Observability
- Monitoring.
- Alerting.

Security
- DDos : Rate limiter
- Evesdropping and Man in middle attack : HTTPS and mTLS
- e2e encrytion

#### Medium
- Rate Limiter [Done]
  ![Image](designs/categories/01-foundations/medium/rate-limiter/RateLimiter.png)
Algo: Token bucket, Leaky bucket, Fixed window log, sliding window log, sliding window counter

- Consistent Hashing [Done]
  ![Image](designs/categories/01-foundations/medium/consistent-hashing/ConsistentHashing.png)

- Unique ID Generator in Distributed Systems [Done]
  ![Image](designs/categories/01-foundations/medium/unique-id-generator/IDGenerator.png)
64 bits ID by Twitter i.e. snowflake. 41 bits timestamp in milis, 10 bits machine + datacenter and 12 bits sequence

#### Medium
- Key-Value Store [Done]
  ![Image](designs/categories/01-foundations/hard/key-value-store/KeyValueStore.png)
Check like cassandra or redis
---

### 2. Storage & Data Infrastructure

#### Easy
- URL Shortener [ Done]
  ![Image](./images/URLGenerator.png)
Sequence to base 62 encoding ( 26+26+10 alphanumeric)

#### Medium
- Google Drive [ Done]
  ![Image](designs/categories/02-storage-and-data-infrastructure/medium/google-drive/GoogleDrive.png)
Resumable uploads

- S3-like Object Storage [ Done]
  ![Image](designs/categories/02-storage-and-data-infrastructure/medium/s3-like-bject-storage/S3LikeObjectStorage.png)
Resumable uploads, erasure coding

---

### 3. Search, Discovery & Information Retrieval

#### Easy
- Web Crawler [ Done]
  ![Image](designs/categories/03-search-discovery-information-retrieval/easy/web-crawler/WebCrawler.png)
Frontier URLs -> HTML downloader -> content parser -> URL fetcher -> URL seen -> repeat
#### Medium
- Search Autocomplete [ Done]
  ![Image](designs/categories/03-search-discovery-information-retrieval/medium/search-autocomplete/AutoComplete.png)
Trie DB
---

### 4. Communication & Messaging Systems

#### Easy
- Notification System [ Done]
  ![Image](designs/categories/04-communication-and-messaging-systems/easy/notification-system/NotificationSystem.png)
Adapters for FCM, APN, MAilChimp, twillio, gupshup

#### Medium
- Chat System [ Done]
  ![Image](designs/categories/04-communication-and-messaging-systems/medium/chat-system/ChatSystem.png)
Websocket

- Distributed Email Service [ Done]
  ![Image](designs/categories/04-communication-and-messaging-systems/medium/distributed-email-service/DistributedEmailSystem.png)
SMTP and IMAP

#### Hard
- Distributed Message Queue [ Done]
  ![Image](designs/categories/04-communication-and-messaging-systems/hard/distributed-message-queue/DistributedMessageQueue.png)
Kafka like queue, producer, Queue = [broker ( data storage, state storage), metadata storage and service discovery], consumers ( consumer groups)
---

### 5. Social Media & Consumer Internet

#### Medium
- News Feed System/ Twitter feed/ Instagram news feed [ Done]
  ![Image](designs/categories/05-social-media-and-consumer-internet/medium/news-feed-system/NewsFeedSystem.png)
fanout, push vs pull

#### Hard
- YouTube
  ![Image](designs/categories/05-social-media-and-consumer-internet/hard/youtube/Youtube.png)
Video uploader -> transcoder ( HLS/ DASH)
Streaming server -> CDN
---

### 6. Location & Geospatial Systems

#### Medium
- Proximity Service
  ![Image](designs/categories/06-location-and-geospatial-systems/medium/proximity-service/ProxityService.png)
Geohash ( Redis GEO = Geohash + sorted sets) / Quadtree  ( in memory)

#### Hard
- Nearby Friends
  ![Image](designs/categories/06-location-and-geospatial-systems/hard/nearby-friends/NearbyFriends.png)
GeoHash use RedisGeo ( GeoHash + SortedSet)

- Google Maps
  ![Image](designs/categories/06-location-and-geospatial-systems/hard/google-maps/GoogleMaps.png)
World map at 21 different zoom levels
Routing tile ( name = geohash, contains nodes and edges and link to otehr routing tiles)
Metadata
Flows, capture location of user and display navigation
---

### 7. Monitoring, Analytics & Advertising

#### Medium
- Metrics Monitoring
  ![Image](designs/categories/07-monitoring-analytics-and-advertising/medium/metrics-monitoring/MetricsMonitoringSystem.png)
Metrics collector agent -> queue -> ingestion svc -> aggregation svc -> alerts / dashboard
flows : ingestion, alerts and dashboard
TSDB like prometheus or InfluxDB
Key concept: TSDB ( InfluxDB or Prometheus)

#### Hard
- Ad Click Event Aggregation
  ![Image](designs/categories/07-monitoring-analytics-and-advertising/hard/ad-click-event-aggregation/AdClickEventAggregationSystem.png)
Ad click ingestion, aggregation, display
---

### 8. Gaming & Real-Time Ranking Systems

#### Medium
- Real-Time Gaming Leaderboard
  ![Image](designs/categories/08-gaming/medium/real-time-gaming-leaderboard/RealTimeGamingLeaderboard.png)
Ingestion, update leaderboard ( redis sorted sets)
---

### 9. Travel & Reservation Systems

#### Medium
- Hotel Reservation [Done]
  ![Image](designs/categories/09-travel-and-reservation-systems/medium/hotel-reservation/TravelBookingSystem.png)
Ensure double booking
---

### 10. Financial Systems (FinTech)

#### Medium
- Payment System
  ![Image](designs/categories/10-financial-systems/medium/payment-system/PaymentSystem.png)
Dist transactions, idempotency, security
#### Hard
- Digital Wallet
  ![Image](designs/categories/10-financial-systems/hard/digital-wallet/DigitalWallet.png)
Dist transactions, idempotency, security

- Stock Exchange
  ![Image](designs/categories/10-financial-systems/hard/stock-exchange/StockExchange.png)
Low latency, monolith, FIX, multicast, use mmap instead of queue

- Fraud detection system
  ![Image](designs/categories/10-financial-systems/hard/fraud-detection-system/FraudDetection.png)
Flows: detect fraud in real time ( feature store [user, device, merchant, location, card, ip], Rule engine [business experts adds rules], velocity checks ,
- ML prediction ( gives a probablity), Graph fraud detection, 
- Risk score aggregation
Rules : 30 + ML :40 + Velocity : 20 + Graph :25
= Total : 115 ( normalise to 0 to 100 and create a spectrum of approve, OTP, manual review, reject)
learn from historical frauds
types of frauds: card stolen, ID stolen, card testing, velocity fraud, location fraud, account takeover
