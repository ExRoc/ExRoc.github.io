---
title: 2018-Multi-University-Training-Contest-8
category_bar: true
mathjax: true
date: 2018-08-16 21:10:00
updated: 2024-06-29 01:05:46
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第八场多校题解
---

链接：[2018 Multi-University Training Contest 8](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+8&source=1&searchmode=source)

过题数：3

排名：243

成员：孙昊哲、官展鹏、葛沛鑫

## A. [Character Encoding](https://acm.hdu.edu.cn/showproblem.php?pid=6397)

### 题意

> $m$ 个在 $[0,n-1]$ 范围内的整数，问有多少种方案使得 $m$ 个整数的和等于 $k$。其中，$2+1+1=4$ 与 $1+2+1=4$ 属于两种不同的方案。

### 输入

> 第一行一个整数 $T\ (1\leq T\leq100)$，表示有 $T$ 组数据，接下去 $T$ 行，每行 $3$ 个整数 $n,m,k\ (1\leq n,m\leq10^5,0\leq k\leq10^5)$。
>
> 数据保证所有 $n,m,k$ 各自的和不超过 $5\times10^6$。

### 输出

> 输出方案数对 $998244353$ 取余的结果。

### 题解

> 若对每个整数的取值无 $[0,n-1]$ 大小的限制，则 $m$ 个自然数的和为 $k$ 的方案数为 $C_{k+m-1}^{m-1}$（隔板法），若至少有 $1$ 个整数的取值大于等于 $n$，相当于取 $m$ 个自然数的和为 $k-n$，方案数为 $C_m^1 C_{k-n+m-1}^{m-1}$，同理若至少有 $i$ 个整数的取值大于等于 $n$，则方案数为 $C_m^i C_{k-in+m-1}^{m-1}$。
>
> 由容斥原理可得：所有 $m$ 个整数都在 $[0,n-1]$ 范围内的方案数等于：所有数字无最大值限制的方案数减去至少有 $1$ 个整数大于等于 $n$ 的方案数，再加上至少有 $2$ 个整数大于等于 $n$ 的方案数，再减去……直到最后加上（或者减去）至少有 $m$ 个整数大于等于 $n$ 的方案数，公式如下：
> $$
> ans=C_{k+m-1}^{m-1}+\sum\limits_{i=1}^m(-1)^iC_m^i C_{k-in+m-1}^{m-1}
> $$
> 

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const LL MOD = 998244353;
const int maxn = 200000 + 100;
LL T, n, m, k;
LL ans, flag;
LL inv[maxn], pro[maxn], invpro[maxn];

void Prepare_C() {
    inv[1] = 1;
    for (int i = 2; i < maxn; ++i) {
        inv[i] = (LL)(MOD - MOD / i) * inv[MOD % i] % MOD;
    }
    pro[0] = invpro[0] = 1;
    for (int i = 1; i < maxn; ++i) {
        pro[i] = pro[i - 1] * i % MOD;
        invpro[i] = invpro[i - 1] * inv[i] % MOD;
    }
}

LL get_C(LL n, LL m) {
    if(n < m) {
        return 0;
    }
    return pro[n] * invpro[m] % MOD * invpro[n - m] % MOD;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Prepare_C();
    scanf("%I64d", &T);
    while (T--) {
        scanf("%I64d%I64d%I64d", &n, &m, &k);
        ans = get_C(k + m - 1, m - 1);
        flag = 1;
        for (int i = 1; i <= m; ++i) {
            flag *= -1;
            ans = ((ans + get_C(k - i * n + m - 1, m - 1) * get_C(m, i) % MOD * flag) % MOD + MOD) % MOD;
        }
        printf("%I64d\n", ans);
    }

    return 0;
}
```

## D. [Parentheses Matrix](https://acm.hdu.edu.cn/showproblem.php?pid=6400)

### 题意

> 构造一个 $h$ 行 $w$ 列的只由小括号组成的矩阵，要求合法括号的行数与列数和最大。如下括号矩阵的第 $2$ 行与第 $2,4$ 列属于合法的括号匹配：
>
> ```bash
> )()(
> ()()
> ```

### 输入

> 第一行一个整数 $T\ (1\leq T\leq50)$，表示有 $T$ 组数据，接下去 $T$ 行，每行 $2$ 个整数 $h,w\ (1\leq h,w\leq200)$。
>

### 输出

> 按题意输出小括号矩阵，若有多个满足条件的矩阵，输出任意一个即可。

### 题解

> 若 $h,w$ 均为奇数，则无法构成任何一行（列）合法括号匹配。
>
> 若 $h,w$ 只有一个为偶数，则只能构造出奇数行（列）合法括号匹配。
>
> 讨论 $h,w$ 均为偶数的情况（假设 $h\leq w$）：
>
> - 若 $h=2$，则每列一个合法括号匹配可满足条件；
> - 若 $h=4$，则按如下方式可构造出最多 $w+h-3$ 组合法括号序列：
>
> ```bash
> ((((((((((
> ((((()))))
> )))))(((((
> ))))))))))
> ```
>
> - 若 $h\geq6$，则按如下方式可构造出最多 $w+h-4$ 组合法括号序列：
>
> ```bash
> ((((((((((
> ()()()()()
> (()()()())
> ()()()()()
> (()()()())
> ))))))))))
> ```

### 过题代码

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 200 + 100;
int T, n, m;
char str[maxn][maxn];

void construct(int n, int m) {
    for (int i = 0; i < n / 2; ++i) {
        for (int j = 0; j < m; ++j) {
            if (j < m / 2) {
                str[i][j] = '(';
            } else {
                str[i][j] = ')';
            }
        }
    }
    for (int i = n / 2; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            if (j % 2 == 0) {
                str[i][j] = '(';
            } else {
                str[i][j] = ')';
            }
        }
    }
}

void construct2(int n, int m) {
    for (int j = 0; j < m; ++j) {
        str[0][j] = '(';
        str[n - 1][j] = ')';
    }
    for (int i = 1; i < n - 1; ++i) {
        for (int j = 0; j < m; ++j) {
            if (j == 0) {
                str[i][j] = '(';
            } else if (j == m - 1) {
                str[i][j] = ')';
            } else if ((i + j) % 2 == 0) {
                str[i][j] = ')';
            } else {
                str[i][j] = '(';
            }
        }
    }
}

void print(bool flag) {
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            if (!flag) {
                printf("%c", str[i][j]);
            } else {
                printf("%c", str[j][i]);
            }
        }
        printf("\n");
    }
    printf("\n");
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    cin >> T;
    while (T--) {
        cin >> n >> m;
        if (n % 2 == 1) {
            construct(n, m);
            print(false);
        } else {
            if (m % 2 == 1) {
                construct(m, n);
                print(true);
            } else {
                if (n > m) {
                    if (m <= 4) {
                        construct(n, m);
                        print(false);
                    } else {
                        construct2(m, n);
                        print(true);
                    }
                } else {
                    if (n <= 4) {
                        construct(m, n);
                        print(true);
                    } else {
                        construct2(n, m);
                        print(false);
                    }
                }
            }
        }
    }

    return 0;
}
```

## E. [Magic Square](https://acm.hdu.edu.cn/showproblem.php?pid=6401)

### 题意

> 给定一个 $3\times3$ 的数字矩阵，矩阵的每一位为 $1$ 到 $9$，$9$ 个互不相等的数字，该矩阵可以分为 $4$ 个 $2\times2$ 的小矩阵，分别用 $1,2,3,4$ 表示，给出 $n$ 条指令，每条指令包含两部分：小矩阵对应的编号以及 `C` 或者 `R`，表示将对应的小矩阵进行逆时针或者顺时针旋转。
>
> 输出初始矩阵经过 $n$ 次操作后的最终状态。 

### 输入

> 第一行一个整数 $T\ (1\leq T\leq100)$，表示有 $T$ 组数据。每组数据的第一行为一个整数 $n\ (1\leq n\leq100)$，表示有 $n$ 条指令，后面跟着一个 $3\times3$ 的数字矩阵，接下去 $n$ 行每行 $2$ 个字符，第一个字符为一个数字，表示小矩阵编号，第二个字符为 `C` 或者 `R`，表示指令的旋转方向。

### 输出

> 输出 $3\times3$ 矩阵的最终状态。

### 题解

> 按题意模拟。

### 过题代码

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 100;
int T, n;
char cmd[maxn];
char str[maxn][maxn];
int swp[2][3][2] = {
    {{0, 1}, {1, 1}, {1, 0}},
    {{1, 0}, {1, 1}, {0, 1}}
};

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    cin >> T;
    while (T--) {
        cin >> n;
        for (int i = 0; i < 3; ++i) {
            cin >> str[i];
        }
        for (int i = 0; i < n; ++i) {
            cin >> cmd;
            int x = (cmd[0] - '0' - 1) / 2;
            int y = (cmd[0] - '0' - 1) % 2;
            int idx = cmd[1] == 'R';
            for (int i = 0; i < 3; ++i) {
                swap(str[x][y], str[x + swp[idx][i][0]][y + swp[idx][i][1]]);
            }
        }
        for (int i = 0; i < 3; ++i) {
            cout << str[i] << endl;
        }
    }

    return 0;
}
```

## J. [Taotao Picks Apples](https://acm.hdu.edu.cn/showproblem.php?pid=6406)

### 题意

> 有 $n$ 个苹果，高度分别为 $h_1,h_2,\cdots,h_n$，Taotao 只会从 $1$ 到 $n$ 依次取苹果，取苹果的条件为：当前为第一个苹果，或者当前苹果的高度高于上一次取到的苹果。有 $m$ 次独立的询问，每次将第 $p$ 个苹果的高度改为 $q$，问在修改高度后，按规则 Taotao 会取到多少个苹果。

### 输入

> 第一行一个整数 $T\ (1\leq T\leq10)$，表示有 $T$ 组数据，每组数据第一行为两个整数 $n,m\ (1\leq n,m\leq10^5)$，第二行 $n$ 个整数 $h_1,h_2,\cdots,h_n\ (1\leq h_i\leq10^9)$，接下去 $m$ 行每行两个整数 $p,q\ (1\leq p\leq n,1\leq q\leq10^9)$，含义如题。

### 输出

> 对于每次询问，输出一行整数，表示 Taotao 能取到的苹果数。

### 题解

> 预处理前缀数组 $pre_i$ 表示从第 $1$ 个到底 $i$ 个苹果高度都不修改的情况下按规则能取到的苹果数。
>
> 若每次能快速查询第 $p$ 个苹果高度修改为 $q$ 时，从第 $p$ 位往后（不考虑前缀情况）按规则能取到多少个苹果，这里需要维护一个单调递增的“当前后缀取苹果数”来支持快速的二分查询。可以发现若按下标从大到小询问，就能用单调栈维护这个递增序列。
>
> - 若 $q$ 大于 $preMax_{p-1}$（即前缀最大值），则答案为 $pre_{p-1}+1+suffix$（其中 $suffix$ 为查询到的后缀合法取苹果数）；
> - 若 $q$ 小于等于 $preMax_{p-1}$，则令 $q=preMax_{p-1}$ 后，再查询 $suffix$，答案为 $pre_{p-1}+suffix$。

### 过题代码

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
struct Node {
    int q, idx;
    Node() {}
    Node(int q, int idx) : q(q), idx(idx) {}
};

int T, n, m, p, q, last, top;
int num[maxn], pre[maxn], sta[maxn], ans[maxn], preMax[maxn];
vector<Node> vct[maxn];

int getAns(int idx, int x) {
    int low = -1;
    int high = top;
    int mid;
    while (high - low > 1) {
        mid = (high + low) >> 1;
        if (sta[mid] > x) {
            low = mid;
        } else {
            high = mid;
        }
    }
    return pre[idx - 1] + 1 + low + 1;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif

    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        last = 0;
        for (int i = 1; i <= n; ++i) {
            scanf("%d", &num[i]);
            if (num[i] > last) {
                last = num[i];
                pre[i] = pre[i - 1] + 1;
            } else {
                pre[i] = pre[i - 1];
            }
            vct[i].clear();
            preMax[i] = max(preMax[i - 1], num[i]);
        }
        for (int i = 0; i < m; ++i) {
            scanf("%d%d", &p, &q);
            vct[p].push_back(Node(q, i));
        }
        top = 0;
        for (int i = n; i >= 1; --i) {
            int len = vct[i].size();
            for (int j = 0; j < len; ++j) {
                q = vct[i][j].q;
                int idx = vct[i][j].idx;
                if (q <= preMax[i - 1]) {
                    ans[idx] = getAns(i, preMax[i - 1]) - 1;
                } else {
                    ans[idx] = getAns(i, vct[i][j].q);
                }
            }
            while (top != 0 && sta[top - 1] <= num[i]) {
                --top;
            }
            sta[top++] = num[i];
        }
        for (int i = 0; i < m; ++i) {
            printf("%d\n", ans[i]);
        }
    }

    return 0;
}
```

## K. [Pop the Balloons](https://acm.hdu.edu.cn/showproblem.php?pid=6407)

### 题意

> 你有一把强力的枪，在打中一个 $n$ 行 $m$ 列的气球矩阵中第 $i$ 行第 $j$ 列的气球时，与该气球同一行与同一列的所有气球都会被“风”吹爆，你每次必须打一个仍存在场上的气球，没有气球的位置不能打，问只打 $x$ 枪的情况下，有多少种可能的射击方式使得所有气球都被打爆，对 $x\in[1,k]$ 范围内所有值输出一个答案。

### 输入

> 第一行一个整数 $T\ (1\leq T\leq100)$，表示有 $T$ 组数据，每组数据第一行为三个整数 $n,m,k\ (1\leq n\leq12,1\leq m\leq20,1\leq k\leq20)$，含义如题，接下去 $n$ 行每行 $m$ 个字符，字符只可能为 `.` 或者 `Q`，为 `.` 表示这个位置没有气球，为 `Q` 表示这个位置有一个气球。

### 输出

> 每组数据输出 $k$ 行整数，每个整数对应一次题目询问的答案。

### 题解

> 用 $dp[i][j]$ 表示到第 $i$ 列时，状态为 $j$ 的方案数（由于行数小于列数，所以以列为第一维状态数更少），状态用 $3$ 进制数表示：
>
> - $j$ 的第 $k$ 位（这里的 $k$ 与题目输入的 $k$ 无关）为 $0$ 时，表示第 $k$ 行从未出现过气球，也不需要射击；
> - 为 $1$ 时，表示第 $k$ 行出现过气球，但未射击过；
> - 为 $2$ 时，表示第 $k$ 行出现过气球，且已射击过。
>
> 在遍历列时，可以枚举转移两种状态：
>
> - 该列不射击，则转移前一列原状态，加上之前未射击过，但该列中有气球的行，将这些行对应的状态置 $1$；
> - 射击该列的第 $k$ 行，则转移前一列原状态，并将第 $k$ 位置 $2$，其他之前未射击过，但该列中有气球的行，将这些行对应的状态置 $1$。
>
> 最后加上一些状态转移优化，以及答案爆 `long long`，可通过该题。

### 过题代码

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const LL Limit = 1000000000000LL;
LL T, n, m, k;
char str[12][50];
LL bit[531442][12], twoCnt[531442];
LL dp[21][531442], ans[21], pow3[21], fac[50];

int getBit(int x, int i) {
    return x / pow3[i] % 3;
}

void init() {
    fac[0] = 1;
    pow3[0] = 1;
    for (int i = 1; i <= 20; ++i) {
        pow3[i] = pow3[i - 1] * 3;
        fac[i] = fac[i - 1] * i;
    }
    for (int i = 0; i < 531442; ++i) {
        int cnt = 0;
        for (int j = 0; j < 12; ++j) {
            bit[i][j] = getBit(i, j);
            if (bit[i][j] == 2) {
                ++cnt;
            }
        }
        twoCnt[i] = cnt;
    }
}

int getBit2(int x, int i) {
    return bit[x][i];
}

int setBit(int x, int i, int y) {
    x -= getBit2(x, i) * pow3[i];
    x += y * pow3[i];
    return x;
}

string toString(int x) {
    string str;
    for (int i = 0; i < n; ++i) {
        str += (char)('0' + getBit2(x, i));
    }
    return str;
}

int main() {
    ios::sync_with_stdio(false);

    init();
    scanf("%I64d", &T);
    while (T--) {
        scanf("%I64d%I64d%I64d", &n, &m, &k);
        for (int i = 0; i < n; ++i) {
            scanf("%s", str[i]);
        }
        dp[0][0] = 1;
        for (int i = 1; i <= m; ++i) {
            memset(dp[i], 0, sizeof(LL) * pow3[n]);
        }
        for (int i = 0; i < m; ++i) {
            int now = i + 1;
            int pre = i;
            for (int j = 0; j < pow3[n]; ++j) {
                if (dp[pre][j] == 0 || twoCnt[j] > k) {
                    continue;
                }
                int jj = j;
                for (int k = 0; k < n; ++k) {
                    if (str[k][i] == 'Q' && getBit2(jj, k) != 2) {
                        jj = setBit(jj, k, 1);
                        dp[now][setBit(j, k, 2)] += dp[pre][j];
                    }
                }
                dp[now][jj] += dp[pre][j];
            }
        }
        memset(ans, 0, sizeof(ans));
        for (int i = 0; i < pow3[n]; ++i) {
            bool flag = true;
            int cnt = 0;
            for (int j = 0; j < n; ++j) {
                if (getBit2(i, j) == 1) {
                    flag = false;
                }
                if (getBit2(i, j) == 2) {
                    ++cnt;
                }
            }
            if (flag) {
                ans[cnt] += dp[m][i];
            }
        }
        for (int i = 1; i <= k; ++i) {
            __int128 Ans = (__int128)ans[i] * fac[i];
            if (Ans > Limit) {
                printf("%I64d%012I64d\n", (LL)(Ans / Limit), (LL)(Ans % Limit));
            } else {
                printf("%I64d\n", (LL)Ans);
            }
        }
    }

    return 0;
}
```

