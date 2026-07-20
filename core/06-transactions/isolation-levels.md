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