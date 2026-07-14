# Isolation Levels

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