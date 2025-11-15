# SQL Server Solution Quality Assurance Checklist

Before finalizing any SQL Server solution, verify all items in this checklist.

## Schema & Design

- [ ] **Normalization**: Schema follows appropriate normalization (2NF/3NF for OLTP, denormalization for OLAP)
- [ ] **Constraints**: All constraints are properly defined (PK, FK, CHECK, UNIQUE)
- [ ] **Data Types**: Data types are appropriate and efficient (no VARCHAR(MAX) where VARCHAR(100) suffices)
- [ ] **Primary Keys**: Meaningful primary keys selected (identity vs natural keys with justification)
- [ ] **Foreign Keys**: Declarative referential integrity implemented where appropriate
- [ ] **Defaults**: Sensible default values where applicable

## Security

- [ ] **SQL Injection**: All queries use parameterized queries, no dynamic SQL vulnerabilities
- [ ] **Permissions**: Appropriate permissions model (principle of least privilege)
- [ ] **Sensitive Data**: PII/PHI properly handled (encryption, masking if needed)
- [ ] **Row-Level Security**: RLS implemented if required by business rules

## T-SQL & Code Quality

- [ ] **Error Handling**: TRY/CATCH blocks for all stored procedures
- [ ] **Transaction Management**: Proper transaction boundaries and isolation levels
- [ ] **Column Lists**: INSERT statements specify column lists (no INSERT without columns)
- [ ] **Schema Qualified**: All objects use schema-qualified names (dbo.TableName)
- [ ] **Set-Based**: No cursors; set-based operations used
- [ ] **Parameterized**: All queries parameterized for plan reuse and security

## Indexing & Performance

- [ ] **Clustered Index**: Appropriate clustered index (narrow, unique, ever-increasing, immutable)
- [ ] **Query Patterns**: Non-clustered indexes match query patterns (WHERE, JOIN, ORDER BY)
- [ ] **Covering Indexes**: INCLUDE columns used where appropriate
- [ ] **Index Overhead**: Index count is reasonable (balance query performance vs write overhead)
- [ ] **Execution Plans**: Tested with actual execution plans, no major warnings
- [ ] **Statistics**: Statistics are current or update strategy is defined

## Compatibility & Configuration

- [ ] **SQL Server Version**: Solution compatible with target SQL Server version
- [ ] **Edition Features**: Edition requirements documented (Enterprise vs Standard)
- [ ] **Compatibility Level**: Database compatibility level requirements specified
- [ ] **Feature Flags**: Any required SQL Server configuration options documented

## Impact Analysis

- [ ] **Existing Queries**: Impact on existing queries assessed
- [ ] **Existing Indexes**: Conflicts with existing indexes reviewed
- [ ] **Breaking Changes**: Any breaking changes identified and documented
- [ ] **Migration Path**: Upgrade/migration strategy defined if needed

## Documentation & Testing

- [ ] **Assumptions**: All assumptions documented
- [ ] **Prerequisites**: Prerequisites clearly listed
- [ ] **Test Cases**: Test cases provided for validation
- [ ] **Rollback Plan**: Rollback strategy documented for production changes
- [ ] **Maintenance**: Ongoing maintenance requirements specified

## Microsoft Best Practices

- [ ] **Official Guidance**: Solution aligns with Microsoft's documented best practices
- [ ] **Documentation URLs**: Relevant Microsoft documentation URLs cited
- [ ] **Known Limitations**: Any known limitations from Microsoft docs noted
- [ ] **Deprecation**: No deprecated features used without justification

## Production Readiness

- [ ] **Scale Testing**: Tested with realistic data volumes
- [ ] **Concurrency**: Tested under realistic concurrent load
- [ ] **Monitoring**: Monitoring and alerting strategy defined
- [ ] **Backup Strategy**: Backup and recovery implications documented
- [ ] **High Availability**: HA/DR implications considered and documented
