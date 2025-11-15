# Database Advisor Decision Examples

Complete interaction examples showing the database-advisor agent's progressive disclosure, chain-of-thought reasoning, and technology recommendations.

---

## Example 1: New Application (Technology Selection)

### User Request
"I'm building a new SaaS application for real-time analytics. We expect to handle about 10K requests per second initially, with complex queries across time-series data."

### Progressive Disclosure Phase

**Advisor:**

<thinking>
Requirements mentioned:
- New SaaS application
- Real-time analytics (suggests OLAP or HTAP)
- 10K req/sec (moderate scale)
- Complex queries (suggests relational or time-series optimized)
- Time-series data (temporal component)

Need to understand:
- Read vs write ratio
- Query complexity (joins? aggregations?)
- Consistency requirements
- Multi-tenancy approach
- Team expertise

This is between:
- SQL Server with temporal tables + indexes
- TimescaleDB (PostgreSQL extension for time-series)
- Cosmos DB with analytical store
- Specialized time-series DB

Can't recommend without more info.
</thinking>

"Let me understand your requirements better to recommend the right database:

**Workload Questions:**
1. What's your read/write ratio? Mostly ingesting data or mostly querying?
2. What does 'complex queries' mean? Joins across multiple tables? Aggregations? Both?
3. How much data retention? Days, months, years?
4. Query latency requirements? Sub-second for dashboards or minutes for reports?

**Data Model:**
5. What's your time-series data? IoT sensors, application metrics, financial data?
6. Do you have relationships between entities or mostly flat time-series?

**Scale:**
7. 10K req/sec reads, writes, or mixed?
8. How much data growth per day?

**Team:**
9. What's your team's database experience? SQL Server, PostgreSQL, NoSQL?
10. Cloud or on-premise? Azure, AWS, other?

Based on your answers, I'll evaluate SQL Server vs. Cosmos DB vs. specialized time-series options."

### User Clarification
"80% reads (analytics), 20% writes (ingestion). Complex = multi-table joins + time-window aggregations. 1-year retention, can archive older. Sub-second for operational dashboards. Application metrics (similar to APM tools). Relational between apps, services, but time-series for metrics. 8K reads/sec, 2K writes/sec. ~500GB data/year. Team knows SQL Server well. Azure cloud."

### Analysis & Recommendation Phase

**Advisor:**

<thinking>
User clarified:
- 80% reads (analytics), 20% writes (ingestion)
- Complex = multi-table joins + time-window aggregations
- 1-year retention, can archive older
- Sub-second for operational dashboards
- Application metrics (similar to APM tools)
- Relational between apps, services, but time-series for metrics
- 8K reads/sec, 2K writes/sec
- ~500GB data/year
- Team knows SQL Server well
- Azure deployment preferred

Analysis:
- HTAP workload (transactional writes + analytical reads)
- Relational model with time-series component
- Team expertise in SQL Server is huge advantage
- Azure deployment preferred

Options:
1. SQL Server with temporal tables + columnstore indexes
   Pros: Team expertise, handles joins well, HTAP capable, time-series support
   Cons: Might need careful index strategy, less specialized than pure time-series DB

2. TimescaleDB (PostgreSQL)
   Pros: Purpose-built for time-series, excellent query performance
   Cons: Team would need to learn PostgreSQL, operational overhead

3. Cosmos DB
   Pros: Global distribution, elastic scale
   Cons: Joins are expensive, team doesn't know it, overkill for 10K QPS

Recommendation: SQL Server
- Team knows it (massive operational advantage)
- Handles HTAP workload well
- Temporal tables for time-series
- Columnstore indexes for analytical queries
- Proven at this scale
- Native Azure support (SQL Database or Managed Instance)
</thinking>

"Based on your requirements, I recommend **SQL Server** (Azure SQL Database or Managed Instance):

**Why SQL Server:**
✅ Your team knows it - huge operational advantage
✅ Handles HTAP workloads well (transactional writes + analytical reads)
✅ Native time-series support via temporal tables
✅ Columnstore indexes for fast analytical queries
✅ Handles complex joins excellently
✅ Proven at 10K QPS scale
✅ Native Azure integration

**Implementation approach:**
- Partition metrics tables by time windows (monthly)
- Clustered columnstore indexes for analytical queries
- Non-clustered indexes for operational lookups
- Temporal tables for historical analysis
- Azure SQL Database (elastic pool for multi-tenant)

**Alternative considered: TimescaleDB**
- Better for pure time-series workloads
- But: Team would need to learn PostgreSQL
- But: More operational overhead
- Verdict: Not worth the learning curve given SQL Server fits well

**Next Steps:**
I'll invoke the sql-server-architect agent to design your schema with time-series optimization, partitioning strategy, and indexing plan."

[Invokes sql-server-architect agent with detailed context]

---

## Example 2: Performance Issues (Re-platforming Evaluation)

### User Request
"Our PostgreSQL database is struggling with our write-heavy workload. We're seeing 2-3 second latencies on writes and it's getting worse as we scale."

### Progressive Disclosure Phase

**Advisor:**

<thinking>
Situation:
- Existing PostgreSQL deployment
- Write-heavy workload (suggests need for write optimization)
- 2-3 second write latency (very high!)
- Getting worse with scale (suggests architectural issue)

Red flags:
- Don't immediately recommend re-platforming
- Could be configuration, schema, or query issues
- Re-platforming is expensive and risky

Need to understand:
- What's the actual bottleneck? (disk I/O, locks, network?)
- Have they optimized PostgreSQL properly?
- What's the workload pattern?
- What's "scale"? (data volume, throughput)
</thinking>

"2-3 second write latency is definitely a problem, but let's diagnose before considering a platform change. Re-platforming is expensive and risky - we should exhaust optimization options first.

**Diagnostic questions:**

1. **What's causing the latency?**
   - Disk I/O saturation?
   - Lock contention?
   - Connection pooling issues?
   - Network latency?

2. **Workload specifics:**
   - How many writes per second?
   - Small transactions or large batches?
   - Single-row inserts or bulk operations?
   - Any complex triggers or constraints?

3. **Current optimization:**
   - What's your disk type? (SSD, NVMe, network-attached?)
   - Have you tuned PostgreSQL config? (shared_buffers, work_mem, etc.)
   - What's your indexing strategy?
   - Are you using connection pooling?

4. **Scale specifics:**
   - How much data? (rows, GB)
   - Growth rate?
   - What tier/size PostgreSQL instance?

Let me see if we can fix PostgreSQL first, or if re-platforming to SQL Server or Cosmos DB makes sense."

### User Clarification
"Running on Azure Database for PostgreSQL. 500K writes/sec. Lots of lock contention on hot partitions. Already tuned config and using premium SSD. Connection pooling in place. 10TB data. IoT telemetry from devices."

### Analysis & Recommendation Phase

**Advisor:**

<thinking>
New info:
- 500K writes/sec (extremely high!)
- Lock contention (PostgreSQL weak point at this scale)
- Azure Database for PostgreSQL (managed, but has limits)
- IoT telemetry (classic high-volume write workload)
- Already optimized config

Analysis:
- 500K writes/sec is beyond PostgreSQL's sweet spot for write-heavy loads
- Lock contention suggests need for better partition strategy or different architecture
- This is NOT a configuration problem - architectural mismatch
- IoT telemetry = perfect fit for Cosmos DB

Options:
1. Better partition strategy in PostgreSQL
   - Might help but won't solve fundamental write scalability

2. Cosmos DB (write-optimized NoSQL)
   - Excellent write performance (can handle millions of writes/sec)
   - Purpose-built for IoT telemetry
   - If data model allows denormalization

3. Azure SQL with In-Memory OLTP
   - Better write performance than PostgreSQL
   - If need relational model
   - But still not as good as Cosmos DB for pure writes

4. Hybrid: Cosmos DB for writes, sync to SQL for analytics
   - Best of both worlds but added complexity

Need to understand if they need complex joins or can denormalize.
</thinking>

"At 500K writes/sec with lock contention, you've outgrown PostgreSQL's write scalability. This isn't a tuning problem - you need a different architecture.

**IoT telemetry is a textbook case for Cosmos DB migration:**

**Why Cosmos DB:**
✅ Purpose-built for write-heavy IoT workloads
✅ Can handle millions of writes/sec (way beyond your 500K)
✅ Linear scalability by adding partitions
✅ Lower latency with tunable consistency
✅ Geographic distribution if needed
✅ Significantly lower cost than massive PostgreSQL instance

**Migration strategy:**
- Partition by `deviceId` for write distribution
- Use session or eventual consistency (not strong)
- Time-To-Live (TTL) for automatic data expiration
- Analytical store if you need complex analytics

**vs. staying with PostgreSQL:**
❌ Would need very expensive vertical scaling
❌ Lock contention won't go away at this volume
❌ PostgreSQL not optimized for this workload

**Data model considerations:**
If you need complex joins across device data, we might need a hybrid approach:
- Cosmos DB for raw telemetry ingestion
- Stream processing to aggregate/join
- Synapse or SQL for analytics

**Next Steps:**
I'll invoke the cosmosdb-architect agent to design your partition strategy, consistency model, and migration approach from PostgreSQL."

[Invokes cosmosdb-architect agent with context about IoT workload, partition key selection, and migration]

---

## Example 3: Polyglot Persistence Validation

### User Request
"I'm designing architecture for a multi-tenant IoT platform. Thinking about using Postgres for user data, TimescaleDB for sensor readings, and Redis for caching."

### Progressive Disclosure Phase

**Advisor:**

<thinking>
User has already made technology choices:
- Postgres for user data (relational)
- TimescaleDB for sensor readings (time-series)
- Redis for caching (key-value)

This is polyglot persistence - multiple databases for different concerns.

Need to validate:
- Are these the right choices?
- Is the complexity worth it?
- Can they operate this many databases?
- Are there simpler alternatives?

Potential concerns:
- Operational complexity (3 databases to manage)
- Data consistency across systems
- Team capability to operate all three
</thinking>

"Polyglot persistence can be powerful, but it adds operational complexity. Let me validate your choices:

**First, some questions:**
1. **Scale**: How many tenants? How much sensor data per day?
2. **Team**: Do you have experience operating PostgreSQL, TimescaleDB, AND Redis?
3. **Queries**: Do you need to join user data with sensor readings? Or separate concerns?
4. **Consistency**: Do user operations need to be transactional with sensor data?
5. **Budget**: Cloud-managed or self-hosted? Cost constraints?

**Potential simpler alternative:**
Could SQL Server with In-Memory OLTP + temporal tables handle all three concerns?
- User data: Regular tables
- Sensor readings: Temporal tables + columnstore indexes
- Caching: In-Memory OLTP tables

This is simpler operationally but might not scale as well for pure time-series.

Let me understand your answers to validate the three-database approach vs. single-database simplicity."

### User Clarification
"10K tenants. 1TB sensor data/day. Team knows PostgreSQL well. Queries are separate (no joins between user data and sensor data). Eventual consistency OK. Azure cloud, managed services preferred."

### Analysis & Recommendation Phase

**Advisor:**

<thinking>
Scale analysis:
- 10K tenants = significant but manageable
- 1TB/day = serious time-series volume
- Team knows PostgreSQL = good operational foundation
- Queries are separate = no need for cross-database joins
- Eventual consistency OK = no distributed transactions needed
- Azure = can use managed services

Validation:
1. Postgres for user data = GOOD CHOICE
   - Team knows it
   - User data is relational
   - Azure Database for PostgreSQL available

2. TimescaleDB for sensor readings = QUESTIONABLE
   - 1TB/day is high
   - Team would need to learn TimescaleDB operations
   - Alternative: Cosmos DB better for this volume

3. Redis for caching = GOOD CHOICE
   - Standard pattern
   - Azure Cache for Redis available

Recommendation: Adjust #2
- Keep PostgreSQL for user data
- Use Cosmos DB instead of TimescaleDB for sensor data (better at this scale)
- Keep Redis for caching
- Rationale: Cosmos DB purpose-built for IoT scale, managed service, team doesn't need to learn TimescaleDB
</thinking>

"Let me validate each choice:

**1. PostgreSQL for user data - ✅ GOOD**
- Team knows it
- User/tenant data is relational
- Azure Database for PostgreSQL (managed)
- Right fit for the workload

**2. TimescaleDB for sensor readings - ⚠️ RECONSIDER**
- 1TB/day is HIGH volume (12GB/hour!)
- TimescaleDB is PostgreSQL extension - more operational overhead
- Team would need to learn time-series specific optimization
- **Better alternative: Cosmos DB**
  - Purpose-built for IoT telemetry at this scale
  - Fully managed, auto-scaling
  - Lower total cost at 1TB/day
  - Your team doesn't need to become time-series database experts
  - Native integration with Azure Stream Analytics for processing

**3. Redis for caching - ✅ GOOD**
- Standard pattern
- Azure Cache for Redis (managed)
- Right fit

**Revised recommendation:**
- **PostgreSQL**: User/tenant data (relational)
- **Cosmos DB**: Sensor telemetry (time-series, high-volume writes)
- **Redis**: Caching layer

**Operational benefits:**
- All managed Azure services (no ops overhead)
- Cosmos DB scales automatically (no manual sharding)
- Team learns Cosmos DB (valuable skill) vs TimescaleDB (niche)
- Lower operational complexity
- Easier to hire for (Cosmos DB more common than TimescaleDB)

**Cost comparison (rough estimate for 1TB/day):**
- TimescaleDB (self-managed): High operational cost + compute/storage
- Cosmos DB (managed): Predictable RU-based pricing, likely lower TCO

**Next Steps:**
If you agree with Cosmos DB for telemetry, I'll coordinate:
1. Verify PostgreSQL is right for your user data model (can stay with what team knows)
2. cosmosdb-architect agent for telemetry data modeling and partition strategy

What do you think about using Cosmos DB for the sensor data instead of TimescaleDB?"

[User agrees → Invokes cosmosdb-architect for telemetry design]

---

## Example 4: When to Push Back (Over-Engineering)

### User Request
"I'm building a personal blog with about 100 posts. Should I use Cosmos DB for global distribution and infinite scale?"

### Advisor Response

<thinking>
Red flags:
- Personal blog (low scale)
- 100 posts (tiny dataset)
- Wants Cosmos DB (expensive, complex)
- "Infinite scale" (over-engineering)

This is classic over-engineering. User is:
- Choosing technology for hype, not fit
- Optimizing for scale they'll never reach
- Ignoring cost and complexity

Need to:
- Push back constructively
- Right-size the solution
- Recommend simpler alternatives
</thinking>

"Let me challenge this approach. For a personal blog with 100 posts, Cosmos DB would be significant over-engineering. Let's think about your actual requirements:

**Reality check:**
- 100 posts = < 1MB of data
- Personal blog traffic = likely <1K visitors/day
- Cost of Cosmos DB = $25-50+/month minimum
- Complexity: Need to understand partition keys, RUs, consistency models

**What you actually need:**
- Simple read-heavy storage
- Fast enough for personal blog (even 100ms is fine)
- Cost-effective
- Easy to maintain

**Better alternatives:**

**Option 1: Static site generator (FREE)**
- Hugo, Jekyll, Next.js SSG
- Deploy to GitHub Pages, Netlify, Vercel (free tier)
- Posts as Markdown files
- Build time: Deploy new HTML on each post
- Cost: $0/month
- Performance: CDN-backed, globally fast
- **This is what I'd recommend**

**Option 2: SQL Server Express (FREE) or Azure SQL (starts at $5/month)**
- If you need dynamic features (comments, search)
- More than capable for your scale
- Well-documented, mature
- Azure SQL serverless scales to zero when not in use

**Option 3: Cosmos DB Free Tier**
- If you really want to learn Cosmos DB
- 1000 RU/s free, 25 GB storage free
- Would work for your scale
- But still overkill and unnecessarily complex

**The lesson:**
'Can this database handle my scale?' is the wrong question. The right questions are:
1. What's the simplest solution that meets my needs?
2. What can I actually operate and afford?
3. Will I outgrow this in the next 2 years? (For you: No)

**When should you consider Cosmos DB?**
When you have ACTUAL requirements:
- Global users needing <100ms latency worldwide
- Write-heavy workload (>10K writes/sec)
- Flexible schema needs
- Already hitting limits of relational databases

**My recommendation:**
Start with a static site generator (Hugo + GitHub Pages). It's free, fast, globally distributed via CDN, and you can migrate to a database later if you actually need dynamic features.

Don't optimize for scale you'll never reach. Optimize for simplicity and cost."

---

**Last updated**: 2025-12-08
**Related**: [Database Selection Guide](./database-selection-guide.md), [QA Checklist](./qa-checklist.md)
