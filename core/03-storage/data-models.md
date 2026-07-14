# Data model and query languages

#### SQL (Relational Databases)
- Strongly typed, schema-first design 
- Enforces data integrity via constraints 
- Normalization reduces redundancy 
- Relationships handled via joins

Implication:
- Excellent for systems of record 
- Harder to evolve schema at scale without coordination

Guarantee : ACID Guarantees

Scaling :
Primarily vertical scaling, horizontal scaliang needs partitioning and replication

#### NoSQL (Non-Relational Databases)
Schema-flexible or schema-on-read 
Data modeled around access patterns 
Denormalization is common

Implication:

- Faster iteration and evolution 
- Risk of data inconsistency without discipline

Guarantee : BASE Model

Scaling :
Primarily horizontal scaling, native support for
- Sharding
- Distributed clusters
- Geo distribution

##### Document Stores
Example: MongoDB, rethink db, couch db, Espresso ( linkedIn)
- JSON-like documents
- Best for hierarchical, evolving data
- Good for one to many relationship
- MongoDb uses Map Reduce to perform read only queries across documents

Use Case:
- Product catalogs
- User profiles
- Content systems

##### Key-Value Stores
Example: Redis, DynamoDB
- O(1) lookup

Use Case:
- Caching layer
- Session stores
- Rate limiting

##### Wide-Column Stores
Example: Cassandra, HBase
- Column-family storage
- Optimized for write-heavy workloads

Use Case:
- Time-series data
- Logging
- Analytics pipelines

##### Graph Databases
Example: Neo4j
- Relationship-first modeling
- Good for many to many relationship

Query language 
1. Property graphs ( tables of veritices and edges with index on edge, tail_vertex and head_vertex)
2. Cypher query language 
   1. Neo4j
   2. Ex : (Miami) :WINTIN_IN (USA)
      1. (LUCY) :BORN_IN (MIAMI)
      2. Query : MATCH (person) :BORN_IN (US: location) RETURN person.name
3. Triple stores and SPARQL ( 
   1. info stoed in 3 parts statements
   2. subject, predicate, object 
   3. ex : Jim likes bananas

Use Case:
- Social graphs
- Fraud detection
- Recommendation engines


| Feature | SQL | NoSQL |
|--------|-----|------|
| Schema | Fixed | Flexible / Dynamic |
| Data type | Structured | Structured + Unstructured |
| Scaling | Vertical | Horizontal |
| Transactions | Strong (ACID) | Eventual consistency (BASE) |
| Query language | SQL | Varies (JSON, APIs, etc.) |
| Joins | Supported | Usually not |
| Query Flexibility	| High| Limited
| Operational Complexity | Lower initially | Higher (distributed systems)
| Data Integrity | Enforced | Application-managed |