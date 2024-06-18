---
title: 2018 Multi-University Training Contest 4
category_bar: true
mathjax: true
date: 2018-08-12 21:10:00
updated: 2024-06-19 02:11:02
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第四场多校题解
---

链接：[2018 Multi-University Training Contest 4](http://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+4&source=1&searchmode=source)

过题数：3

排名：333 / 802

成员：官展鹏，冯彦博，孙昊哲

## B. [Harvest of Apples](http://acm.hdu.edu.cn/showproblem.php?pid=6333)

### 题意

> 总共有 $n$ 个苹果，编号分别为 $1$ 到 $n$，问最多能摘到 $m$ 个苹果的总方案数。

### 输入

> 第一行包含一个整数 $T\ (1\leq T\leq10^5)$，接下去有 $T$ 组数据，每组数据包含两个整数 $n$ 和 $m\ (1\leq m\leq n\leq10^5)$。

### 输出

> 对每组数据，输出答案对 $10^9+7$ 取模的结果。

### 题解

> $n$ 个苹果最多能摘到 $m$ 个的方案数为 $\sum_{i=0}^mC_n^i$，由于这个公式的计算是 $O(m)$ 的，所以 $10^5$ 组数据显然超时，考虑离线莫队。
> 我们定义 $S_n^m=\sum_{i=0}^mC_n^i$，设 $C_n^{-1}=C_n^{n+1}=0$，则有：
>
> $$S_n^m=S_n^{m-1}+C_n^m$$
>
> $$\begin{align}
> S_n^m&=\sum_{i=0}^mC_n^i\\\\
> &=\sum_{i=0}^m(C_{n-1}^{m-1}+C_{n-1}^m)\\\\
> &=2\sum_{i=0}^mC_{n-1}^i-C_{n-1}^m\\\\
> &=2S_{n-1}^m-C_{n-1}^m
> \end{align}$$
>
> 于是将上标 $m$ 设为左区间 $l$，下标 $n$ 设为右区间 $r$，有以下转移方程：
>
> $$\begin{align}
> S_n^{m-1}&=S_n^m-C_n^m\\\\
> S_n^{m+1}&=S_n^m+C_n^{m+1}\\\\
> S_{n-1}^m&=\frac{1}{2}(S_n^m+C_{n-1}^m)\\\\
> S_{n+1}^m&=2S_n^m-C_n^m
> \end{align}$$
>
> 方程中的每一项都可以 $O(1)$ 求得，因此总的时间复杂度为 $O(n\sqrt n)$。

### 过题代码

```c++
#include <cstdio>
#include <cmath>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
const int MOD = 1000000007;
struct Node {
    int l, r;
    int Index;
};

int sq;

bool operator<(const Node &a, const Node &b) {
    return a.r / sq == b.r / sq? a.l < b.l: a.r < b.r;
}

int T, n, m, l, r, Max;
int ans[maxn];
Node node[maxn];
int inv[maxn], pro[maxn], invpro[maxn];

void Init() {
    inv[1] = 1;
    for (int i = 2; i < maxn; ++i) {
        inv[i] = (LL)(MOD - MOD / i) * inv[MOD % i] % MOD;
    }
    pro[0] = invpro[0] = 1;
    for (int i = 1; i < maxn; ++i) {
        pro[i] = (LL)pro[i - 1] * i % MOD;
        invpro[i] = (LL)invpro[i - 1] * inv[i] % MOD;
    }
}

int get_C(int n, int m) {
    if (n < m) {
        return 0;
    }
    return (LL)pro[n] * invpro[m] % MOD * invpro[n - m] % MOD;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Max = 0;
    Init();
    scanf("%d", &T);
    for (int i = 0; i < T; ++i) {
        scanf("%d%d", &node[i].r, &node[i].l);
        node[i].Index = i;
        Max = max(Max, node[i].r);
    }
    sq = sqrt(Max);
    sort(node, node + T);
    l = 1;
    r = 0;
    LL tmp = 1;
    LL ttmp;
    for (int i = 0; i < T; ++i) {
        while (r < node[i].r) {
            ttmp = tmp + tmp;
            if (ttmp >= MOD) {
                ttmp -= MOD;
            }
            tmp = ttmp - get_C(r, l);
            if (tmp < 0) {
                tmp += MOD;
            }
            ++r;
        }
        while (l > node[i].l) {
            tmp = tmp - get_C(r, l);
            if (tmp < 0) {
                tmp += MOD;
            }
            --l;
        }
        while (r > node[i].r) {
            ttmp = tmp + get_C(r - 1, l);
            if (ttmp >= MOD) {
                ttmp -= MOD;
            }
            tmp = (ttmp * inv[2]) % MOD;
            --r;
        }
        while (l < node[i].l) {
            tmp = tmp + get_C(r, l + 1);
            if (tmp >= MOD) {
                tmp -= MOD;
            }
            ++l;
        }
        ans[node[i].Index] = tmp;
    }
    for (int i = 0; i < T; ++i) {
        printf("%d\n", ans[i]);
    }

    return 0;
}
```

## D. [Nothing is Impossible](http://acm.hdu.edu.cn/showproblem.php?pid=6335)

### 题意

> 一个班上有 $m$ 个学生参加考试，试卷上总共有 $n$ 道题，每道题有 $a_i$ 个正确选项和 $b_i$ 个错误选项，每个学生每道题只能从 $a_i+b_i$ 个选项中选择一个选项作为答案，每个学生都不知道正确答案，但是这个教室内的学生在考试期间可以相互讨论，且他们十分团结，他们想让班上的最高分的做对的题数最多，问他们在采取最优策略下，能够做对的最多的题数。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq100)$，接下去有 $T$ 组数据，每组数据第一行为两个整数 $n,m\ (1\leq n\leq100,1\leq m\leq10^9)$，接下去 $n$ 行每行两个整数 $a_i,b_i\ (1\leq b_i\leq100,a_i=1)$。

### 输出

> 输出最高分能够做对的最多的题数。

### 题解

> 个人认为这题应该加一个“最高分至少能够做对的题数”，即在他们所有人运气都最坏的情况下，能够做对的题数的最大值，最优策略就是让所有人的答案填法按照 $b_i$ 进制一个一个地填上去，如果能够达到第 $i$ 位，则说明第 $i$ 位以下的所有情况都已经填上去，也就至少能够保证做对 $i-1$ 道题，为了让位数增长最快，应该让 $b_i$ 值小的作为低位。

### 过题代码

```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100 + 100;
struct Node {
    int a, b;
};

bool operator<(const Node &a, const Node &b) {
    return a.b < b.b;
}

int T, n, m, ans;
Node node[maxn];
int num[maxn];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        ans = 0;
        scanf("%d%d", &n, &m);
        for (int i = 0; i < n; ++i) {
            scanf("%d%d", &node[i].a, &node[i].b);
        }
        sort(node, node + n);
        for (int i = 0; i < n; ++i) {
            if (m > node[i].b) {
                ++ans;
            }
            m /= (node[i].b + 1);
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## E. [Matrix from Arrays](http://acm.hdu.edu.cn/showproblem.php?pid=6336)

### 题意

> 给定一个长度为 $L$ 的数组 $A_0,A_1\cdots A_{L-1}$，用以下代码生成一个无穷大矩阵：
```c++
int cursor = 0;
for (int i = 0; ; ++i) {
    for (int j = 0; j <= i; ++j) { 
        M[j][i - j] = A[cursor];
        cursor = (cursor + 1) % L;
    }
}
```
> 有 $Q$ 次询问，每次询问为从第 $x_0$ 行 $y_0$ 列到第 $x_1$ 行 $y_1$ 列的子矩阵中数字的和。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq100)$，接下有 $T$ 组数据，每组数据第一行为一个整数 $L\ (1\leq L\leq10)$，第二行为 $L$ 个整数 $A_0,A_1\cdots,A_{L-1}\ (1\leq A_i\leq100)$，第三行为一个整数 $Q\ (1\leq Q\leq100)$，接下去 $Q$ 行每行四个整数 $x_0,y_0,x_1,y_1\ (0\leq x_0\leq x_1\leq10^8,0\leq y_0\leq y_1\leq10^8)$。

### 输出

> 对于每次询问，输出结果。

### 题解

> 打表可以发现矩阵的循环节为 $2L$，因此可以只打出 $2L\times2L$ 的矩阵，就可以通过循环节求出所有矩阵的值，设 $S_{x,y}$ 为无穷大矩阵前 $x$ 行 $y$ 列的和，则所求子矩阵的和为
>
> $$S_{x_1,y_1}-S_{x_0-1,y_1}-S_{x_1,y_0-1}+S_{x_0-1,y_0-1}$$
>
> 注意 $x_0,y_0$ 取得 $0$ 的情况。

### 过题代码
```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const int maxn = 100 + 100;
int T, n, q;
int a[maxn];
LL num[maxn][maxn];

LL Sum(int x, int y) {
    if (x < 0 || y < 0) {
        return 0;
    }
    int nn = n << 1;
    LL ret = num[nn - 1][nn - 1] * ((x + 1) / nn) * ((y + 1) / nn);
    int xx = x % nn;
    int yy = y % nn;
    if (xx != nn - 1) {
        ret += num[xx][nn - 1] * ((y + 1) / nn);
    }
    if (yy != nn - 1) {
        ret += num[nn - 1][yy] * ((x + 1) / nn);
    }
    if (xx != nn - 1 && yy != nn - 1) {
        ret += num[xx][yy];
    }
    return ret;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 0; i < n; ++i) {
            scanf("%d", &a[i]);
        }
        int Index = 0;
        for (int i = 0; i < maxn; ++i) {
            for (int j = 0; j <= i; ++j) {
                num[j][i - j] = a[Index];
                Index = (Index + 1) % n;
            }
        }
        for (int i = 0; i < maxn; ++i) {
            for (int j = 0; j < maxn; ++j) {
                if (i != 0) {
                    num[i][j] += num[i - 1][j];
                }
                if (j != 0) {
                    num[i][j] += num[i][j - 1];
                }
                if (i != 0 && j != 0) {
                    num[i][j] -= num[i - 1][j - 1];
                }
            }
        }
        scanf("%d", &q);
        int x1, x2, y1, y2;
        while (q--) {
            scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
            LL ans = Sum(x2, y2) + Sum(x1 - 1, y1 - 1);
            ans -= Sum(x2, y1 - 1) + Sum(x1 - 1, y2);
            printf("%I64d\n",  ans);
        }
    }

    return 0;
}
```

## J. [Let Sudoku Rotate](http://acm.hdu.edu.cn/showproblem.php?pid=6341)

### 题意

> 有一个 $16\times16$ 的数独，其中的每一行、每一列、每一 $4\times4$ 的方格内都不重复地包含 $0$ 到 $F$ 的每一个十六进制数，$Kazari$ 已经完成了一个数独，但是被 $Minato$ 逆时针旋转了其中的某几个 $4\times4$ 的方格，给出旋转后的方格，问最少需要几步才能从原数独逆时针旋转到当前状态。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10^3)$，接下来有 $T$ 组数据，每组数据为一个 $16\times16$ 的字符矩阵，矩阵中只包含 `0` 到 `9` 以及 `A` 到 `F` 这 $16$ 种字符，表示最终的数独状态。

### 输出

> 输出一个非负整数，表示最少需要的操作次数。

### 题解

> 将每个 $4\times4$ 格子顺时针旋转，每次旋转检查是否与当前格子左上方的所有格子冲突，若冲突则剪枝，否则继续往下搜索，“由于数独限制较强，剪枝效果良好。”

### 过题代码
```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100;
int T, cnt, ans;
int num[maxn][maxn], Copy[maxn][maxn];
char str[maxn][maxn];
int vis[20];

void Print() {
    for (int i = 0; i < 16; ++i) {
        for (int j = 0; j < 16; ++j) {
            printf("%3d", num[i][j]);
        }
        printf("\n");
    }
}

void rot(int x, int y) {
    for (int i = 0; i < 4; ++i) {
        for (int j = 0; j < 4; ++j) {
            Copy[x + j][y + 3 - i] = num[x + i][y + j];
        }
    }
    for (int i = 0; i < 4; ++i) {
        for (int j = 0; j < 4; ++j) {
            num[x + i][y + j] = Copy[x + i][y + j];
        }
    }
}

bool Check(int x, int y) {
    for (int i = x; i < x + 4; ++i) {
        ++cnt;
        for (int j = 0; j < y + 4; ++j) {
            if (vis[num[i][j]] == cnt) {
                return false;
            }
            vis[num[i][j]] = cnt;
        }
    }
    for (int j = y; j < y + 4; ++j) {
        ++cnt;
        for (int i = 0; i < x + 4; ++i) {
            if (vis[num[i][j]] == cnt) {
                return false;
            }
            vis[num[i][j]] = cnt;
        }
    }
    return true;
}

void dfs(int depth, int step) {
    if (depth == 16) {
        ans = min(ans, step);
        return ;
    }
    int x = depth / 4;
    int y = depth % 4;
    for (int i = 0; i < 4; ++i) {
        if (Check(x * 4, y * 4)) {
            dfs(depth + 1, step + i);
        }
        rot(x * 4, y * 4);
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        for (int i = 0; i < 16; ++i) {
            scanf("%s", str[i]);
            for (int j = 0; j < 16; ++j) {
                if (str[i][j] <= '9') {
                    num[i][j] = str[i][j] - '0';
                } else {
                    num[i][j] = str[i][j] - 'A' + 10;
                }
            }
        }
        ans = INT_MAX;
        dfs(0, 0);
        printf("%d\n", ans);
    }

    return 0;
}
```

## K. [Expression in Memories](http://acm.hdu.edu.cn/showproblem.php?pid=6342)

### 题意

> 给定一个合法表达式的定义（即我们平时认为的合法表达式）：表达式中每个数字除 $0$ 以外其他数字不包含前导零，表达式中只包含加号和乘号，两个数字之间只能包含一个加号或乘号，加号和乘号必须包含在数字之间。给定一个字符串，字符串中只含有字符 `0` 到 `9`、`+`、`*`以及 `?`，其中 `?` 可以被替换成任意一个字符，如果可以将给出的字符串替换为合法的表达式，则输出替换后的表达式，否则输出 `IMPOSSIBLE`。

### 输入

> 第一行为一个整数 $T$，接下去有 $T$ 组数据，每组数据为一行字符串 $s\ (1\leq|s|\leq500,\sum|s|\leq10^5)$，字符串中只包含字符 `0` 到 `9` 和 `+`、`*`、`?`。

### 输出

> 如果可以通过替换其中的所有 `?` 成为一个合法的表达式，则输出最终的合法表达式，否则输出 `IMPOSSIBLE`，若有多解输出任意一个。

### 题解

> 首先如果有 `?` 在一个单独的零（零前无字符，或者是加号与乘号）后面，就将 `?` 替换为 `+`，否则将 `?` 替换为 `1`，替换结束后检查字符串格式是否为一个合法的表达式，如果是则输出字符串，否则输出 `IMPOSSIBLE`。

### 过题代码

```c++
#include <cstdio>
#include <ctype.h>
#include <cstring>
using namespace std;

typedef long long LL;
const int maxn = 500 + 100;
int T;
char str[maxn];

bool single_zero(int Index) {
    if (str[Index] != '0') {
        return false;
    }
    if (Index == 0 || str[Index - 1] == '+' || str[Index - 1] == '*') {
        return true;
    }
    return false;
}

bool Check() {
    int len = strlen(str);
    if (!isdigit(str[0]) || !isdigit(str[len - 1])) {
        return false;
    }
    int Index = 0;
    while (Index < len) {
        if (str[Index] == '0' && isdigit(str[Index + 1])) {
            return false;
        }
        while (Index < len && str[Index] != '+' && str[Index] != '*') {
            ++Index;
        }
        ++Index;
        if (Index >= len) {
            return true;
        }
        if (!isdigit(str[Index])) {
            return false;
        }
    }
    return true;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%s", str);
        for (int i = 0; str[i]; ++i) {
            if (str[i] == '?') {
                if (i > 0 && single_zero(i - 1)) {
                    str[i] = '+';
                } else {
                    str[i] = '1';
                }
            }
        }
        if (Check()) {
            printf("%s\n", str);
        } else {
            printf("IMPOSSIBLE\n");
        }
    }

    return 0;
}
```

## L. [Graph Theory Homework](http://acm.hdu.edu.cn/showproblem.php?pid=6343)

### 题意

> 给定 $n$ 个节点的完全图，每个节点都有一个权值 $w_i$，节点 $i$ 和节点 $j$ 之间的边权为 $\lfloor\sqrt{|w_i-w_j|}\rfloor$，问从节点 $1$ 到节点 $n$ 的最短路径长度。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10)$，接下来有 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq10^5)$，第二行有 $n$ 个整数 $w_1,w_2,\cdots,w_n\ (1\leq w_i\leq10^5)$。

### 输出

> 输出节点 $1$ 到节点 $n$ 的最短路径。

### 题解

> 从节点 $1$ 到节点 $n$ 之间，经历的中间节点越多，$1$ 到 $n$ 的距离越长，最短距离就是从节点 $1$ 到 $n$ 的边权。

### 过题代码

```c++
#include <cstdio>
#include <cmath>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
int T, n;
int num[maxn];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) {
            scanf("%d", &num[i]);
        }
        printf("%d\n", (int)sqrt(abs(num[n] - num[1])));
    }

    return 0;
}
```