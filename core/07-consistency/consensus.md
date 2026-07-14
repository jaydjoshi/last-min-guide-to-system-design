# Consistency and Consensus 

```text
How do multiple machines agree
despite failures?
```

That is consensus.

---

#### What is Consensus?

Consensus means:

```text
Multiple nodes agree on one decision.
```

Examples:

- Who is leader?
- What value gets committed?
- What is the correct order of operations?

---

#### Why Consensus Matters

Needed for:

- Leader election
- Distributed locks
- Metadata coordination
- Configuration changes
- Fault-tolerant replication

---

#### Core Requirements

Consensus needs:

##### Agreement

```text
All healthy nodes agree
on same decision.
```

---

##### Safety

```text
Never make conflicting decisions.
```

Example:

Never two leaders for same term.

---

##### Liveness

```text
Eventually make progress.
```

---

#### Quorums

Suppose:

```text
5 nodes
Majority = 3
```

Need majority agreement.

Key idea:

```text
Majorities overlap.
```

This preserves safety.

---

#### Leader-Based Consensus

Common model:

```text
Leader proposes decisions

Followers replicate log

Quorum commits them
```

---

#### Raft (High Level)

Three main parts:

---

##### 1. Leader Election

Nodes vote.

One becomes leader.

```text
Followers -> Candidates -> Leader
```

---

##### 2. Log Replication

Commands appended to replicated log.

```text
Write
 ↓
Leader log
 ↓
Followers copy
```

---

##### 3. Commit Rule

Entry committed after majority acknowledges.

---

#### Log as Source of Truth

Important idea:

```text
State machine = replayed log
```

Consensus often means agreeing on log order.

Not just agreeing on values.

---

#### Split Brain

Danger:

```text
Two nodes think they are leader.
```

Consensus prevents this.

---

#### Strong Consistency

Consensus often gives:

```text
Linearizable behavior
```

Feels like single machine.

Read after write sees latest value.

---

#### Safety vs Availability

Tradeoff:

```text
Prefer refusing progress

over returning wrong answers
```

Huge chapter theme.

---

#### Failure Handling

Consensus handles:

- Node crashes
- Delayed messages
- Network partitions
- Leader failures

---

#### Compare Concepts

| Concept | Purpose |
|--------|---------|
| Replication | Copy data |
| Quorum | Majority agreement |
| Consensus | Agree under failures |
| Raft | Protocol implementing consensus |

---

#### Real Systems

Uses consensus:

- etcd
- ZooKeeper
- Kafka metadata quorum
- Kubernetes control plane


# One-Line Summary

> Consensus lets distributed systems agree safely under failures using quorums and protocols like Raft, typically by agreeing on an ordered replicated log.