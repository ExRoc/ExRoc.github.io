---
title: sum values from two different queries (sum totals of two queries)
author: PipisCrew
date: 2020-11-26
categories: [sql]
toc: true
---

```js
-- https://stackoverflow.com/a/6685597

SELECT SUM(hr) FROM
(
  Select sum(hours) as hr FROM table WHERE name='xxx' and Description='Worked'
  UNION ALL
  Select sum(hours2) as hr FROM table WHERE name='xxx' and Description2='Worked'
)a
```

origin - https://www.pipiscrew.com/?p=19406 sum-values-from-two-different-queries-sum-totals-of-two-queries