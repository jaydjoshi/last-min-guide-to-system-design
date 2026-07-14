# Encoding and Evolution

Encoding means:

```text
Transforming in-memory data into a format
that can be stored or transmitted.
```

Also called:

* Serialization
* Marshaling

Reverse process:

* Deserialization

---

#### Why Encoding Matters

Used whenever data crosses boundaries:

```text
Application -> Disk
Application -> Network
Service A -> Service B
Database -> Client
```

Goals:

* Compact representation
* Fast processing
* Compatibility over time

---

> Data formats must evolve without breaking systems.

This is schema evolution.

---

#### Major Encoding Types

##### 1. Language-Specific Encodings

Examples:

* Java serialization
* Python pickle

Problems:

* Language locked
* Poor interoperability
* Security issues

Usually avoided.

---

##### 2. Text Formats

Examples:

* JSON
* XML
* CSV

Pros:

* Human readable
* Flexible
* Widely supported

Cons:

* Larger payloads
* Weak typing

---

##### 3. Binary Formats

Examples:

* Avro
* Protocol Buffers
* Thrift

Pros:

* Smaller
* Faster
* Strong schemas

Used heavily in distributed systems.

---

#### Schema Evolution

Systems change.

Fields get added.

Schemas evolve.

Need compatibility.

---

##### Backward Compatibility

New code can read old data.

---

##### Forward Compatibility

Old code can read newer data.

---

Both matter.

---

#### How Compatibility Works

Typical rule:

```text
Unknown fields are ignored.
```

Example:

Old schema:

```text
name
age
```

New schema:

```text
name
age
email
```

Old consumers ignore email.

System still works.

---

#### Avro vs Protocol Buffers (High Level)

##### Protocol Buffers / Thrift

Schema IDs on fields.

```text
1 -> name
2 -> age
```

Supports evolution via field numbering.

---

##### Avro

Writer schema + reader schema.

Schemas negotiated during read.

Very flexible.

Popular in data pipelines.

---

#### Dataflow Between Processes

Chapter also covers how encoded data moves.

Three patterns:

---

##### Database Writes

Application writes encoded records.

Later readers may use different schema versions.

Schema evolution required.

---

##### Service Calls (RPC)

```text
Service A <-> Service B
```

Requests and responses serialized.

Versioning matters.

---

##### Message Passing

Events often encoded.

Example:

```text
OrderCreated event
```

Consumers may run different versions.

Compatibility critical.

---

#### Why This Matters in Distributed Systems

Services rarely upgrade simultaneously.

Reality:

```text
Old producers
New consumers
Mixed versions everywhere
```

Encodings must tolerate this.

---

#### Tradeoffs

| Format   | Human Readable | Compact | Schema Support |
| -------- | -------------- | ------- | -------------- |
| JSON     | Yes            | No      | Weak           |
| Protobuf | No             | Yes     | Strong         |
| Avro     | No             | Yes     | Strong         |

---

#### Real System Uses

##### JSON

* REST APIs
* External interfaces

---

##### Protobuf

* gRPC
* Internal services

---

##### Avro

* Kafka pipelines
* Data platforms

---

#### Mental Models

##### Encoding

```text
Objects -> Bytes
```

---

##### Schema Evolution

```text
Change without breaking readers
```

---

##### Compatibility

```text
Old and new versions coexist
```

#### One-Line Summary

> Encoding turns data into bytes for storage or communication, while schema evolution ensures those formats can change over time without breaking distributed systems.