---
title: 涉及到 double 值比较的 eps 应用
category_bar: true
mathjax: true
date: 2020-08-24 23:42:00
updated: 2024-06-14 02:47:47
index_img: 
author: ExRoc
categories: ["博文"]
tags: ["ACM"]
description: 双精度小技巧
---

在一般涉及到 `double` 类型变量计算的时候，精度损失是必然的。`double` 的有效数字只有 16 位，在 16 位之后的数字就乱七八糟了，再加上乘除、`cmath` 等头文件的计算，精度又要损失几位，用 `cfloat` 头文件里的 `DBL_EPSILON` 作为精度误差可能不太管事了。
所以我们可以自己设置一个精度常量 `eps`，然后写几个 `inline` 的比较函数，这里以 $10^{-6}$ 为例，要用的时候，用到哪个打哪个,最后再拿一题小练一下。

```c++
const double eps = 1e-6;

inline bool zero(const double &x) {
    return fabs(x) < eps;
}

inline bool equal(const double &x, const double &y) {
    return fabs(x - y) < eps;
}

inline bool smaller(const double &x, const double &y) {
    return !equal(x, y) && x < y;
}

inline bool larger(const double &x, const double &y) {
    return !equal(x, y) && x > y;
}
```

## 题目链接

> [51Nod 1080: 两个数的平方和](https://www.51nod.com/Challenge/Problem.html#problemId=1080)

## 题意

> 给一个数 $N$，将 $N$ 表示为 $2$ 个整数 $i,j$ 的平方和 $(0\leq i\leq j)$，如果有多种表示，按照 $i$ 的递增序输出。其中 $N\in[1,10^9]$。

## 题解

> $i$ 从 $0$ 到 $\sqrt N$ 跑，计算 $\sqrt{N-i^2}$ 的值，如果为整数，按大小放到 `set<pair<int,int>>` 里面，再依次输出。

## 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const double eps = 1e-6;

inline bool equal(const double &x, const double &y) {
    return fabs(x - y) < eps;
}

int main() {
    #ifdef ExRoc
    freopen("test.txt", "r", stdin);
    #endif // ExRoc
    ios::sync_with_stdio(false);

    int N;
    double sq;
    set<pair<int, int> > ans;
    cin >> N;
    sq = sqrt(N);

    for(int i = 0; i < sq; ++i) {
        double j = sqrt(N - i * i);
        if(equal(j, floor(j))) {
            int Min = min(i, (int)j);
            int Max = max(i, (int)j);
            ans.insert(make_pair(Min, Max));
        }
    }
    if(ans.size() == 0) {
        cout << "No Solution" << endl;
    } else {
        set<pair<int, int> >::iterator it;
        for(it = ans.begin(); it != ans.end(); ++it) {
            cout << it->first << " " << it->second << endl;
        }
    }

    return 0;
}
```