# Database Concurrency Anomalies & Isolation Levels (DDIA Cheat Sheet)

## 1. Dirty Read 🟤

**Definition:** Reading data written by another transaction that has **not yet committed**.

### Example

```text
T1:
Update Balance = 1000
(Not committed)

        ↓

T2:
Read Balance = 1000

        ↓

T1:
Rollback
```

T2 read data that never officially existed.

**Memory Trick:**
> **Dirty = Uncommitted data.**

---

## 2. Non-repeatable Read 🔵

**Definition:** Reading the **same row twice** within a transaction returns different values because another transaction committed an update.

### Example

```text
T1:
Read Balance = 100

        ↓

T2:
Update Balance = 200
Commit

        ↓

T1:
Read Balance = 200
```

**Memory Trick:**
> **Same row, different value.**

---

## 3. Read Skew 🟣

**Definition:** Reading different rows from **different snapshots**, resulting in an inconsistent view of the database.

### Example

Initial state:

```text
Account A = 100
Account B = 100
```

Money transfer:

```text
A = 50
B = 150
```

Transaction reads:

```text
A = 100
B = 150
```

This combination never actually existed.

**Memory Trick:**
> **Mixed snapshot.**

---

## 4. Phantom Read 👻

**Definition:** Repeating the same query returns a different **set of rows** because another transaction inserted or deleted matching rows.

### Example

```sql
SELECT * FROM Orders
WHERE amount > 1000;
```

Initially:

```text
5 rows
```

Another transaction inserts:

```text
Order(amount = 5000)
Commit
```

Running the same query again:

```text
6 rows
```

**Memory Trick:**
> **Ghost rows appeared.**

---

## 5. Lost Update 🟠

**Definition:** Two transactions update the same row concurrently, causing one update to be overwritten.

### Example

```text
Counter = 10

T1 reads 10
T2 reads 10

T1 writes 11
T2 writes 11
```

Expected:

```text
12
```

Actual:

```text
11
```

**Memory Trick:**
> **Someone's update disappeared.**

---

## 6. Dirty Write 🔴

**Definition:** One transaction overwrites another transaction's **uncommitted write**.

### Example

```text
T1:
Write Balance = 200
(Not committed)

        ↓

T2:
Write Balance = 300
Commit

        ↓

T1:
Rollback
```

This creates ambiguity about the correct final value.

**Memory Trick:**
> **Overwriting unfinished work.**

> **Note:** Almost all modern relational databases prevent Dirty Writes, regardless of the isolation level.

---

## 7. Write Skew 🟢

**Definition:** Two concurrent transactions modify **different rows** based on the same snapshot, violating a business rule.

### Example

Initial state:

```text
Alice = On Call
Bob   = On Call
```

Transaction A:

```text
Bob is on call.
I'll go off.
```

Transaction B:

```text
Alice is on call.
I'll go off.
```

Final state:

```text
Alice = Off
Bob   = Off
```

Business rule violated:
> At least one doctor must remain on call.

**Memory Trick:**
> **Business rule broken without touching the same row.**

---

# Isolation Levels vs Concurrency Anomalies

| Isolation Level | Dirty Read | Non-repeatable Read | Read Skew | Phantom Read | Lost Update | Dirty Write | Write Skew |
|-----------------|:----------:|:-------------------:|:---------:|:------------:|:-----------:|:-----------:|:----------:|
| **Read Uncommitted** | ❌ | ❌ | ❌ | ❌ | ❌ | ✅* | ❌ |
| **Read Committed** | ✅ | ❌ | ❌ | ❌ | ⚠️ DB-dependent | ✅ | ❌ |
| **Repeatable Read / Snapshot Isolation** | ✅ | ✅ | ✅ | ⚠️ DB-dependent | ✅** | ✅ | ❌ |
| **Serializable** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

## Legend

- ✅ = Prevented
- ❌ = Allowed
- ⚠️ = Depends on the database implementation

### Notes

- `*` Dirty Writes are prevented by virtually all modern relational databases, even under Read Uncommitted.
- `**` Lost Updates are prevented in Snapshot Isolation implementations that detect write-write conflicts (e.g., PostgreSQL).

---

# One-Line Memory Tricks

| Anomaly | Think... | Problem |
|----------|----------|----------|
| 🟤 Dirty Read | Read uncommitted data | Bad Read |
| 🔵 Non-repeatable Read | Same row changed | Bad Read |
| 🟣 Read Skew | Mixed snapshot | Bad Read |
| 👻 Phantom Read | New matching rows appeared | Bad Query |
| 🟠 Lost Update | My update disappeared | Bad Write |
| 🔴 Dirty Write | Overwrote uncommitted work | Bad Write |
| 🟢 Write Skew | Business rule violated | Bad Business Logic |

---

# Mnemonic

```text
Read Uncommitted
    Trust nobody.

Read Committed
    Only committed data.

Repeatable Read
    Same snapshot.

Serializable
    Same as executing transactions one by one.
```

# How Isolation Levels Are Implemented

Different databases use different techniques to implement isolation levels. The SQL standard defines **what anomalies should be prevented**, but **not how** they should be implemented.

| Isolation Level | Common Implementation | How It Works | Pros | Cons |
|-----------------|-----------------------|--------------|------|------|
| **Read Uncommitted** | No read locks (or ignores them) | Transactions can read data modified by other uncommitted transactions. | Maximum concurrency | Dirty reads and almost every anomaly possible. Rarely used. |
| **Read Committed** | **MVCC** (PostgreSQL, Oracle) or **Short-lived Read Locks** (SQL Server) | Every statement sees only committed data. Writers don't expose uncommitted changes. Read locks (if used) are released immediately after each statement. | Good concurrency, prevents dirty reads | Same transaction may observe different values for the same row. |
| **Repeatable Read** | **Snapshot Isolation (MVCC)** or **Long-lived Read Locks** | Transaction gets a consistent snapshot (MVCC) or holds read locks until commit. Re-reading the same row always returns the same value. | Consistent reads, good concurrency with MVCC | Write skew still possible. Phantom behavior depends on implementation. |
| **Serializable** | **Serializable Snapshot Isolation (SSI)**, **Two-Phase Locking (2PL)**, or **Serial Execution** | Ensures concurrent execution is equivalent to running transactions one after another. | Strongest consistency guarantee | More locking, aborts, retries, or reduced concurrency. |

---

# Implementation Techniques

## 1. Lock-Based Concurrency Control (Pessimistic)

The database prevents conflicts by locking rows (or tables).

### Shared Lock (S Lock)

- Acquired while reading.
- Multiple transactions can hold shared locks simultaneously.

```text
T1 ---- Read Row A ---- [S]

T2 ---- Read Row A ---- [S]
```

Both reads succeed.

---

### Exclusive Lock (X Lock)

- Acquired while writing.
- Only one transaction can hold it.

```text
T1 ---- Write Row A ---- [X]

T2 ---- Wait...
```

The second transaction must wait.

---

### Used By

- SQL Server (default Read Committed)
- Older database implementations
- MySQL for some operations

---

## 2. Two-Phase Locking (2PL)

Used to implement **Serializable** isolation.

Two phases:

### Growing Phase

Acquire locks only.

```text
Read A
Lock A

Read B
Lock B
```

---

### Shrinking Phase

Release locks only after commit.

```text
Commit

Unlock A

Unlock B
```

This guarantees serializability.

### Pros

- Very strong consistency

### Cons

- Blocking
- Deadlocks
- Lower concurrency

---

## 3. Multi-Version Concurrency Control (MVCC)

Instead of locking readers, the database stores multiple versions of each row.

```text
Time →

Version 1
Balance = 100

Version 2
Balance = 150

Version 3
Balance = 200
```

A transaction always reads the version that existed when it started.

Readers never block writers.

Writers never block readers.

### Used By

- PostgreSQL
- Oracle
- MySQL InnoDB
- CockroachDB
- YugabyteDB

---

## 4. Snapshot Isolation (SI)

A specialized form of MVCC.

Every transaction receives a consistent snapshot.

```text
Transaction starts

↓

Snapshot = Version 25

↓

All reads come from Version 25
```

Even if another transaction commits Version 26,

the current transaction continues reading Version 25.

### Prevents

- Dirty Reads
- Non-repeatable Reads
- Read Skew

### Does NOT Prevent

- Write Skew

---

## 5. Serializable Snapshot Isolation (SSI)

Improves Snapshot Isolation.

Instead of blocking transactions,

the database tracks dependencies between transactions.

Example

```text
T1 reads X

T2 reads Y

↓

T1 writes Y

↓

T2 writes X
```

This creates a dangerous dependency cycle.

The database aborts one transaction.

### Used By

- PostgreSQL Serializable

### Pros

- Serializable correctness
- High concurrency

### Cons

- Transactions may abort and need retries.

---

## 6. Serial Execution

Simplest implementation.

Transactions execute one after another.

```text
T1

↓

T2

↓

T3

↓

T4
```

No concurrent execution.

No anomalies possible.

### Used By

- Some in-memory databases
- Single-threaded systems
- Redis (for individual commands)

### Pros

- Very simple
- No locking
- No deadlocks

### Cons

- Poor scalability for long-running transactions

---

# Which Databases Use Which Technique?

| Database | Default Isolation | Primary Technique |
|----------|-------------------|-------------------|
| PostgreSQL | Read Committed | MVCC |
| PostgreSQL Serializable | Serializable | Serializable Snapshot Isolation (SSI) |
| MySQL (InnoDB) | Repeatable Read | MVCC + Next-Key (Gap) Locks |
| Oracle | Read Committed | MVCC |
| SQL Server | Read Committed | Locking (MVCC optional via Snapshot Isolation) |
| SQLite | Serializable | Database/File Locks (single writer) |
| CockroachDB | Serializable | MVCC + Serializable Timestamp Ordering |
| YugabyteDB | Serializable | MVCC + Distributed Transaction Protocol |

---

# Easy Mental Model

```text
Locking
    ↓
Stop others from touching data.

MVCC
    ↓
Give everyone their own copy (snapshot).

Snapshot Isolation
    ↓
Read one consistent snapshot.

Serializable Snapshot Isolation
    ↓
Allow snapshots, but detect dangerous conflicts.

Serial Execution
    ↓
Run one transaction at a time.
```

---

# DDIA Takeaway

There are **three major families of concurrency control**:

1. **Locking (Pessimistic Concurrency Control)**
    - Prevent conflicts by blocking other transactions.

2. **MVCC / Snapshot Isolation (Optimistic Reads)**
    - Readers use snapshots instead of locks.
    - Excellent concurrency, but write skew is still possible.

3. **Serializable Execution**
    - Achieved using **2PL**, **SSI**, or **serial execution**.
    - Prevents all concurrency anomalies while balancing correctness and performance.