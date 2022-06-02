# Last minute guide to System Design

## Index

1. Designing Data intensive applications
   1. Foundation of Data systems
       - Key characteristics of Systems.
           - Reliability
           - Scalability
           - Maintainability
       - Data model and query languages
       - Serialization and Deserialization
       - Storage and Retrieval
         - Hash Indexes
         - SSTable and LSM tree
         - B Tree
   2. Distributed Data
      - Replication
        - Leaders and followers
        - Multi-leader
        - Leaderless
      - Partitioning
        - By key range
        - By Hash of key
      - Transactions
        - ACID
        - BASE
      - Consistency
        - Linerizability
      - Distributed transactions
        - 2 PC
        - 3 PC
      - Consensus
        - Paxos
        - Raft

2. Other Building blocks of System Design
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
   
3. Important theorems/ short forms
     - CAP Theorem
     - Cube theorem
     - ACID and BASE
     
4. How to approach System Design

5. Memoize these topics!
   - Power of Two
   - Latency numbers every programmer should know
   - Availability numbers

6. How does famous Systems work?
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

7. System Design questions with solutions
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
