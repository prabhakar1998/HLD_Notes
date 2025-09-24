# HLD Prep Checklist (Senior SDE / FAANG Focus)

## 1. HLD Foundations ✅

### 1.1 Requirements Gathering

> **Always start with clarification questions** and align on CAP theorem trade-offs

#### Functional Requirements
- **Definition**: What the system needs to do
- **Format**: "User should be able to..." statements
- **Example**: URL shortener → User should be able to submit a long URL and get a shortened URL

#### Non-Functional Requirements
- **Scalability**: QPS (Queries Per Second), DAU (Daily Active Users), MAU (Monthly Active Users)
- **Durability**: Data persistence guarantees
- **Availability/Consistency**: Trade-offs under CAP theorem
- **Latency**: Response time expectations
- **Reliability**: SLA requirements
- **Load Patterns**: Write/read ratio analysis

### 1.2 Scale Estimation

#### Key Metrics to Calculate
- **Daily Active Users (DAU)**
- **Total read/write operations per second**
- **Storage requirements**
- **Bandwidth requirements**

#### Quick Estimation Framework
1. Start with user base size
2. Calculate daily operations per user
3. Convert to QPS (operations/day ÷ 86400)
4. Apply peak traffic multiplier (usually 2-5x)

### 1.3 System Trade-offs Analysis

#### Consistency Spectrum
- **Strong Consistency**: Higher latency, lower availability
  - **Use Cases**: Banking, inventory management (ACID properties required)
- **Eventual Consistency**: Lower latency, higher availability
  - **Use Cases**: Social media feeds, content delivery

#### Performance vs Consistency vs Cost
- **High Performance + Strong Consistency** = Higher cost
- **High Performance + Eventual Consistency** = Moderate cost
- **Strong Consistency + Low Cost** = Lower performance

### 1.4 System Architecture Design

#### Essential Components
- **Draw system components** with clear boundaries
- **Show data flow** between services
- **Identify service boundaries** and responsibilities
- **Define APIs** between components

### 1.5 Failure Scenarios & Mitigation

#### Single Point of Failure (SPOF) Analysis
- **Database Failure**: Use replicas and automatic failover
- **Cache Failure**: Implement fallback to database
- **Region Failure**: Multi-region deployment with DNS failover
- **Network Partition**: Retry with exponential backoff + idempotency keys
- **Service Failure**: Circuit breaker pattern + graceful degradation

#### Monitoring & Observability
- **Early Detection**: Comprehensive monitoring and alerting
- **Health Checks**: Service and dependency monitoring
- **Metrics**: Error rates, latency, throughput
- **Logging**: Structured logging for debugging

### 1.6 Fundamental Concepts

#### ACID Properties
- **Atomicity**: All-or-nothing transactions (complete success or complete rollback)
- **Consistency**: Database transitions from one valid state to another
- **Isolation**: Concurrent transactions execute independently without interference
- **Durability**: Committed transactions persist even after hardware failure

#### BASE Properties (NoSQL Alternative)
- **Basically Available**: System guarantees availability even during failures
- **Soft State**: System state may change over time without explicit input
- **Eventual Consistency**: System will become consistent over time through replication

#### CAP Theorem
**You can only guarantee 2 out of 3:**

- **Consistency + Partition Tolerance**: Every read receives the latest write or error
  - **Examples**: Traditional RDBMS in single-region
  - **Trade-off**: Reduced availability during network partitions

- **Availability + Partition Tolerance**: Every read responds with data (no errors)
  - **Examples**: DynamoDB, Cassandra
  - **Trade-off**: May return stale data during network partitions

- **Consistency + Availability**: Perfect for systems without network partitions
  - **Reality**: Network partitions are inevitable in distributed systems

#### Real-World CAP Examples
| System | Choice | Trade-off |
|--------|--------|-----------|
| **Banking** | CP | Prefer consistency over availability |
| **Social Media** | AP | Prefer availability over consistency |
| **E-commerce Cart** | AP | Prefer availability (eventual consistency OK) |
| **Payment Processing** | CP | Prefer consistency (critical for money) |

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

### 2.7 Database Selection Summary

#### Default Choice
> **Always prefer PostgreSQL** - it can handle most use cases effectively

#### Quick Selection Criteria
1. **Data Structure**: Clear and consistent structure
2. **Query Patterns**: Type and frequency of queries  
3. **Scale Requirements**: Read/write volume and performance needs

#### OLAP vs OLTP
- **OLAP (Online Analytical Processing)**: Analytics, reporting, read-heavy
  - **Examples**: ClickHouse, BigQuery, Snowflake
- **OLTP (Online Transaction Processing)**: Real-time transactions, ACID compliant
  - **Examples**: PostgreSQL, MySQL, MongoDB

> **For detailed database comparisons, selection frameworks, and architecture patterns, see Section 4**

## 3. API Design
*Topics to be covered:*
- [ ] REST vs gRPC vs WebSockets vs SSE vs Webhooks
- [ ] Idempotency, pagination, rate-limiting
- [ ] Versioning strategies (URL, header)
- [ ] Security: auth & authorization (JWT, OAuth, mTLS)
- [ ] Observability: logging, metrics, tracing
- [ ] Backpressure & graceful degradation

## 4. Database Comparisons ✅

### 4.1 Database Types by Use Case

#### Relational Databases (PostgreSQL, MySQL)
- **Data Model**: Tables with rows and columns, strict schema
- **Consistency**: ACID compliant, strong consistency
- **Scalability**: Vertical scaling primarily, read replicas for horizontal read scaling
- **Query Patterns**: Complex joins, transactions, SQL queries
- **Indexing**: B-tree indexes, composite indexes, partial indexes
- **Use When**: 
  - Clear structured data
  - ACID guarantees needed (payments, inventory)
  - Complex relationships and joins
  - Strong consistency requirements
- **Examples**: User management, financial transactions, inventory systems

#### Document Databases (MongoDB, CouchBase)
- **Data Model**: JSON-like documents, flexible schema
- **Consistency**: Configurable (strong to eventual)
- **Scalability**: Horizontal scaling via sharding
- **Query Patterns**: Document queries, aggregation pipelines
- **Indexing**: Compound indexes, text indexes, geospatial indexes
- **Use When**: 
  - Unstructured data with varying fields
  - Dynamic query patterns
  - Flexible schema requirements
  - Rapid prototyping needs
- **Examples**: Content management, user profiles, product catalogs

#### Wide-Column Databases (Cassandra, HBase)
- **Data Model**: Column families, denormalized data
- **Consistency**: Tunable consistency (eventual by default)
- **Scalability**: Linear horizontal scaling, multi-datacenter
- **Query Patterns**: Simple queries by partition key, limited joins
- **Indexing**: Partition key indexing, secondary indexes (limited)
- **Use When**: 
  - Ever-increasing data volume (time-series, IoT)
  - High write throughput requirements
  - Limited/finite query patterns
  - Multi-region deployments
- **Examples**: Time-series data, IoT sensors, messaging systems

#### Search Engines (Elasticsearch, Solr)
- **Data Model**: Inverted indexes, JSON documents
- **Consistency**: Near real-time, eventually consistent
- **Scalability**: Horizontal scaling via sharding
- **Query Patterns**: Full-text search, aggregations, analytics
- **Indexing**: Inverted indexes, analyzer-based tokenization
- **Use When**: 
  - Text search with fuzzy matching
  - Full-text search capabilities
  - Real-time analytics and aggregations
  - Log analysis and monitoring
- **Note**: Use as secondary data source, not primary
- **Examples**: Search functionality, log analysis, content discovery

#### Time-Series Databases (InfluxDB, TimescaleDB)
- **Data Model**: Time-stamped data points, tags and fields
- **Consistency**: Strong consistency for writes, configurable for reads
- **Scalability**: Time-based partitioning, retention policies
- **Query Patterns**: Time-range queries, aggregations over time windows
- **Indexing**: Time-based indexes, tag indexes
- **Use When**: 
  - Append-only, write-heavy workloads
  - Bulk range queries over time periods
  - Sequential writes without updates
  - Monitoring and observability data
- **Examples**: Metrics monitoring, IoT data, financial tick data

#### Key-Value Stores (Redis, DynamoDB)
- **Data Model**: Simple key-value pairs, some support data structures
- **Consistency**: Configurable (Redis: strong, DynamoDB: eventual/strong)
- **Scalability**: Horizontal scaling (DynamoDB), clustering (Redis)
- **Query Patterns**: Simple key lookups, limited range queries
- **Indexing**: Hash-based, some support secondary indexes
- **Use When**: 
  - Simple key-based access patterns
  - High-performance caching
  - Session management
  - Real-time recommendations
- **Examples**: User sessions, caching layer, leaderboards

#### Graph Databases (Neo4j, Amazon Neptune)
- **Data Model**: Nodes and relationships, property graphs
- **Consistency**: ACID compliant (Neo4j), eventual (Neptune)
- **Scalability**: Limited horizontal scaling, read replicas
- **Query Patterns**: Graph traversals, relationship queries
- **Indexing**: Node/relationship property indexes
- **Use When**: 
  - Complex relationship modeling
  - Graph traversal queries
  - Social networks, recommendation engines
  - Fraud detection patterns
- **Examples**: Social networks, recommendation systems, knowledge graphs

### 4.2 Comprehensive Database Comparison Table

| Database | Type | Consistency | Scalability | Query Flexibility | Best For |
|----------|------|-------------|-------------|------------------|----------|
| **PostgreSQL** | Relational | ACID Strong | Vertical + Read Replicas | SQL, Complex Joins | OLTP, Complex Queries |
| **MongoDB** | Document | Configurable | Horizontal Sharding | Flexible, Aggregation | Flexible Schema, Rapid Dev |
| **Cassandra** | Wide-Column | Eventual | Linear Horizontal | Limited, CQL | High Write Volume, IoT |
| **Elasticsearch** | Search | Near Real-time | Horizontal | Full-text, Analytics | Search, Log Analysis |
| **Redis** | Key-Value | Strong | Clustering | Key-based, Lua | Caching, Real-time |
| **DynamoDB** | Key-Value | Configurable | Auto-scaling | Key/Index-based | Serverless, AWS Native |
| **InfluxDB** | Time-Series | Strong Writes | Time Partitioning | Time-based Queries | Metrics, Monitoring |
| **Neo4j** | Graph | ACID Strong | Limited | Graph Traversal | Relationships, Social |

### 4.3 Performance Characteristics

#### Read/Write Patterns
- **Read-Heavy**: PostgreSQL (with replicas), MongoDB, Elasticsearch
- **Write-Heavy**: Cassandra, InfluxDB, DynamoDB
- **Balanced**: PostgreSQL, MongoDB
- **Analytics**: Elasticsearch, ClickHouse, BigQuery

#### Latency Expectations
- **Sub-millisecond**: Redis, DynamoDB (single-digit ms)
- **Low Latency**: PostgreSQL, MongoDB (< 10ms)
- **Moderate**: Cassandra, Elasticsearch (10-100ms)
- **High Latency OK**: Data warehouses, batch analytics

### 4.4 Quick Decision Framework

#### By Data Structure
- **Structured + Relationships** → PostgreSQL
- **Semi-structured + Flexible** → MongoDB  
- **Key-Value + High Performance** → Redis/DynamoDB
- **Time-Series + High Writes** → InfluxDB/TimescaleDB
- **Graph + Relationships** → Neo4j
- **Search + Analytics** → Elasticsearch

#### By Scale Requirements
- **< 1M records** → PostgreSQL (any type)
- **1M - 100M records** → PostgreSQL, MongoDB, Redis
- **100M - 1B records** → MongoDB, Cassandra, DynamoDB
- **> 1B records** → Cassandra, DynamoDB, ClickHouse

#### By Consistency Requirements
- **Strong Consistency Required** → PostgreSQL, Redis, Neo4j
- **Eventual Consistency Acceptable** → Cassandra, DynamoDB, Elasticsearch
- **Configurable** → MongoDB (read/write concerns)

### 4.5 Multi-Database Architecture Patterns

#### Polyglot Persistence
Use multiple databases optimized for different data patterns:
- **Primary OLTP**: PostgreSQL
- **Caching**: Redis
- **Search**: Elasticsearch  
- **Analytics**: ClickHouse
- **Time-Series**: InfluxDB

#### Real-World Architecture Examples

##### E-commerce Platform
```
User Action → PostgreSQL (orders, payments)
           → Redis (cart state, sessions)
           → Elasticsearch (product search)
           → InfluxDB (analytics events)
```

##### Social Media Platform
```
User Posts → Cassandra (posts, timeline)
          → Neo4j (social graph, relationships)  
          → Redis (real-time features, notifications)
          → Elasticsearch (content search)
```

##### IoT Platform
```
Sensor Data → InfluxDB (time-series metrics)
           → PostgreSQL (device metadata, users)
           → Elasticsearch (log analysis, alerting)
           → Redis (real-time dashboards)
```

##### Financial System
```
Transactions → PostgreSQL (core transactions, ACID)
            → Redis (fraud detection cache, sessions)
            → ClickHouse (analytics, reporting)
            → Elasticsearch (audit logs, compliance)
```

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
- [ ] Kafka/ Pulsar / RabbitMQ comparisons
- [ ] Failure handling, retries, backpressure

## 7. API & Communication Patterns ✅

### 7.1 Communication Protocol Overview

#### REST
- **Standard**: Exposing web APIs over HTTP using JSON
- **Methods**: 
  - **Idempotent**: GET, PUT (complete replacement), DELETE
  - **Non-idempotent**: POST, PATCH
- **Pros**: Human readable, easy to debug
- **Use Cases**: Standard web APIs, CRUD operations

#### gRPC
- **Built on**: HTTP/2, supports multiplexing and streaming
- **Benefits**: 
  - Binary format (compact and efficient)
  - Strongly typed (Protobuf schema)
  - Bidirectional streaming
  - Best for microservice-to-microservice communication
- **Cons**: 
  - Harder to debug (binary data)
  - Browser support limitations
- **Use Cases**: Performance-sensitive applications, internal services

#### WebSockets
- **Connection**: Persistent TCP connection between client and server
- **Process**: Client upgrades HTTP connection to TCP for bidirectional messaging
- **Benefits**:
  - Low latency (connection established once)
  - Real-time bidirectional communication
- **Cons**:
  - Harder to scale (long-lived connections)
  - Special handling required for load balancers and firewalls
- **Use Cases**: Real-time chat, stock trading apps, multiplayer games
- **Note**: Server typically supports 50K-200K concurrent TCP connections

#### Server-Sent Events (SSE)
- **Direction**: Unidirectional (server → client) over HTTP
- **Process**: Client subscribes via HTTP request
- **Benefits**:
  - Lightweight compared to WebSockets
  - Built-in reconnect/retry mechanism
  - Simpler than WebSockets
  - UTF-8 text support (text/event-stream)
- **Cons**:
  - Unidirectional only
  - Less flexible than WebSockets
- **Use Cases**: Notifications, feeds, dashboards

#### HTTP Long-Polling
- **Process**: Client sends request, server keeps it open until new data arrives or timeout
- **Built on**: HTTP/1.1 with immediate reconnection
- **Cons**:
  - Inefficient (TCP handshakes every time)
  - HTTP header overhead
  - Higher latency than WebSockets/SSE
  - Resource wasteful when no data available

### 7.2 Connection Recovery Strategies

#### Client Restarts
- Existing connections dropped
- Client must re-establish connections (gRPC, WebSockets, SSE)
- In-progress streams lost
- Idempotent requests can be safely retried

#### Server Restarts
- All active client connections terminated
- Clients should implement retry with exponential backoff
- Server recovery time affects reconnection success

### 7.3 Streaming Patterns

#### Communication Directions
- **Client → Server**: REST, gRPC unidirectional
- **Server → Client**: gRPC unidirectional, Server-Sent Events, HTTP Long-Polling
- **Bidirectional**: gRPC bidirectional streaming, WebSockets

### 7.4 Protocol Selection Trade-offs

#### 1. Latency Analysis
- **REST**: Higher latency (HTTP overhead per request/response)
- **gRPC**: Lower latency (HTTP/2 multiplexing + binary Protobuf)
- **WebSockets**: Very low latency (persistent duplex connection)
- **SSE**: Low latency push (text-only, unidirectional)
- **Long-polling**: Highest latency (depends on re-requests and wasted cycles)

#### 2. Scalability Characteristics
- **REST**: Easiest to scale horizontally (stateless nature)
- **gRPC**: Scalable, but long-lived streams consume resources
  - Requires connection load balancing (e.g., Envoy)
- **WebSockets**: Harder to scale
  - Millions of long-lived connections need stateful load balancers or sticky sessions
- **SSE**: One TCP connection per client (simpler than WebSockets but still resource-intensive)
- **Long-polling**: Worst scalability (half-open requests + repeated handshakes)

#### 3. Browser-Friendliness
- **REST**: Natively supported everywhere
- **gRPC**: Needs special tooling for browsers (gRPC-Web)
- **SSE**: Native EventSource API in browsers (super easy)
- **WebSockets**: Native WebSocket API in browsers
- **Long-polling**: Works everywhere (good fallback for legacy)

#### 4. Type Safety
- **REST**: JSON/XML (human-readable but weakly typed, schema drift issues)
- **gRPC**: Strong type safety via Protobuf (enforced schema contract)
- **SSE**: Text-only (schema validation handled in app layer)
- **WebSockets**: No schema enforcement (define your own protocol on top)
- **Long-polling**: Same as REST (depends on payload format)

### 7.5 Protocol Decision Matrix

#### Internal APIs (Service-to-Service)
**Prefer gRPC for:**
- ✅ Strong typing (Protobuf schema contract)
- ✅ Low latency (HTTP/2 multiplexing)
- ✅ Great for microservices architecture
- ✅ Streaming capabilities (client, server, bidirectional)

#### External APIs (Browser/Mobile Clients)
**Choose based on use case:**

- **REST**: CRUD-style operations, request/response patterns
- **SSE**: Notifications, real-time feeds, dashboards
- **WebSockets**: Chats, gaming, collaborative editing
- **Long-polling**: Fallback when SSE/WebSockets not available

#### Quick Reference Table

| Factor | REST | gRPC | WebSockets | SSE | Long-Polling |
|--------|------|------|------------|-----|--------------|
| **Latency** | High | Low | Very Low | Low | Highest |
| **Scalability** | Excellent | Good* | Challenging | Good | Poor |
| **Browser Support** | Native | gRPC-Web | Native | Native | Universal |
| **Type Safety** | Weak | Strong | None | Weak | Weak |
| **Connection** | Stateless | Stateful | Stateful | Stateful | Semi-stateful |

*Requires proper load balancing for streams

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
- [x] **HLD Foundations** - Complete
- [x] **Database Design** - Complete
- [ ] **API Design** - Pending
- [x] **Database Comparisons** - Complete
- [ ] **Transactions & Concurrency** - Pending
- [ ] **Messaging / Event Systems** - Pending
- [x] **API & Communication Patterns** - Complete
- [ ] **High-Concurrency Systems** - Pending
- [ ] **Streaming / Media Systems** - Pending
- [ ] **System Migration** - Pending
- [ ] **Billing / Reconciliation Systems** - Pending