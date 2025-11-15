# Database Selection Guide

Reference material for the database-advisor agent. Consult this when evaluating database technology options.

## Database Categories & Use Cases

### Relational (ACID)
**Platforms**: SQL Server, PostgreSQL, MySQL, Oracle, Azure SQL Database

**Characteristics**:
- Strong consistency guarantees (ACID transactions)
- Complex queries with JOINs across normalized tables
- Mature query optimization and indexing strategies
- Schema enforcement and referential integrity

**Best for**:
- Financial systems requiring ACID guarantees
- Applications with complex relational data models
- Transactional workloads (OLTP)
- When data integrity is non-negotiable
- Teams with strong SQL expertise

**Not ideal for**:
- Massive write throughput (>100K writes/sec)
- Highly denormalized data with flexible schema
- Global distribution with multi-master writes
- When you need sub-millisecond latency at global scale

---

### Document (NoSQL)
**Platforms**: Cosmos DB, MongoDB, DynamoDB, Couchbase

**Characteristics**:
- Flexible schema (schema-on-read)
- Nested/hierarchical data stored as documents
- Horizontal scalability
- Eventual or tunable consistency

**Best for**:
- Rapidly evolving schemas
- Hierarchical data (user profiles, product catalogs)
- Content management systems
- Mobile/web applications with denormalized data
- Global distribution requirements

**Not ideal for**:
- Complex multi-document transactions
- Heavy relational joins across collections
- When strong consistency is critical
- Analytics requiring complex aggregations

---

### Key-Value
**Platforms**: Redis, DynamoDB (key-value mode), Cosmos DB (key-value API), Memcached

**Characteristics**:
- Ultra-fast lookups by key (sub-millisecond)
- Simple data model (key → value)
- In-memory or persistent storage
- Linear scalability

**Best for**:
- Caching layers
- Session management
- Real-time leaderboards/counters
- Shopping carts
- Rate limiting

**Not ideal for**:
- Complex queries or secondary indexes
- When you need to query by value attributes
- Reporting or analytics
- Data requiring strong consistency across keys

---

### Wide-Column
**Platforms**: Cassandra, HBase, Azure Cosmos DB (Cassandra API), Bigtable

**Characteristics**:
- Massive write throughput (millions/sec)
- Column-family data model
- Tunable consistency
- Linear scalability

**Best for**:
- Time-series data (IoT, logs, metrics)
- High-volume write workloads
- Append-heavy operations
- When eventual consistency is acceptable
- Need for linear horizontal scaling

**Not ideal for**:
- Complex queries or JOINs
- Strong consistency requirements
- Low-latency random reads (read-optimized)
- Small datasets (<1TB)

---

### Time-Series
**Platforms**: InfluxDB, TimescaleDB, Azure Data Explorer, Prometheus

**Characteristics**:
- Optimized for timestamp-indexed data
- Efficient aggregations and downsampling
- Data retention policies (TTL)
- Purpose-built compression

**Best for**:
- IoT telemetry data
- Application performance monitoring (APM)
- Financial market data
- Server/infrastructure metrics
- When time-range queries are primary access pattern

**Not ideal for**:
- General-purpose transactional data
- Complex relational models
- When time is not primary index
- Real-time individual record updates

---

### Graph
**Platforms**: Neo4j, Cosmos DB (Gremlin API), Amazon Neptune

**Characteristics**:
- Relationship-first data model
- Efficient graph traversals
- Pattern matching queries
- Native graph storage

**Best for**:
- Social networks (connections, followers)
- Recommendation engines
- Fraud detection (relationship patterns)
- Knowledge graphs
- Network analysis

**Not ideal for**:
- Simple hierarchical data
- When relationships are not primary concern
- Aggregate analytics
- High-volume write workloads

---

### Search
**Platforms**: Azure Cognitive Search, Elasticsearch, Solr, MeiliSearch

**Characteristics**:
- Full-text search with relevance scoring
- Faceted navigation and filtering
- Inverted indexes optimized for search
- Near real-time indexing

**Best for**:
- Product search and discovery
- Content search (documents, articles)
- Log analysis and searching
- Autocomplete and typeahead
- When search quality (relevance) matters

**Not ideal for**:
- Primary data store (use as secondary index)
- ACID transactions
- Strong consistency requirements
- When you just need exact matching (use relational)

---

## CAP Theorem & Consistency Models

### CAP Theorem Fundamentals

**CAP Theorem**: In the presence of a network partition (P), a distributed system must choose between:
- **Consistency (C)**: Every read sees the most recent write
- **Availability (A)**: Every request receives a response (success or failure)

**Key insight**: You can have CP (consistency + partition tolerance) OR AP (availability + partition tolerance), but not all three simultaneously during a partition.

### Consistency Models (Strongest to Weakest)

#### 1. Strong Consistency (Linearizability)
**What it means**: Every read sees the most recent write globally. Reads reflect writes immediately.

**Examples**:
- SQL Server (single instance)
- Cosmos DB (Strong consistency level)
- PostgreSQL (default)

**Use when**:
- Financial transactions (account balances)
- Inventory management (prevent overselling)
- Booking systems (prevent double-booking)

**Trade-offs**:
- ✅ Simplest programming model (no stale reads)
- ❌ Higher latency (coordination required)
- ❌ Lower availability during partitions
- ❌ Doesn't scale well across regions

---

#### 2. Bounded Staleness
**What it means**: Reads lag behind writes by at most K versions or T time. Guaranteed maximum staleness.

**Examples**:
- Cosmos DB (Bounded Staleness)
- Some distributed SQL systems

**Use when**:
- Multi-region deployments needing consistency guarantees
- Can tolerate defined lag (e.g., 5 seconds, 100K operations)
- Need predictable consistency with better availability than strong

**Trade-offs**:
- ✅ Predictable staleness bounds
- ✅ Better availability than strong consistency
- ❌ More complex than strong consistency
- ❌ Requires monitoring of lag

---

#### 3. Session Consistency
**What it means**: Within a single session, reads are consistent. You always see your own writes.

**Examples**:
- Cosmos DB (Session - default)
- Many distributed systems

**Use when**:
- User-facing applications (users see their own updates)
- Shopping carts, user profiles, preferences
- Most web applications

**Trade-offs**:
- ✅ Great for user experience (no stale reads for own data)
- ✅ Good performance and availability
- ❌ Different users may see different data temporarily
- ❌ Not suitable for cross-user consistency needs

---

#### 4. Consistent Prefix
**What it means**: Reads never see out-of-order writes. If writes happen in order A→B→C, you'll never see C before B.

**Examples**:
- Cosmos DB (Consistent Prefix)
- Systems with causal ordering

**Use when**:
- Event streams or logs (order matters)
- Conversation threads (messages in order)
- Append-only workloads

**Trade-offs**:
- ✅ Maintains causality and ordering
- ✅ Better availability than stronger models
- ❌ May still see stale data (just in correct order)
- ❌ Not suitable when need latest data

---

#### 5. Eventual Consistency
**What it means**: All replicas converge eventually. No guarantees on when. Temporary inconsistency is possible.

**Examples**:
- Cosmos DB (Eventual)
- DynamoDB (default)
- Cassandra (default)
- DNS (classic example)

**Use when**:
- Read-heavy workloads where staleness is acceptable
- Global content delivery (CDN, product catalogs)
- Social media feeds (stale is okay)
- Maximum availability and performance needed

**Trade-offs**:
- ✅ Highest availability and lowest latency
- ✅ Best for geo-distributed writes
- ❌ Complex application logic (handle stale reads, conflicts)
- ❌ Not suitable for financial or booking systems

---

## Workload Pattern Analysis

### OLTP (Online Transaction Processing)

**Characteristics**:
- High concurrency (thousands of concurrent users)
- Low latency (<10ms for reads, <50ms for writes)
- Small transactions (single-row or few-row updates)
- Strong consistency usually required
- Point lookups and range scans

**Query patterns**:
- `SELECT * FROM Users WHERE UserId = ?`
- `UPDATE Orders SET Status = 'Shipped' WHERE OrderId = ?`
- `INSERT INTO Transactions (UserId, Amount) VALUES (?, ?)`

**Examples**:
- E-commerce checkout
- Banking transactions
- User authentication
- Order management

**Best database fit**:
- **SQL Server**: Excellent OLTP performance, ACID guarantees, In-Memory OLTP for extreme performance
- **Cosmos DB**: When need global distribution with low latency, session consistency acceptable
- **PostgreSQL**: Solid OLTP, strong consistency, lower cost

**Scaling approach**:
- Vertical scaling (bigger servers)
- Read replicas for read-heavy loads
- Caching layer (Redis)
- Connection pooling

---

### OLAP (Online Analytical Processing)

**Characteristics**:
- Complex aggregations and joins
- Large scans (millions of rows)
- Batch processing acceptable
- Eventual consistency often acceptable
- Denormalized data models (star/snowflake schemas)

**Query patterns**:
- `SELECT Region, SUM(Revenue) FROM Sales GROUP BY Region`
- `SELECT * FROM FactSales JOIN DimDate JOIN DimProduct WHERE Year = 2024`
- Complex window functions, CTEs

**Examples**:
- Business intelligence dashboards
- Data warehousing
- Reporting and analytics
- Historical trend analysis

**Best database fit**:
- **Azure Synapse Analytics**: Purpose-built for OLAP, massive parallelism
- **SQL Server with Columnstore Indexes**: OLAP on existing SQL Server infrastructure
- **Cosmos DB Analytical Store**: NoSQL data with analytical queries

**Scaling approach**:
- MPP (Massively Parallel Processing)
- Columnar storage
- Partitioning by time windows
- Pre-aggregated materialized views

---

### HTAP (Hybrid Transaction/Analytical Processing)

**Characteristics**:
- Need both OLTP and OLAP on same data
- Real-time analytics on operational data
- Avoid ETL lag (want up-to-the-second reports)

**Examples**:
- Real-time dashboards showing live data
- Fraud detection (real-time pattern analysis)
- Operational reporting
- Real-time personalization

**Best database fit**:
- **SQL Server with In-Memory OLTP + Columnstore**: Single database, dual optimization
- **Cosmos DB with Analytical Store**: OLTP on transactional store, OLAP on analytical store
- **Hybrid approach**: OLTP database + Change Data Capture (CDC) to OLAP system

**Trade-offs**:
- Single system: Simpler architecture, but potential resource contention
- Dual system: Optimized for each workload, but added complexity and ETL lag

---

### Write-Heavy Workloads

**Characteristics**:
- High ingestion rate (>100K writes/sec)
- Often append-only (immutable data)
- Typically time-series or log data
- Reads are often analytical (not real-time lookups)

**Examples**:
- IoT telemetry (sensor data)
- Application logs
- Clickstream analytics
- Financial market tick data

**Best database fit**:
- **Cosmos DB**: Purpose-built for write-heavy, millions of writes/sec, global scale
- **Cassandra/HBase**: Massive write throughput, linear scalability
- **InfluxDB/TimescaleDB**: If time-series specific
- **SQL Server**: Can handle moderate write loads (<50K writes/sec) with proper partitioning

**Optimization strategies**:
- Partition by time or write-key
- Batch writes when possible
- Use eventual consistency
- Asynchronous processing
- Separate write and read paths (CQRS)

---

### Read-Heavy Workloads

**Characteristics**:
- 80-95% reads, 5-20% writes
- Global distribution beneficial
- Caching highly effective
- Eventual consistency often acceptable

**Examples**:
- Product catalogs (e-commerce)
- Content delivery (articles, media)
- User profiles (read frequently, update rarely)
- Reference data

**Best database fit**:
- **Cosmos DB with geo-replication**: Global reads with low latency, writes to single region
- **SQL Server with read replicas**: Primary for writes, replicas for reads
- **Redis as cache + SQL Server**: Hot data in cache, cold data in database

**Optimization strategies**:
- CDN for static content
- Redis/Memcached for caching
- Read replicas in multiple regions
- Denormalized data models (avoid joins)
- Aggressive cache TTLs

---

## Cost Considerations

### Managed vs. Self-Hosted

**Managed (Azure SQL Database, Cosmos DB, etc.)**:
- ✅ No operational overhead (patching, backups, HA automatic)
- ✅ Faster time to production
- ✅ Built-in monitoring and scaling
- ❌ Higher per-unit cost
- ❌ Less control over configuration
- ❌ Potential vendor lock-in

**Self-Hosted (SQL Server on VMs, Kubernetes, etc.)**:
- ✅ Lower per-unit cost at scale
- ✅ Full configuration control
- ✅ No vendor lock-in
- ❌ Operational overhead (team needs expertise)
- ❌ Slower to production
- ❌ Responsible for HA, DR, monitoring

**Decision point**: Managed is usually better unless:
- Large scale where cost savings justify operational overhead
- Specific compliance/regulatory requirements
- Team already has deep operational expertise

---

### Total Cost of Ownership (TCO)

**Include all costs**:
1. **Infrastructure**: Compute, storage, network, backups
2. **Licensing**: Database licenses (SQL Server Enterprise vs Standard)
3. **Operational**: Staff time, on-call, monitoring tools
4. **Training**: Team learning curve for new technology
5. **Migration**: Cost to migrate if choice was wrong

**Cosmos DB cost model**:
- Charged by Request Units (RUs) consumed
- Predictable for steady workloads
- Can spike unexpectedly with inefficient queries
- Reserved capacity for discounts

**SQL Server cost model**:
- DTU or vCore-based (Azure SQL)
- Predictable monthly cost
- Scale up/down as needed
- Licenses included (managed) or BYOL

---

## When to Consider Re-platforming

Re-platforming is expensive and risky. Only consider when:

### Valid reasons:
✅ **Fundamental workload mismatch**: PostgreSQL for 500K writes/sec IoT data → Cosmos DB
✅ **Can't scale further**: Hit platform limits, can't vertical scale anymore
✅ **Global distribution needed**: Single-region SQL → multi-region Cosmos DB
✅ **Cost is unsustainable**: Massive PostgreSQL instance → purpose-built time-series DB
✅ **Consistency model wrong**: Need eventual consistency but using strong (or vice versa)

### Invalid reasons (fix instead):
❌ "Technology is old" (if it works, don't change)
❌ "NoSQL is cooler" (hype-driven)
❌ Haven't optimized current platform (add indexes, tune queries first)
❌ Haven't tried caching layer
❌ Team wants to learn new tech (train on non-critical system first)

### Re-platforming checklist:
- [ ] Exhausted optimization options on current platform
- [ ] Measured actual bottleneck (not guessing)
- [ ] Estimated migration cost and risk
- [ ] New platform provably better for workload
- [ ] Team capable of operating new platform
- [ ] Budget approved for migration
- [ ] Rollback plan if migration fails

---

## Technology-Specific Notes

### When to use SQL Server:
- Team knows SQL Server well
- Windows/.NET ecosystem
- Need Enterprise features (Always On AG, In-Memory OLTP)
- OLTP or HTAP workloads
- Strong consistency required
- Azure integration (Azure SQL Database, Managed Instance)

### When to use Cosmos DB:
- Global distribution needed (multi-region reads/writes)
- Write-heavy workloads (>100K writes/sec)
- Need tunable consistency
- Flexible schema requirements
- Azure-native applications
- IoT, mobile, gaming, web applications

### When to use PostgreSQL:
- Open source preference
- Advanced SQL features (array types, JSON, full-text search)
- Cost-sensitive (lower TCO than commercial databases)
- Cross-platform (Linux, containers)
- Strong community and extensions

### When to use Redis:
- Only as caching layer, NOT primary data store
- Sub-millisecond latency required
- Session management, rate limiting, real-time features
- Always pair with persistent database

---

**Last updated**: 2025-12-08
**Related**: [Output Templates](./output-templates.md), [QA Checklist](./qa-checklist.md)
