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

---

##### Dirty Reads

Transaction reads uncommitted data.

Bad.

---

##### Dirty Writes

Two transactions overwrite each other.

Bad.

---

##### Read Skew

A transaction sees part old state and part new state.

```text
Acc1 : $500
Acc2 : $500
Total balance : $1000

Transfer from Acc1 to Acc2
Mid way
Acc1 : $400
Acc2 : $500 ( Credit did not happen yet, it is in progress)
Total balance in another transaction : $900

```

---

##### Lost Updates

Two writes.
One disappears.

---

##### Write Skew

Each write looks safe alone
Unsafe together

```text
Doctor on call , Atleast 1 doctor must be on call.

Doc1 on call = true
Doc2 on call = true

In 2 transactions, both read data for doct1 and doc2 and decide to take break since 1 will be  on call
Doc1 on call = false ( takes break)
Doc2 on call = false ( takes break)

None are on call
```

---