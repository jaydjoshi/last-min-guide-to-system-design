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

##### Sloppy Quorums

Writes may go to temporary nodes.

Can weaken guarantees.

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