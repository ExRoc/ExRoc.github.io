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

