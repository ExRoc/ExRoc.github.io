---
title: OUTER APPLY Operator
author: PipisCrew
date: 2020-11-30
categories: [sql]
toc: true
---

```js
SELECT T.TeamId,
    T.Name TeamName,
    ISNULL(x.MatchCount, 0) x.MatchCount
FROM Teams T
	LEFT JOIN
	(
		SELECT COUNT(*) MatchCount
		FROM Matches
		WHERE Matches.TeamId = T.TeamId
		GROUP BY Matches.TeamId
	) x  ON x.TeamId = T.TeamId
```

this will result 
![](https://i.imgur.com/F9wgkcV.png)

**OUTER APPLY**
allows us to use parent table fields (join table-evaluated functions with SQL Tables). [more](https://codingsight.com/advanced-sql-cross-apply-and-outer-apply/)

```js
SELECT T.TeamId,
    T.Name TeamName,
    ISNULL(x.MatchCount, 0) x.MatchCount
FROM Teams T
	OUTER APPLY
	(
		SELECT COUNT(*) MatchCount
		FROM Matches
		WHERE Matches.TeamId = T.TeamId
		GROUP BY Matches.TeamId
	) x
```

using values from joins
```js
SELECT T.TeamId,
    T.Name TeamName,
    ISNULL(x.MatchCount, 0) x.MatchCount, 
    MyCounter.testCount
FROM Teams T
	OUTER APPLY
	(
		SELECT COUNT(*) MatchCount
		FROM Matches
		WHERE Matches.TeamId = T.TeamId
		GROUP BY Matches.TeamId
	) x
	OUTER APPLY
	(
		SELECT COUNT(*) ChampionCount
		FROM Champions
		WHERE Champions.TeamId = T.TeamId
		GROUP BY Champions.TeamId
	) y
	OUTER APPLY (
		SELECT 
		   CASE
			WHEN (ISNULL(ISNULL(x.MatchCount, 0) - ISNULL(y.ChampionCount, 0), 0)) < 0="" then="" 0="" else="" isnull(isnull(x.matchcount,="" 0)="" -="" isnull(y.championcount,="" 0),="" 0)="" end="" as="" testcount="" )="" mycounter="" ```="" **cross="" apply**="" is="" equivalent="" to="" an="" **inner="" join**="" **outer="" apply**="" is="" equivalent="" to="" a="" **left="" outer="" join**="" 0="" then="" 0="" else="" isnull(isnull(x.matchcount,="" 0)="" -="" isnull(y.championcount,="" 0),="" 0)="" end="" as="" testcount="" )="" mycounter="" ```="" **cross="" apply**="" is="" equivalent="" to="" an="" **inner="" join**="" **outer="" apply**="" is="" equivalent="" to="" a="" **left="" outer="">

origin - https://www.pipiscrew.com/?p=19060 outer-apply-operator