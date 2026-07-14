# B Tree


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