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

### Serialization and Deserialization

### Storage and Retrieval
#### Hash Indexes
#### SSTable and LSM tree
#### B Tree

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