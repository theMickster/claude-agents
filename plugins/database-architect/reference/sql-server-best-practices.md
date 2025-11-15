# SQL Server Best Practices Reference

This document contains detailed best practices for SQL Server database architecture. Consult this as a reference when designing solutions.

## Schema Design Principles

- Apply proper normalization (2NF/3NF for OLTP, denormalization for OLAP)
- Use appropriate data types (avoid VARCHAR(MAX) when VARCHAR(100) suffices)
- Implement declarative referential integrity with foreign keys
- Design meaningful primary keys (identity columns vs natural keys)
- Add CHECK constraints for data validation
- Use UNIQUE constraints appropriately
- Consider temporal tables for audit requirements
- Design for future partitioning when tables will grow large

## T-SQL Best Practices

- Use parameterized queries to prevent SQL injection and enable plan reuse
- Leverage CTEs for readability, window functions for analytics
- Avoid cursors; use set-based operations
- Use EXISTS instead of COUNT(\*) > 0 for existence checks
- Implement proper error handling with TRY/CATCH blocks
- Use transactions with appropriate isolation levels
- Include WITH (NOLOCK) judiciously (understand dirty reads trade-off)
- Always specify column lists in INSERT statements
- Use schema-qualified object names (dbo.TableName)

## Indexing Strategy

- Design clustered index keys to be narrow, unique, ever-increasing, and immutable
- Design non-clustered indexes based on query patterns (WHERE, JOIN, ORDER BY)
- Use included columns for covering indexes
- Implement filtered indexes for queries on sparse data
- Consider columnstore indexes for analytical queries on fact tables
- Monitor index usage with DMVs (sys.dm_db_index_usage_stats)
- Balance index benefits vs maintenance overhead
- Document index rationale for team understanding

## Performance Optimization Workflow

1. Capture slow query with actual execution plan
2. Identify operators: scans (bad), seeks (good), key lookups (consider covering index)
3. Check for implicit conversions (data type mismatches)
4. Verify statistics are current (UPDATE STATISTICS)
5. Implement appropriate indexes
6. Test with realistic data volumes
7. Document changes and performance improvements

## When to Escalate

Seek clarification when:

- Business rules for constraints are ambiguous
- Performance requirements are not specified (response time, throughput)
- SQL Server version is not confirmed
- Security requirements (row-level security, encryption) are unclear
- High availability requirements need definition
- Conflicting requirements exist (normalization vs performance)

## Edge Cases to Consider

- **Large Data Volumes**: Partitioning, compression, archival strategies
- **High Concurrency**: Isolation levels, locking hints, optimistic concurrency
- **Complex Business Logic**: When to use stored procedures vs application code
- **Regulatory Compliance**: Audit tables, temporal tables, encryption
- **Multi-Region Deployments**: Replication, Always On across regions
- **Mixed Workloads**: Separating OLTP and OLAP (read replicas, columnstore)
