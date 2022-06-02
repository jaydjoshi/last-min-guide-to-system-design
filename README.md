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
     - CAP Theorem
     - Cube theorem
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
#### Scalability
#### Maintainability

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
#### SAGA

### Consensus
#### Paxos
#### Raft