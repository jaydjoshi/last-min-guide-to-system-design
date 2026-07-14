# Partitioning

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