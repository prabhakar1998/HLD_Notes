# HLD Prep Checklist (Senior SDE / FAANG Focus)

## 1. HLD Foundations
- [ ] Understand requirements gathering (functional + non-functional)
- [ ] Define scale targets (QPS, latency, storage)
- [ ] Identify trade-offs: performance vs consistency vs cost
- [ ] Be able to draw system components and data flow
- [ ] Explain failure scenarios and mitigation

## 2. Database Design ✅

### 2.1 Schema Design Strategies

#### Normalized vs Denormalized Databases

**Normalized Database:**
- ✅ Optimizes for storage space requirement
- ✅ High consistency is present
- ❌ Joins are required (slower queries)

**Denormalized Database:**
- ✅ Often used in read-heavy data where updates/deletes are rare
- ✅ Faster queries (no joins required)
- ❌ Data duplication
- ❌ Update anomalies (changes require updates in multiple places)

### 2.2 Database Keys and Indexing

- **Primary Key**: Primary index on which table is indexed
- **Foreign Keys**: Table relations through primary key references
- **Secondary Index**: Indexed columns apart from primary index (B+ tree implementation)
- **Composite Indexes**: Multiple columns combined to form a single index

### 2.3 Partitioning vs Sharding

#### Partitioning
- Single database server with large tables split into smaller physical segments
- Improves query performance on large datasets

#### Sharding
- Each shard acts as its own independent database
- ✅ Improves scalability and reliability (one shard failure doesn't impact others)
- ✅ Distributed across different servers

**Sharding Strategies:**
- Hash-based sharding
- Range-based sharding
- Geographic hash-based sharding

### 2.4 Replication Strategies

#### Leader-Follower Replication
**Examples**: Kafka, PostgreSQL, Redis, MongoDB
- Leader handles all write requests
- Followers handle read requests
- Simple consistency model

#### Multi-Master Replication
**Examples**: Cassandra, DynamoDB
- Multiple nodes can accept writes
- Requires conflict resolution strategies
- Higher availability but complex consistency

### 2.5 Conflict Resolution Strategies

#### 1. Last Write Wins (LWW)
- Each write gets a timestamp
- Latest timestamp wins during conflicts
- **Pros**: Simple, deterministic
- **Cons**: Can lose updates with clock skew

#### 2. CRDTs (Conflict-free Replicated Data Types)
- Specialized data structures for automatic conflict resolution
- All replicas can update concurrently
- **Used in**: Redis Enterprise Active-Active, Riak, CouchDB
- **Examples**: Counters, sets, registers

#### 3. Operational Transformation (OT)
- Allows concurrent edits on shared data
- Transforms conflicting operations for convergence
- **Used in**: Google Docs, Etherpad, collaborative editors

### 2.6 Caching Strategies

#### Writing Policies
- **Write-Through**: Write to cache and database simultaneously
  - ✅ Ensures consistency
  - ❌ Slower due to synchronous writes
  
- **Write-Back (Write-Behind)**: Write to cache first, then async to database
  - ✅ Low latency, high throughput
  - ❌ Risk of data loss if cache fails
  
- **Write-Around**: Update cache only on reads, not writes
  - ✅ Reduces cache pollution
  - ❌ Cache misses on subsequent reads

#### Reading Policies
- **Read-Through**: Read from cache, fetch from DB if miss
- **Read-Behind**: Async fetch from DB on cache miss
- **Read-Once**: Read from cache only, no DB fallback

### 2.7 Database Selection Guide

#### Default Choice
> **Always prefer PostgreSQL** - it can handle most use cases effectively

#### Selection Criteria
1. **Data Structure**: Clear and consistent structure
2. **Query Patterns**: Type and frequency of queries
3. **Scale Requirements**: Read/write volume and performance needs

#### Database Types by Use Case

##### Relational Databases (PostgreSQL)
- **Use When**: 
  - Clear structured data
  - ACID guarantees needed (payments, inventory)
  - Complex relationships and joins

##### Document Databases (MongoDB, CouchBase)
- **Use When**: 
  - Unstructured data with varying fields
  - Dynamic query patterns
  - Flexible schema requirements

##### Columnar Databases (Cassandra, ClickHouse)
- **Use When**: 
  - Ever-increasing data volume
  - Limited/finite query patterns
  - OLAP workloads

##### Search Engines (Elasticsearch)
- **Use When**: 
  - Text search with fuzzy matching
  - Full-text search capabilities
  - Built on Apache Lucene
- **Note**: Use as secondary data source, not primary

##### Time-Series Databases (InfluxDB, TimescaleDB)
- **Use When**: 
  - Append-only, write-heavy workloads
  - Bulk range queries
  - Sequential writes without updates

##### File Storage (S3, CDN)
- **S3**: Use pre-signed URLs for uploads
- **CDN**: Global asset distribution

##### Analytics (Hadoop)
- **Use When**: 
  - Large dataset analytics
  - Report generation
  - Offline processing

#### OLAP vs OLTP
- **OLAP (Online Analytical Processing)**: Analytics, reporting, read-heavy
- **OLTP (Online Transaction Processing)**: PostgreSQL, MongoDB (ACID compliant)

#### Real-World Example
- **Inventory Management**: Strong consistency → RDBMS (PostgreSQL)
- **Historical Orders**: Eventually consistent → Cassandra

## 3. API Design
*Topics to be covered:*
- [ ] REST vs gRPC vs WebSockets vs SSE vs Webhooks
- [ ] Idempotency, pagination, rate-limiting
- [ ] Versioning strategies (URL, header)
- [ ] Security: auth & authorization (JWT, OAuth, mTLS)
- [ ] Observability: logging, metrics, tracing
- [ ] Backpressure & graceful degradation

## 4. Database Comparisons
*Topics to be covered:*
- [ ] PostgreSQL: ACID, joins, strong consistency, OLTP workloads
- [ ] MongoDB: document store, flexible schema, hierarchical data
- [ ] Cassandra: wide-column, linear horizontal scaling, eventual consistency
- [ ] Elasticsearch: full-text search, analytics, near real-time indexing
- [ ] Compare scalability, read/write patterns, consistency, indexing options

## 5. Transactions & Concurrency
*Topics to be covered:*
- [ ] ACID vs BASE
- [ ] Distributed transactions (2PC vs Sagas)
- [ ] Concurrency control: optimistic vs pessimistic locking
- [ ] Conflict detection and retries
- [ ] Real-world examples: ticket booking, money transfers

## 6. Messaging / Event Systems
*Topics to be covered:*
- [ ] Kafka architecture: brokers, partitions, consumer groups, replication
- [ ] Delivery semantics: at-most-once, at-least-once, exactly-once
- [ ] Message queue patterns vs streaming vs pub/sub
- [ ] Pulsar / RabbitMQ comparisons
- [ ] Failure handling, retries, backpressure

## 7. API & Communication Patterns
*Topics to be covered:*
- [ ] REST vs gRPC (synchronous), WebSockets / SSE (real-time)
- [ ] Streaming patterns: client → server, server → client, bi-directional
- [ ] Trade-offs: latency, scalability, browser-friendliness, type-safety
- [ ] Protocol decisions for internal vs external clients

## 8. High-Concurrency Systems
*Topics to be covered:*
- [ ] Ticket booking, seat allocation strategies
- [ ] Optimistic locking, compare-and-swap, distributed locks
- [ ] Rate-limiting & anti-oversell patterns
- [ ] Performance tuning: caching, database batching, partitioning

## 9. Streaming / Media Systems
*Topics to be covered:*
- [ ] Live chat/comments: fan-out strategies, consistency, scalability
- [ ] Video streaming: HLS / DASH / RTMP
- [ ] CDNs & edge caching
- [ ] Low-latency trade-offs vs throughput
- [ ] Eventual consistency for feeds and timelines

## 10. System Migration
*Topics to be covered:*
- [ ] Database migration strategies (online vs offline)
- [ ] Zero-downtime schema changes
- [ ] Versioned APIs and rolling deployments
- [ ] Data validation and rollback strategies

## 11. Billing / Reconciliation Systems
*Topics to be covered:*
- [ ] Multi-service data consistency (eventual vs strong)
- [ ] Deduplication & idempotency
- [ ] Event-driven reconciliation pipelines
- [ ] Failure handling, retries, and audit logs

---

## Progress Tracker
- [x] **Database Design** - Complete
- [ ] **API Design** - Pending
- [ ] **Database Comparisons** - Pending
- [ ] **Transactions & Concurrency** - Pending
- [ ] **Messaging / Event Systems** - Pending
- [ ] **API & Communication Patterns** - Pending
- [ ] **High-Concurrency Systems** - Pending
- [ ] **Streaming / Media Systems** - Pending
- [ ] **System Migration** - Pending
- [ ] **Billing / Reconciliation Systems** - Pending