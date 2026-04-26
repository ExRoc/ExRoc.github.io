---
title: 2018 Multi-University Training Contest 9
category_bar: true
mathjax: true
date: 2018-08-21 08:51:49
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

## J. [Rikka with Stone-Paper-Scissors](https://acm.hdu.edu.cn/showproblem.php?pid=6424)

### 题意

> 定义函数 $f_a(n) = \underbrace{\log \cdots \log}_{a\ 个 \log}\ n$，其中 $\log$ 以 $2$ 为底。
>
> 对于正整数数组 $A$，定义函数
> $$
> g_A(n) = \begin{cases}
>             f_{A_1}(n) & |A| = 1 \\
>             f_{A_1}(n) ^ {g_B(n)} & |A| > 1
>          \end{cases}
> $$
> 其中 $B$ 为数组 $A$ 的长度为 $|A| - 1$ 的后缀子数组。
>
> 例如：$g_{[1, 2]}(n) = (\log n) ^ {(\log \log n)}$，$g_{[3, 1, 2]}(n) = (\log \log \log n)^{\left((\log n) ^ {\log \log n}\right)}$。
>
> 现在给定数组 $A$ 和 $B$，判断 $ k = \lim_{n \to +\infty} \frac{g_A(n)}{g_B(n)}$ 与 $0, +\infty$ 的大小关系。

### 输入

> 第一行为一个正整数 $T\ (1 \leq T \leq 10^5)$，接下去有 $T$ 组数据；每组数据第一行为两个正整数 $a, b\ (1 \leq a, b \leq 3)$，分别表示数组 $A$ 与 $B$ 的长度；第二行为 $a$ 个整数 $A_i$，第三行为 $b$ 个整数 $B_i\ (1 \leq A_i, B_i \leq 10^9)$，分别表示数组 $A$ 与 $B$ 中的数字。

### 输出

> 对于每组数据，若 $k = 0$ 则输出 $-1$，若 $k$ 趋于 $+\infty$ 则输出 $1$，否则输出 $0$。

### 题解

> 先考虑数组长度为 $3$ 时的一般情况，再考虑长度为 $1, 2$ 时如何兼容。
>
> 对 $g_A(n)$ 函数取 $\log$ 不影响其相对大小关系，而且还可以把指数项移到系数项，构造出 $f_A(n)$ 函数相加 / 相乘的形式：
> $$
> \begin{align*}
>     g_A(n) = & f_{A_1}(n) ^ {\left(f_{A_2}(n) ^ {f_{A_3}(n)}\right)} \\
>     \log(g_A(n)) = & \log\left(f_{A_1}(n) ^ {\left(f_{A_2}(n) ^ {f_{A_3}(n)}\right)}\right) \\
>                     = & f_{A_2}(n) ^ {f_{A_3}(n)} \times \log(f_{A_1}(n)) \\
>                     = & f_{A_2}(n) ^ {f_{A_3}(n)} \times f_{A_1 + 1}(n) \\
>     \log(\log(g_A(n))) = & \log\left(f_{A_2}(n) ^ {f_{A_3}(n)} \times f_{A_1 + 1}(n)\right) \\
>                         = & \log\left(f_{A_2}(n) ^ {f_{A_3}(n)}\right) + \log(f_{A_1 + 1}(n)) \\
>                         = & f_{A_3}(n) \times \log(f_{A_2}(n)) + f_{A_1 + 2}(n) \\
>                         = & f_{A_3}(n) \times f_{A_2 + 1}(n) + f_{A_1 + 2}(n)
> \end{align*}
> $$
> 当底数为 $2$ 时，取对数次数越多，结果越小，因此 $\lim_{n \to +\infty} \frac{f_{a + 1}(n)}{f_a(n)} = 0$，在大小比较中取决定作用的是 $a$ 小的那一项，对于有不同项的乘积相加不容易比较，我们利用 $\lim_{n \to +\infty, a \to +\infty} f_a(n) = 1$，给以上的 $f_{A_1 + 2}(n)$ 项配上系数 $f_{+\infty}(n)$ 不影响结果大小，因此当数组长度为 $3$ 时，我们可以比较以下公式中取 $\log$ 次数较小的项
> $$
> \begin{align*}
>     \log(\log(g_A(n))) = & f_{A_3}(n) \times f_{A_2 + 1}(n) + f_{A_1 + 2}(n) \times f_{+\infty}(n)
> \end{align*}
> $$
> **注意**：取 $\log$ 次数较少项在大小比较中起决定性作用，即使与之相乘的另一项次数更多，也不影响比较结果。
>
> 比较方式为：定义数组 $X_1 = [A_1 + 2, +\infty], X_2 = [\min(A_2 + 1, A_3), \max(A_2 + 1, A_3)], Y = [\min(X_1, X_2), \max(X_1, X_2)]$（其中 $X$ 数组使用字典序规则比较），比较由题给 $A, B$ 数组生成的 $Y_A$ 与 $Y_B$ 数组的字典序大小，小的则结果更大。
>
> 最后考虑数组长度为 $2$ 和 $1$ 的情况，发现上式若将 $A_3$ 使用 $+\infty$ 替换后 $\lim_{n \to +\infty} g_A(n)$ 结果与 $A = [A_1, A_2]$ 相同，$A_2$ 使用 $+\infty$ 替换也不影响比较结果，因此当数组长度小于 $3$ 时，可使用 $+\infty$ 填充数组长度到 $3$ 之后再用以上规则进行比较。

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 200000 + 100;
int T, a, b, x;
vector<vector<int>> A, B;
vector<int> vct;

vector<vector<int>> convert(const vector<int> &vct) {
        vector<vector<int>> p;
        if (vct.size() == 1) {
            p = {{vct[0] + 2, INT_MAX}, {INT_MAX, INT_MAX}};
        } else if (vct.size() == 2) {
            p  = {{vct[0] + 2, INT_MAX}, {vct[1] + 1, INT_MAX}};
        } else {
            p = {{vct[0] + 2, INT_MAX}, {vct[1] + 1, vct[2]}};
        }
        sort(p[0].begin(), p[0].end());
        sort(p[1].begin(), p[1].end());
        sort(p.begin(), p.end());
        return p;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    cin >> T;
    while (T--) {
        cin >> a >> b;
        vct.clear();
        for (int i = 0; i < a; ++i) {
            cin >> x;
            vct.push_back(x);
        }
        A = convert(vct);
        vct.clear();
        for (int i = 0; i < b; ++i) {
            cin >> x;
            vct.push_back(x);
        }
        B = convert(vct);
        if (A < B) {
            cout << 1 << endl;
        } else if (A > B) {
            cout << -1 << endl;
        } else {
            cout << 0 << endl;
        }
    }

    return 0;
}
```

## K. [Rikka with Badminton](https://acm.hdu.edu.cn/showproblem.php?pid=6425)

### 题意

> $n$ 个学生，有 $a$ 个学生没球（羽毛球）没拍（羽毛球拍），有 $b$ 个同学有一个拍但没球，有 $c$ 个同学有一个球但没拍，有 $d$ 个同学有一个球和一个拍，其中 $a + b + c + d = n$。
>
> 若要举办一场羽毛球赛，参加的同学拥有的球拍数总和至少为 $2$，羽毛球数至少为 $1$，所有同学均有可能参加或者不参加，共 $2^n$ 种可能，问所有可能中，有多少种可能无法举办比赛。

### 输入

> 第一行为一个整数 $T\ (1 \leq T \leq 10^3)$，表示有 $T$ 组数据，每组数据一行四个整数 $a, b, c, d\ (0 \leq a, b, c, d \leq 10^7, a + b + c + d \geq 1)$，含义如题。

### 输出

> 每组数据输出一行整数，为所求答案对 $998244353$ 取模的结果。

### 题解

> 穷举所有可能情况的方案数相加：
>
> | 没球没拍（$a$ 人） | 有拍没球（$b$ 人） | 有球没拍（$c$ 人） | 有球有拍（$d$ 人） |                    方案数                    |
> | :----------------: | :----------------: | :----------------: | :----------------: | :------------------------------------------: |
> |        不选        |        不选        |        不选        |        不选        |                     $1$                      |
> |     至少 1 人      |        不选        |        不选        |        不选        |                  $2^a - 1$                   |
> |     至少 1 人      |     至少 1 人      |        不选        |        不选        |      $(2^{a+b}-1) - (2^a-1) - (2^b-1)$       |
> |     至少 1 人      |        不选        |     至少 1 人      |        不选        |      $(2^{a+c}-1) - (2^a-1) - (2^c-1)$       |
> |     至少 1 人      |        不选        |        不选        |     恰好 1 人      |              $(2^a-1) \times d$              |
> |     至少 1 人      |     恰好 1 人      |     至少 1 人      |        不选        | $b \times [(2^{a+c}-1) - (2^a-1) - (2^c-1)]$ |
> |     至少 1 人      |        不选        |     至少 1 人      |     恰好 1 人      | $d \times [(2^{a+c}-1) - (2^a-1) - (2^c-1)]$ |
> |        不选        |     至少 1 人      |        不选        |        不选        |                  $2^b - 1$                   |
> |        不选        |     恰好 1 人      |     至少 1 人      |        不选        |             $b \times (2^c - 1)$             |
> |        不选        |        不选        |     至少 1 人      |        不选        |                  $2^c - 1$                   |
> |        不选        |        不选        |     至少 1 人      |     恰好 1 人      |             $d \times (2^c - 1)$             |
> |        不选        |        不选        |        不选        |     恰好 1 人      |                     $d$                      |
>
> 

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 200000 + 100;
const LL MOD = 998244353;
int T;
LL a, b, c, d;

LL fastPow(LL res, LL n) {
    LL ans;
    for (ans = 1; n != 0; n >>= 1) {
        if ((n & 1) != 0) {
            ans = ans * res % MOD;
        }
        res = res * res % MOD;
    }
    return ans;
}

LL two(LL n) {
    return fastPow(2, n);
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    cin >> T;
    while (T--) {
        cin >> a >> b >> c >> d;
        LL ans = 1
            + two(a) - 1
            + two(a + b) - 1 - (two(a) - 1) - (two(b) - 1)
            + two(a + c) - 1 - (two(a) - 1) - (two(c) - 1)
            + (two(a) - 1) * d % MOD
            + b * (two(a + c) - 1 - (two(a) - 1 ) - (two(c) - 1)) % MOD
            + d * (two(a + c) - 1 - (two(a) - 1) - (two(c) - 1)) % MOD
            + two(b) - 1
            + b * (two(c) - 1) % MOD
            + two(c) - 1
            + d * (two(c) - 1) % MOD
            + d;
        cout << (ans % MOD + MOD) % MOD << endl;
    }

    return 0;
}
```

