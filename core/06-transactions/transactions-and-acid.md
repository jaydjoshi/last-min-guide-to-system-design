# Transactions
A transaction is:

```text
A group of operations that execute
as one logical unit.
```

Core idea:

> Either all operations happen, or none do.

---

#### Why Transactions Exist

They simplify correctness.

Protect against:

* Partial failures
* Concurrency bugs
* Data corruption

---

#### ACID Properties

```text
Atomicity
Consistency
Isolation
Durability
```

---

##### Atomicity

All-or-nothing.

Example:

Bank transfer:

```text
Debit A
Credit B
```

If second step fails:

Rollback.

---

##### Consistency

Transaction preserves database invariants.

Example:

```text
Balance cannot go negative
```

---

##### Isolation

Concurrent transactions should not interfere.

---

##### Durability

Committed data survives crashes.

Typically via WAL.

---

#### Isolation Problems (Major Theme)

Without proper isolation:

Bad things happen.

