# SQL Server Solution Output Format

When providing SQL Server solutions, structure your response using this format:

## 1. Executive Summary

Brief overview of the approach and key decisions (2-3 paragraphs maximum).

**Include:**
- What problem you're solving
- Your chosen approach
- Key trade-offs or decisions made

## 2. Schema/Query Implementation

Complete, production-ready T-SQL code with:

- Proper formatting and indentation
- Inline comments explaining complex logic
- Error handling (TRY/CATCH blocks where appropriate)
- Transaction management where appropriate

**Example:**
```sql
-- Create Orders table with temporal support
CREATE TABLE dbo.Orders (
    OrderID INT IDENTITY(1,1) NOT NULL,
    CustomerID INT NOT NULL,
    OrderDate DATETIME2 NOT NULL,
    Total DECIMAL(18,2) NOT NULL,
    -- Temporal columns
    SysStartTime DATETIME2 GENERATED ALWAYS AS ROW START NOT NULL,
    SysEndTime DATETIME2 GENERATED ALWAYS AS ROW END NOT NULL,
    PERIOD FOR SYSTEM_TIME (SysStartTime, SysEndTime),
    CONSTRAINT PK_Orders PRIMARY KEY CLUSTERED (OrderID),
    CONSTRAINT FK_Orders_Customer FOREIGN KEY (CustomerID)
        REFERENCES dbo.Customers(CustomerID)
)
WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.OrdersHistory));
```

## 3. Indexing Recommendations

Specific index definitions with rationale for each.

**Format:**
```sql
-- Index for customer order history queries
CREATE NONCLUSTERED INDEX IX_Orders_CustomerID_OrderDate
ON dbo.Orders (CustomerID, OrderDate DESC)
INCLUDE (Total, OrderStatus);
```

**Rationale:** Explain why this index helps specific queries.

## 4. Performance Considerations

- **Expected impact**: Quantify improvements where possible
- **Potential bottlenecks**: What could go wrong at scale
- **Scaling considerations**: How solution handles growth

## 5. Configuration Notes

Any SQL Server settings, feature flags, or prerequisites required:

- SQL Server version requirements
- Edition requirements (Enterprise/Standard features)
- Database settings (compatibility level, etc.)
- Server configuration options

## 6. Testing Guidance

How to validate the solution works correctly:

```sql
-- Test case examples
-- Verify temporal table is tracking history
INSERT INTO dbo.Orders (CustomerID, OrderDate, Total)
VALUES (1, GETDATE(), 100.00);

UPDATE dbo.Orders SET Total = 150.00 WHERE OrderID = 1;

-- Query history
SELECT * FROM dbo.Orders
FOR SYSTEM_TIME ALL
WHERE OrderID = 1;
```

## 7. Maintenance Notes

Ongoing care requirements:

- Index maintenance (rebuild vs reorganize schedules)
- Statistics update frequency
- History table management (partitioning, archival)
- Monitoring recommendations (DMVs, Extended Events)
