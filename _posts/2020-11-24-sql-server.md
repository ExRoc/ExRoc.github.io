---
title: SQL Server - Sparse column
author: PipisCrew
date: 2020-11-24
categories: [sql]
toc: true
---

```js
CREATE TABLE Sparsed(ID INT IDENTITY(1,1),
FirstCol INT SPARSE,
SecondCol VARCHAR(100) SPARSE,
ThirdCol SmallDateTime SPARSE)
```

Sparse columns are better at managing NULL and ZERO values in SQL Server. It does not take any space in database at all. If column is created with SPARSE clause with it and it contains **ZERO or NULL** it will be **take lesser space** than regular column (without SPARSE clause).

Sparse columns **require more storage** space for nonnull values than the space required for identical data that is not marked SPARSE.

https://docs.microsoft.com/en-us/sql/relational-databases/tables/use-sparse-columns?view=sql-server-ver15

* * *

Find sparse columns in a dbase
```js
SELECT t.name AS 'TableName', c.name  AS 'ColumnName', y.name
FROM sys.columns c
JOIN        sys.tables  t   ON c.object_id = t.object_id
JOIN sys.types y ON y.user_type_id = c.user_type_id
WHERE is_sparse = 1
```

origin - https://www.pipiscrew.com/?p=19399 sql-server-sparse-column