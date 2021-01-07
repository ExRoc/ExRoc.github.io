---
title: Assembly definition into SQL Server (Programmability / Assemblies)
author: PipisCrew
date: 2020-12-05
categories: [sql]
toc: true
---

```js
--src - https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-assembly-files-transact-sql
SELECT * FROM sys.assembly_files

--OR
SELECT af.name,
	CAST(content AS varchar(MAX))
FROM sys.assemblies AS a
JOIN sys.assembly_files AS af ON
	af.assembly_id = a.assembly_id

----------CREATE an assembly
--src - https://docs.microsoft.com/en-us/sql/relational-databases/clr-integration/assemblies/creating-an-assembly
CREATE ASSEMBLY HelloWorld FROM ‘path to file’ WITH PERMISSION_SET = SAFE
```

origin - https://www.pipiscrew.com/?p=19419 assembly-definition-into-sql-server-programmability-assemblies