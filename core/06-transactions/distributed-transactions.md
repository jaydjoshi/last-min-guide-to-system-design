# Distributed transactions -  2PC, 3PC, and SAGA

A distributed transaction spans multiple services or databases.

Example:

```text
Order Service
Payment Service
Inventory Service
```

One business operation may touch all three.

Problem:

```text
How do we keep all systems consistent
if failures happen mid-operation?
```

Three classic approaches:

```text
1. Two-Phase Commit (2PC)
2. Three-Phase Commit (3PC)
3. Saga Pattern
```

---

#### 1. Two-Phase Commit (2PC)

##### Core Idea

All participants agree to commit before anyone commits.

Atomic commit across nodes.

```text
Either everyone commits
or everyone aborts
```

---

##### Roles

Two roles:

* Coordinator (Transaction Manager)
* Participants (Resource Managers)

Example:

```text
Coordinator
   |
----------------------
|         |          |
DB1      DB2       DB3
```

---

##### Two Phases

##### Phase 1 — Prepare (Voting)

Coordinator asks:

```text
Can you commit?
```

Sends:

```text
PREPARE
```

Participants:

* Execute transaction locally
* Write changes to log
* Lock resources
* Vote:

```text
YES
or
NO
```

---

###### Example

```text
Coordinator → Payment : PREPARE
Coordinator → Inventory : PREPARE

Payment → YES
Inventory → YES
```

All vote yes.

Move to phase 2.

---

##### Phase 2 — Commit

Coordinator decides:

```text
GLOBAL COMMIT
```

Sends commit to all.

```text
Coordinator -> Commit
Participants -> Commit locally
```

Done.

---

##### Abort Case

If even one votes NO:

```text
GLOBAL ABORT
```

Everyone rolls back.

---

#### 2PC Flow

```text
Begin Transaction
   ↓
Prepare Request
   ↓
All vote yes?
  /   \
No     Yes
 |      |
Abort  Commit
```

---

##### Why Logging Matters

Participants persist:

```text
Prepared state
```

If crash occurs:

Recovery can continue.

Critical for durability.

---

##### Strengths

* Atomicity
* Strong consistency
* Simple model conceptually

---

##### Problems with 2PC

##### Blocking Problem

Huge weakness.

Suppose:

```text
Everyone prepared
Coordinator crashes
```

Participants sit in:

```text
uncertain state
```

Cannot commit.
Cannot abort.

Blocked.

Classic problem.

---

##### Coordinator is Single Point of Failure

Even replicated coordinators are tricky.

---

##### Performance Cost

* Multiple network round trips
* Locks held longer
* Lower availability

---

##### Where Used

Traditional distributed databases:

* XA transactions
* Some RDBMS clusters

Less common in microservices.

---

#### 2. Three-Phase Commit (3PC)

Attempt to reduce blocking.

Adds extra phase.

---

##### Idea

Split commit into:

```text
1. CanCommit
2. PreCommit
3. DoCommit
```

---

##### Phase 1 — CanCommit

Like prepare.

Ask:

```text
Can you commit?
```

Participants vote.

---

##### Phase 2 — PreCommit

Coordinator says:

```text
Prepare to commit
```

Participants enter safe precommit state.

Important:

```text
No uncertainty about whether commit possible.
```

---

##### Phase 3 — DoCommit

Final commit.

```text
Commit now.
```

---

##### Flow

```text
CanCommit
  ↓
PreCommit
  ↓
DoCommit
```

---

##### Why It Helps

If coordinator fails after precommit,
participants can often decide safely.

Less blocking.

---

##### Problems

Still has issues:

* Assumes timing bounds
* Complicated
* Rarely used in practice

Most systems prefer consensus instead.

---

#### 2PC vs 3PC

| Feature        | 2PC      | 3PC     |
| -------------- | -------- | ------- |
| Phases         | 2        | 3       |
| Blocking Risk  | Yes      | Reduced |
| Complexity     | Lower    | Higher  |
| Real-world Use | Commoner | Rare    |

---

#### 3. Saga Pattern

Very different model.

Instead of one global atomic transaction:

```text
Sequence of local transactions
plus compensations
```

Huge idea.

---

##### Example

Order workflow:

```text
1 Create Order
2 Charge Payment
3 Reserve Inventory
```

---

Suppose step 3 fails.

Use compensations:

```text
Undo Payment
Cancel Order
```

---

##### Concept

Forward steps:

```text
T1 → T2 → T3
```

Compensations:

```text
C3 ← C2 ← C1
```

Reverse undo.

---

#### Orchestration Saga

Central coordinator drives flow.

```text
Saga Orchestrator
    |
Payment
Inventory
Shipping
```

Like workflow engine.

---

##### Example

```text
Order Created
 ↓
Charge Payment
 ↓
Reserve Inventory
 ↓
Ship
```

If reserve fails:

```text
Refund Payment
```

---

#### Choreography Saga

No central orchestrator.

Services react to events.

```text
OrderCreated event
  ↓
Payment processes
  ↓
Inventory reacts
```

Event-driven.

---

##### Pros

* Decentralized
* Looser coupling

---

##### Cons

* Harder debugging
* Complex event chains

---

#### Important Truth

Sagas are NOT atomic.

They provide:

```text
Eventual consistency
```

Very different from 2PC.

---

##### Compensation Is Hard

Compensating action is not always simple rollback.

Refunding payment ≠ erase payment.

Semantic undo.

Very important.

---

#### Saga Example Timeline

```text
T1 Create Order        Success
T2 Charge Card         Success
T3 Reserve Inventory   Fail

Compensate:
C2 Refund Card
C1 Cancel Order
```

---

#### When Sagas Work Well

Good for:

* Microservices
* Long-running workflows
* Business processes

Examples:

* Travel booking
* E-commerce order flow
* Payment workflows

---

#### When Sagas Are Bad

Bad when hard invariants needed.

Example:

* Core banking ledger

Use stronger transaction models.

---

#### Compare All Three

| Feature       | 2PC       | 3PC     | Saga          |
| ------------- | --------- | ------- | ------------- |
| Atomic Commit | Yes       | Yes     | No            |
| Blocking      | Yes       | Reduced | No            |
| Availability  | Lower     | Medium  | Higher        |
| Consistency   | Strong    | Strong  | Eventual      |
| Complexity    | Medium    | High    | High          |
| Typical Use   | Databases | Rare    | Microservices |

---

#### One-Line Summary

> 2PC provides atomic distributed commit through voting and coordinated commit, 3PC reduces blocking with an extra phase but is rarely used, while Sagas trade atomicity for availability by using sequences of local transactions with compensating actions.