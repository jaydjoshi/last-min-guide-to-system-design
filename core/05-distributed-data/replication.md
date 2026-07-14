# Replication

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