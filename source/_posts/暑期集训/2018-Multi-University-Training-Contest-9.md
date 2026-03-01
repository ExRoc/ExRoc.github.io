---
title: 2018 Multi-University Training Contest 9
category_bar: true
mathjax: true
date: 2018-08-21 21:10:00
updated: 2024-07-16 01:35:47
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第九场多校题解
---

链接：[2018 Multi-University Training Contest 9](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+9&source=1&searchmode=source)

过题数：3

排名：222

成员：孙昊哲、官展鹏

## A. [Rikka with Nash Equilibrium](https://acm.hdu.edu.cn/showproblem.php?pid=6415)

### 题意

> 在一个 $n$ 行 $m$ 列的矩阵中，定义“纳什均衡点 $(i,j)$”为：第 $i$ 行第 $j$ 列的数字比第 $i$ 行与第 $j$ 列所有数字都大。
>
> 问如果将 $[1, nm]$ 个不同的整数放置在这个矩阵内，且每个整数只出现一次的情况下，有多少种方案可以使得矩阵内只有一个纳什均衡点。

### 输入

> 第一行一个整数 $T\ (1\leq T\leq20)$，表示有 $T$ 组数据，接下去 $T$ 行，每行 $3$ 个整数 $n,m,K\ (1\leq n,m\leq80,1\leq k\leq10^9)$。
>
> 数据保证最多 $3$ 组数据的 $max(n,m)>50$。

### 输出

> 输出方案数对 $K$ 取余的结果。

### 题解

> 全局只有一个纳什均衡点，这个点只可能是数字 $nm$，从最大的数字往小的数字放，如果前 $i$ 大的数字已经放过了某 $j$ 行某 $k$ 列，则在放置第 $i+1$ 大的数字时只能放在这 $j$ 行于 $k$ 列内，否则第 $i+1$ 大的数字将成为第二个纳什均衡点。
>
> 设 $dp[i][j][k]$ 表示放前 $i$ 个数字，覆盖了 $j$ 行 $k$ 列的方案数，则有三种情况：
>
> 1. 前 $i-1$ 个数字覆盖了 $j-1$ 行 $k$ 列，则第 $i$ 个数字有 $k(n-(j-1))$ 种放法；
> 2. 前 $i-1$ 个数字覆盖了 $j$ 行 $k-1$ 列，则第 $i$ 个数字有 $j(m-(k-1))$ 种放法；
> 3. 前 $i-1$ 个数字覆盖了 $j$ 行 $k$ 列，则第 $i$ 个数字有 $jk-(i-1)$ 种放法。
>
> 状态转移方程为：
> $$
> dp[i][j][k]=dp[i-1][j-1][k]\times(n-j+1)\times k+dp[i-1][j][k-1]\times(m-k+1)\times j+dp[i-1][j][k]\times(j*k-i+1)
> $$

### 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 100;
int T, n, m, nm;
LL MOD;
LL dp[maxn * maxn][maxn][maxn];

int main() {
    ios::sync_with_stdio(false);

    cin >> T;
    while (T--) {
        cin >> n >> m >> MOD;
        nm = n * m;
        dp[1][1][1] = nm;
        for (int i = 2; i <= nm; ++i) {
            int topj = min(n, i);
            for (int j = 1; j <= topj; ++j) {
                int topk = min(m, i);
                for (int k = 1; k <= topk; ++k) {
                    dp[i][j][k] = 0;
                    if (j * k < i) {
                        continue;
                    }
                    dp[i][j][k] = (dp[i - 1][j - 1][k] * (n - j + 1) * k +
                                   dp[i - 1][j][k - 1] * (m - k + 1) * j +
                                   dp[i - 1][j][k] * (j * k - i + 1)) %
                                  MOD;
                }
            }
        }
        cout << dp[nm][n][m] << endl;
    }

    return 0;
}
```

## B. [Rikka with Seam](https://acm.hdu.edu.cn/showproblem.php?pid=6416)

### 题意

> 给定一个 $n$ 行 $m$ 列的 $01$ 矩阵，从每一行选择一个位置的数字进行删除，且相邻两行间被删除的数字下标差的绝对值不超过 $k$，问在执行删除后有多少种不同的剩余矩阵。

### 输入

> 第一行一个整数 $T\ (1\leq T\leq1000)$，表示有 $T$ 组数据，每组数据第一行为 $3$ 个整数 $n,m,k\ (2\leq n,m\leq2\times10^3,1\leq k\leq m)$，接下去 $n$ 行，每行是一个长度为 $m$ 的 $01$ 字符串。
>
> 数据保证最多 $5$ 组数据的 $max(n,m)>300$。

### 输出

> 输出不同的剩余矩阵数对 $998244353$ 取余的结果。

### 题解

> 若不考虑重复计算，设 $dp[i][j]$ 表示删掉第 $i$ 行第 $j$ 列的数字得到的方案数，状态转移方程为：
> $$
> dp[i][j]=\sum\limits_{p=\max(1,j-k)}^{\max(m,j+k)}dp[i-1][p]
> $$
> 每行按连续的 $0$ 与连续的 $1$ 进行分块，可以发现从连续相同的中任意删掉一位，得到的 $01$ 串都是完全一样的，所以若 $str[i][j]=str[i][j-1]$，就会有重复计算的结果。
>
> 定义 $sub[i][j]$ 表示 $dp[i][j]$ 与 $dp[i][j-1]$ 重复计算的方案数，则当 $str[i][j]\neq str[i][j-1]$ 时，$sub[i][j]=0$，否则：
> $$
> sub[i][j]=\sum\limits_{p=\max(1,j-k)}^{\min(m,j+k-1)}dp[i-1][p]-\sum\limits_{p=\max(2,j-k+1)}^{\min(m,j+k-1)}sub[i-1][p]
> $$
> 去重后方案数为：
> $$
> dp[i][j]=\sum\limits_{p=\max(1,j-k)}^{\min(m,j+k)}dp[i-1][p]-\sum\limits_{p=\max(2,j-k+1)}^{\min(m,j+k)}sub[i-1][p]
> $$
> 

### 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 2000 + 100;
const int MOD = 998244353;
int T, n, m, k;
char str[maxn][maxn];
int dp[maxn][maxn], sub[maxn][maxn];

int main() {
    ios::sync_with_stdio(false);

    scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d", &n, &m, &k);
        for (int i = 1; i <= n; ++i) {
            scanf("%s", str[i] + 1);
        }
        for (int j = 1; j <= m; ++j) {
            dp[1][j] = j;
            sub[1][j] = sub[1][j - 1];
            if (str[1][j] == str[1][j - 1]) {
                ++sub[1][j];
            }
        }

        for (int i = 2; i <= n; ++i) {
            for (int j = 1; j <= m; ++j) {
                int l = max(1, j - k);
                int r = min(m, j + k);
                dp[i][j] = (((dp[i - 1][r] - dp[i - 1][l - 1]) % MOD -
                             (sub[i - 1][r] - sub[i - 1][l]) % MOD) %
                                MOD +
                            MOD) %
                           MOD;
                if (str[i][j] != str[i][j - 1]) {
                    sub[i][j] = 0;
                    continue;
                }
                r = min(m, j + k - 1);
                sub[i][j] = (((dp[i - 1][r] - dp[i - 1][l - 1]) % MOD -
                              (sub[i - 1][r] - sub[i - 1][l]) % MOD) %
                                 MOD +
                             MOD) %
                            MOD;
            }
            for (int j = 1; j <= m; ++j) {
                dp[i][j] = (dp[i][j] + dp[i][j - 1]) % MOD;
                sub[i][j] = (sub[i][j] + sub[i][j - 1]) % MOD;
            }
        }
        printf("%d\n", ((dp[n][m] - sub[n][m]) % MOD + MOD) % MOD);
    }

    return 0;
}
```

## D. [Rikka with Stone-Paper-Scissors](https://acm.hdu.edu.cn/showproblem.php?pid=6418)

### 题意

> Yuta 与 Rikka 用打牌的方式玩剪刀石头布，三种牌分别为“是石头”、“剪刀”、“布”，Yuta 最开始有 $a$ 张剪刀、$b$ 张石头、$c$ 张布，Rikka 最开始有  $a'$ 张剪刀、$b'$ 张石头、$c'$ 张布。之前出过的牌后面不能再出，已知 Yuta 随机出牌，Rikka 按最优策略出牌，且后续会根据之前 Yuta 已出过的牌调整出牌策略。Rikka 赢一局加一分，输一局扣一分，平局不得分，问 Rikka 最终得分的期望是多少。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10^4)$，接下去有 $T$ 组数据，每组输入为 $6$ 个整数 $a,b,c,a',b',c'\ (0\leq a,b,c,a',b',c'\leq10^9,a+b+c=a'+b'+c'>0)$。

### 输出

> 对于每组输入，以分数形式输出 Rikka 的期望得分。

### 题解

> 不论后续 Rikka 如何采用最优策略，只要 Yuta 出的第一张牌是完全随机的，那 Rikka 后续的最优策略也将得到随机的结果，因此相当于 Rikka 也是随机出牌，计算得分期望为：
> $$
> \frac{a'\times(c-b)+b'\times(a-c)+c'\times(b-a)}{a+b+c}
> $$
> 

### 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 10 + 100;
int T;
LL fenzi, fenmu;
LL a[maxn], b[maxn];

int main() {
    ios::sync_with_stdio(false);

    cin >> T;
    while (T--) {
        fenmu = 0;
        for (int i = 0; i < 3; ++i) {
            cin >> a[i];
            fenmu += a[i];
        }
        for (int i = 0; i < 3; ++i) {
            cin >> b[i];
        }
        fenzi = 0;
        for (int i = 0; i < 3; ++i) {
            fenzi += b[i] * (a[(i + 2) % 3] - a[(i + 1) % 3]);
        }
        LL g = __gcd(fenzi, fenmu);
        fenzi /= g;
        fenmu /= g;
        if (fenzi == 0) {
            cout << 0 << endl;
            continue;
        }
        if (fenmu < 0) {
            fenmu = -fenmu;
            fenzi = -fenzi;
        }
        if (fenmu == 1) {
            cout << fenzi << endl;
            continue;
        }
        cout << fenzi << "/" << fenmu << endl;
    }

    return 0;
}
```

