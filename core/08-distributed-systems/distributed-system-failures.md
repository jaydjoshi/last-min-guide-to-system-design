# The Trouble with Distributed Systems


```text
Distributed systems fail partially,
not completely.
```

Single machines usually:

- Work
- Crash

Distributed systems can be in weird intermediate states.

---

#### Partial Failures

```text
Some nodes work
Some don't
Network may lie
```

Hardest distributed systems problem.

---

#### Unreliable Networks

Messages may:

- Be delayed
- Be lost
- Arrive twice
- Arrive out of order

---

##### Timeout Problem

If a request times out:

```text
Did it fail?

Or was it just slow?
```

You often cannot know.

Major chapter insight.

---

#### Network Partitions

Nodes may stop communicating.

```text
Node A can't see Node B
```

System may split.

Now you must choose:

- Wait
- Keep serving requests
- Risk inconsistency

---

#### Unreliable Clocks

Physical clocks can lie.

Problems:

- Clock drift
- Clock skew
- NTP failures
- Leap seconds

---

##### Rule

```text
Never trust wall-clock time
for correctness.
```

Use ordering, not timestamps, for correctness.

---

#### Process Pauses

Machines can appear dead but are not.

Examples:

- Garbage collection pause
- VM freeze
- Scheduler delays

Can trigger false failure detection.

---

#### Failure Detection Is Uncertain

Failures are inferred, not observed.

Typically using:

- Timeouts
- Heartbeats
- Quorums

But these are guesses.

---

#### Local Knowledge Can Be Wrong

A node may believe:

```text
I am leader
```

And be wrong.

Important mental shift:

```text
Local knowledge may be false
```

---

#### System Models

##### Synchronous

Bounded delays.

Mostly unrealistic.

---

##### Asynchronous

No timing guarantees.

Closer to reality.

---

##### Reality

Usually somewhere messy in between.

---

#### Logical Time

Use ordering rather than physical clocks.

Examples:

- Lamport timestamps
- Version vectors

Helps reason about causality.

---

#### Byzantine Faults (High Level)

Nodes may behave arbitrarily or maliciously.

Separate, harder fault model.

---

#### Core Mental Models

```text
Networks are unreliable

Clocks are unreliable

Failure detection is uncertain
```


#### One-Line Summary

> Distributed systems are hard because networks, clocks, and failure detection are unreliable, so systems must be designed assuming uncertainty and partial failure.