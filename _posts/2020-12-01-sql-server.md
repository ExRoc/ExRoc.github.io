---
title: SQL Server - Iterate records without cursor
author: PipisCrew
date: 2020-12-01
categories: [sql]
toc: true
---

```js
--https://support.microsoft.com/en-us/help/111401/how-to-iterate-through-a-result-set-by-using-transact-sql-in-sql-serve

--sample
DECLARE @id INT

SELECT @id = MIN(OrderId) FROM Orders WHERE OrderId IN (1,2,3)

WHILE @id IS NOT NULL
BEGIN
		PRINT  @id
		SELECT * FROM Orders_Details WHERE OrderId =@id
		SELECT @id = MIN(OrderId) from Orders WHERE OrderId IN (1,2,3) AND OrderId > @id
END
```

origin - https://www.pipiscrew.com/?p=19416 sql-server-iterate-records-without-cursor