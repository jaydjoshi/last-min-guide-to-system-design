# Core Concepts

This directory contains the reusable building blocks used throughout system design interviews and real-world distributed systems. The topics are organized in a recommended learning order, starting from first principles and progressing to distributed systems concepts.

---

## 📚 Learning Roadmap

### 1. Fundamentals

Understand the core qualities of well-designed systems.

* [Reliability](01-fundamentals/reliability.md)
* [Scalability](01-fundamentals/scalability.md)
* [Maintainability](01-fundamentals/maintainability.md)

---

### 2. System Design Framework

Learn a structured approach for solving system design interviews.

* [System Design Interview Framework](02-framework/system-design-interview-framework.md)

Topics covered:

* Requirement Gathering
* Functional Requirements
* Non-functional Requirements
* Capacity Estimation
* High-Level Design
* Deep Dive
* Bottlenecks & Trade-offs

---

### 3. Storage

Learn how modern databases store and retrieve data efficiently.

* [Data Models](03-storage/data-models.md)
* [Hash Index](03-storage/hash-index.md)
* [LSM Tree](03-storage/lsm-tree.md)
* [B-Tree](03-storage/b-tree.md)
* [Storage Engine Comparison](03-storage/storage-engine-comparison.md)

Topics include:

* SQL vs NoSQL
* Document, Key-Value, Wide Column & Graph Databases
* WAL
* MemTables
* SSTables
* Compaction
* Bloom Filters
* Clustered & Secondary Indexes

---

### 4. Encoding

Learn how data is serialized and evolves across distributed services.

* [Serialization & Schema Evolution](04-encoding/serialization-and-schema-evolution.md)

Topics include:

* JSON
* Protocol Buffers
* Avro
* Forward & Backward Compatibility
* Schema Evolution

---

### 5. Distributed Data

Understand how data is replicated and partitioned across multiple machines.

* [Replication](05-distributed-data/replication.md)
* [Partitioning](05-distributed-data/partitioning.md)

Topics include:

* Leader-Follower Replication
* Multi-Leader Replication
* Leaderless Replication
* Quorum Reads/Writes
* Read Repair
* Hinted Handoff
* Range & Hash Partitioning
* Hotspots
* Rebalancing

---

### 6. Transactions

Learn how databases maintain correctness during concurrent operations.

* [Transactions & ACID](06-transactions/transactions-and-acid.md)
* [Isolation Levels](06-transactions/isolation-levels.md)
* [Distributed Transactions](06-transactions/distributed-transactions.md)

Topics include:

* ACID Properties
* Concurrency Anomalies
* MVCC
* Snapshot Isolation
* Serializable Isolation
* Two-Phase Commit
* Three-Phase Commit
* Saga Pattern

---

### 7. Consistency

Understand how distributed systems agree on a single state.

* [Consensus](07-consistency/consensus.md)

Topics include:

* Consensus
* Quorums
* Leader Election
* Split Brain
* Raft

---

### 8. Distributed Systems

Study the challenges that arise once applications run across multiple machines.

* [Distributed System Failures](08-distributed-systems/distributed-system-failures.md)

Topics include:

* Partial Failures
* Time & Clocks
* Network Partitions
* Failure Detection
* Distributed Coordination

---

### 9. Interview Reference

Quick reference material for interviews.

* [Power of Two](09-interview-reference/power-of-two.md)
* [Latency Cheatsheet](09-interview-reference/latency-cheatsheet.md)
* [Availability Cheatsheet](09-interview-reference/availability-cheatsheet.md)

---

# Recommended Reading Order

If you're new to System Design, follow this order:

1. Fundamentals
2. System Design Framework
3. Storage
4. Encoding
5. Distributed Data
6. Transactions
7. Consistency
8. Distributed Systems
9. Interview Reference

---

# Related Repository Sections

The concepts in this directory are applied in the real-world examples under the `designs/` directory, where you'll find complete system design walkthroughs for services such as payment systems, digital wallets, fraud detection, nearby friends, stock exchanges, monitoring systems, and more.
