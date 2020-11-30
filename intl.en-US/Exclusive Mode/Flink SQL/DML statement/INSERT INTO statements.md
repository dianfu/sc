---
keyword: INSERT INTO
---

# INSERT INTO statements

This topic describes the method and limits of executing INSERT INTO statements in Realtime Compute for Apache Flink.

## Operation limits

|Table type|Limit|
|----------|-----|
|Source table|Can be referenced in only FROM clauses and does not support INSERT statements.|
|Dimension table|Can be referenced in only JOIN statements and does not support INSERT statements.|
|Result table|Supports only INSERT statements.|
|View|Can be referenced in only FROM clauses.|

## Syntax

```
INSERT INTO tableName
[ (columnName[ , columnName]*) ]
queryStatement;
```

## Examples

```
INSERT INTO LargeOrders
SELECT * FROM Orders WHERE units > 1000;

INSERT INTO Orders(z,v)
SELECT c,d FROM OO;
```

**Note:**

-   In Realtime Compute for Apache Flink, a single SQL job can contain multiple data manipulation language \(DML\) operations, data sources, data destinations, and dimension tables. For example, a job file can contain two snippets of SQL statements for independent services. You can execute the SQL statements to write data to different data destinations.
-   Realtime Compute for Apache Flink does not allow you to execute a separate SELECT statement to query data. To execute a SELECT statement, you must include the SELECT statement in a CREATE VIEW or an INSERT INTO statement.
-   You can execute an INSERT INTO statement to update an existing record. For example, you can insert a key value into an ApsaraDB for RDS result table that contains a primary key. If the key value already exists, the existing record is updated. If the key value does not exist, a new key value is inserted.

