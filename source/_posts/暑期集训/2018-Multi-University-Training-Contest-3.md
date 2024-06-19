---
title: 2018 Multi-University Training Contest 3
category_bar: true
mathjax: true
date: 2018-08-11 21:10:00
updated: 2024-06-18 02:13:48
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第三场多校题解
---

链接：[2018 Multi-University Training Contest 3](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+3&source=1&searchmode=source)

过题数：5

排名：146 / 815

成员：官展鹏，冯彦博，孙昊哲

## A. [Ascending Rating](https://acm.hdu.edu.cn/showproblem.php?pid=6319)

### 题意

> 给定一个长度为 $n$ 的序列 $a_1,a_2,\cdots,a_n$，对每一个长度为 $m$ 的区间 $[i,i+m-1]\ (i\in[1,n-m+1])$，都有以下操作：
>
> 1. 最开始有一个 $maxrating_i$ 与 $count_i$ 值，初始值分别为 $-1$ 和 $0$；
> 2. 从 $l$ 到 $r$，每扫描一个数字，如果当前数字大于 $maxrating_i$，就将 $maxrating_i$ 更新为当前数字，并将 $count_i+1$。
>
> 每一个连续的区间 $maxrating_i$ 和 $count_i$ 的初始值都为 $-1$ 和 $0$，求每个区间最终的 $maxrating_i$ 值和 $count_i$ 值。
### 输入

> 第一行为一个整数 $T\ (1\leq T\leq2000)$，接下来有 $T$ 组数据，每组数据第一行为 $7$ 个整数 $n,m,k,p,q,r,MOD\ (1\leq m,k\leq n\leq10^7,5\leq p,q,r,MOD\leq10^9)$，第二行为 $k$ 个整数 $a_1,a_2,\cdots a_k\ (0\leq a_i\leq10^9)$，表示序列的前 $k$ 项，第 $k+1$ 到第 $n$ 项由以下公式生成：
>
> $$a_i=(p\times a_{i−1}+q\times i+r)\mod MOD$$
>
> 数据保证 $\sum n\leq7\times10^7$ 且 $\sum k\leq2\times10^6$。

### 输出

> 每组数据输出两个数字 $A$ 和 $B$，其中：
>
> $$A=\sum_{i=1}^{n-m+1}maxrating_i\bigoplus i$$
>
> $$B=\sum_{i=1}^{n-m+1}count_i\bigoplus i$$

### 题解

> 从后往前维护一个单调队列，维护到第 $i$ 个区间时队列的大小即 $count_i$，队列中的最大值就是 $maxrating_i$，扫一遍只需要 $O(n)$。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const int maxn = 10000000 + 100;
int T, n, m, k, p, q, r, MOD, head, tail;
LL A, B;
int num[maxn], que[maxn];

int add(int a, int b) {
    int ret = a + b;
    if (ret >= MOD) {
        return ret - MOD;
    }
    return ret;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        head = tail = 0;
        scanf("%d%d%d%d%d%d%d", &n, &m, &k, &p, &q, &r, &MOD);
        for (int i = 1; i <= k; ++i) {
            scanf("%d", &num[i]);
        }
        for (int i = k + 1; i <= n; ++i) {
            num[i] = add((LL)p * num[i - 1] % MOD, (LL)q * i % MOD);
            num[i] = add(num[i], r);
        }
        for (int i = 0; i < m; ++i) {
            int Index = n - i;
            while(head != tail && num[Index] >= num[que[head]]) {
                --head;
            }
            que[++head] = Index;
        }
        A = num[que[tail + 1]] ^ (n - m + 1);
        B = (head - tail) ^ (n - m + 1);
        for (int i = n - m; i >= 1; --i) {
            while (head != tail && num[i] >= num[que[head]]) {
                --head;
            }
            que[++head] = i;
            if (que[tail + 1] >= i + m) {
                ++tail;
            }
            A += num[que[tail + 1]] ^ i;
            B += (head - tail) ^ i;
        }
        printf("%I64d %I64d\n", A, B);
    }

    return 0;
}
```

## C. [Dynamic Graph Matching](https://acm.hdu.edu.cn/showproblem.php?pid=6321)

### 题意

> 定义一种图的匹配：在图上选择任意条边，在被选择的边中任意两条边之间没有公共点，则这些边就是图上的一种匹配。
> 在一个 $n$ 个节点的图上，最初这张图上的边数为 $0$，有 $m$ 次操作，每次操作为以下两种中的一种：
>
> 1. $+\ u\ v$：在节点 $u$ 和节点 $v$ 之间添加一条边；
> 2. $-\ u\ v$：删掉一条在节点 $u$ 和 $v$ 之间的边，在删边之前保证节点 $u,v$ 之间至少存在一条边。
>
> 在每次操作之后，对于每一个 $k\ (k\in[1,\frac{n}{2}])$，输出图上边数为 $k$ 的匹配的数量，两个节点之间的多条边视为不同的边。
### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10)$，接下去有 $T$ 组数据，每组数据第一行为两个整数 $n,m\ (2\leq n\leq10,n\mod2=0,1\leq m\leq30000)$，接下去 $m$ 行每行由一个字符 $ch\ (ch\in\lbrace'+','-'\rbrace)$ 和两个数字 $u,v\ (1\leq u,v\leq n)$ 组成，表示一次操作。

### 输出

> 对于每一次操作，输出 $\frac{n}{2}$ 个整数，分别表示当 $k\in[1,\frac{n}{2}]$ 时，图上边数为 $k$ 的匹配数量对 $10^9+7$ 取模的结果。

### 题解

> 我们用 $2^n$ 个整数对应图上点的选择方案，整数的第 $i$ 个二进制位为 $0$ 表示不选择第 $i$ 个点，否则表示选择第 $i$ 个点，则一个数字表示一种选择点的状态。用 $dp[i]$ 记录某种匹配的边覆盖的点的状态为 $i$ 的情况下，匹配的数量，则 $i$ 中 $1$ 的个数必须为偶数，否则匹配数量就为 $0$。
>
> 每当节点 $u$ 和 $v$ 之间的边的数量 $+1$ 或 $-1$，就会影响所有选点状态同时包含 $u,v$ 两点的 $dp$ 值，如果 $i$ 的第 $u$ 和 $v$ 位都为 $1$，则 $dp[i]+=d\times dp[i-(1<<v)-(1<<u)]$，当 $ch$ 为 `+` 时 $d=1$，否则 $d=-1$。
>
> 初始状态 $dp[0]=1$，用 $ans[i]$ 记录在匹配边的数量为 $i$ 时的总方案数，设 $bit_i$ 为数字 $i$ 的二进制位中 $1$ 的数量，则 $ans[j]=\sum_{i=0}^{2^n}dp[i]$，其中 $j=\frac{bit_i}{2}$。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
using namespace std;

typedef long long LL;
const int maxn = 1000 + 100;
const int MOD = 1000000000 + 7;
int T, n, m, u, v, d;
char ch[2];
int dp[maxn], ans[maxn], bit[maxn];

int add(int a, int b) {
    int ret = a + b;
    if (ret < 0) {
        return ret + MOD;
    }
    if (ret >= MOD) {
        return ret - MOD;
    }
    return ret;
}

void solve(int u, int v, int d) {
    int tmp = (1 << u) | (1 << v);
    for (int i = 1; i < (1 << n); ++i) {
        if ((bit[i] & 1) == 1) {
            continue;
        }
        if ((i & tmp) == tmp) {
            dp[i] = add(dp[i], d * dp[i ^ tmp]);
            ans[bit[i] >> 1] = add(ans[bit[i] >> 1], d * dp[i ^ tmp]);
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    for (int i = 0; i < maxn; ++i) {
        bit[i] = 0;
        for (int j = 0; j < 10; ++j) {
            if (((i >> j) & 1) == 1) {
                ++bit[i];
            }
        }
    }
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        memset(dp, 0, sizeof(int) * (1 << n));
        memset(ans + 1, 0, sizeof(int) * (n >> 1));
        dp[0] = 1;
        for (int i = 0; i < m; ++i) {
            scanf("%s%d%d", ch, &u, &v);
            d = ch[0] == '+'? 1: -1;
            solve(u - 1, v - 1, d);
            for (int j = 1; j <= (n >> 1); ++j) {
                if (j != 1) {
                    printf(" ");
                }
                printf("%d", ans[j]);
            }
            printf("\n");
        }
    }

    return 0;
}
```

## D. [Euler Function](https://acm.hdu.edu.cn/showproblem.php?pid=6322)

### 题意

> 输出第 $k$ 小的 $\phi(n)$ 为合数的 $n$ 的值，其中 $\phi(x)$ 为欧拉函数（$\phi(x)$ 表示 $i\in[1,x]$ 中 $\gcd(i,x)=1$ 的数字个数）。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10^5)$，接下去 $T$ 行每行一个整数 $k\ (1\leq k\leq10^9)$。

### 输出

> 对于每一个 $k$，输出第 $k$ 小的合法的数字。

### 题解

> 通过打表找规律可以发现，除了 $k=1$ 时答案为 $5$ 外，其他答案都为 $k+5$。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    int T, k;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &k);
        if (k == 1) {
            printf("5\n");
        } else {
            printf("%d\n", k + 5);
        }
    }

    return 0;
}
```

## F. [Grab The Tree](https://acm.hdu.edu.cn/showproblem.php?pid=6324)

### 题意

> 小 $Q$ 和小 $T$ 玩一个游戏，在一棵 $n$ 个节点的树上，每个节点都有一个权值 $w_i$，由小 $Q$ 先开始选点，小 $Q$ 选择的点中，任意两个节点不能同时为某条边的两个端点，之后剩下的点全是小 $T$ 的，小 $Q$ 的最终分数就是他所选择的所有点的权值异或值，小 $T$ 的分数也是他拥有的点的权值异或值，问在小 $Q$ 采取最优策略的情况下，谁会获胜，还是平局。

### 输入

> 第一行包含一个整数 $T\ (1\leq T\leq20)$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq10^5)$，第二行为 $n$ 个整数 $w_1,w_2,\cdots,w_n\ (1\leq w_i\leq10^9)$，接下去 $n-1$ 行每行两个整数 $u,v\ (1\leq u,v\leq n)$，表示节点 $u,v$ 之间有一条边。

### 输出

> 如果小 $Q$ 会获胜，则输出 $Q$，如果小 $T$ 会获胜，则输出 $T$，否则输出 $D$。

### 题解

> 如果树上所有点的异或值为 $0$，无论小 $Q$ 如何选点，小 $T$ 的分数都会和小 $Q$ 的分数相等，如果树上所有点的异或值非 $0$，则小 $Q$ 只需要选择最大值，就可以让树上所有其他点异或值的最高位小于小 $Q$ 选择的点的权值，小 $Q$ 必胜。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
int T, n, num, Xor, u, v;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        Xor = 0;
        scanf("%d", &n);
        for (int i = 0; i < n; ++i) {
            scanf("%d", &num);
            Xor ^= num;
        }
        for (int i = 1; i < n; ++i) {
            scanf("%d%d", &u, &v);
        }
        if (Xor == 0) {
            printf("D\n");
        } else {
            printf("Q\n");
        }
    }

    return 0;
}
```

## G. [Interstellar Travel](https://acm.hdu.edu.cn/showproblem.php?pid=6325)

### 题意

> 在一个二维平面上总共有 $n$ 个点，坐标分别为 $(x_i,y_i)$，小 $Q$ 要从坐标为 $(x_1,y_1)$ 的点到达坐标 $(x_n,y_n)$ 的点，他每次只能从横坐标小的点到达横坐标大的点，即 $x_{start}<x_{to}$，如果他要从坐标为 $(x_i,y_i)$ 的点到达 $(x_j,y_j)$ 的点，则需要的代价为 $x_i\times y_j-x_j\times y_i$，问要完成目标的最小代价。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10)$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (2\leq n\leq2\times10^5)$，接下去 $n$ 行每行两个整数 $x_i,y_i\ (0\leq x_i,y_i\leq10^9)$，数据保证 $y_1=y_n=0,0=x_1<x_2,x_3,\cdots,x_{n−1}<x_n$。

### 输出

> 每组数据输出一个序列 $p_1,p_2,\cdots,p_m\ (1\leq p_i\leq n)$，表示小 $Q$ 选择的路径为 $p_1\rightarrow p_2\rightarrow\cdots\rightarrow p_m$，$p_i$ 表示路径上第 $i$ 个点的编号为 $p_i$，点的编号按输入顺序从 $1$ 到 $n$ 确定，其中 $p_1=1,p_m=n$，如果有多组解，输出字典序最小的一组。

### 题解

> 起点为 $(0,0)$，且经过两个点的代价为两个点对应的向量叉积，即这两点与原点构成的三角形面积，若从向量 $i$ 到向量 $j$ 是以顺时针旋转，则这两个向量的叉积就为负数，为了使代价最小，也就是使负数的绝对值——三角形的面积最大，因此这条路径就是从原点到终点的一个凸壳。
>
> 首先对所有点去重，然后构造凸壳，构造过程中保留凸壳上的共线点，所有拐点必然是要作为答案的，但是共线点上可能存在比下一个拐点编号更小的点，为使答案的字典序最小，必须选择这些共线点上编号小于下一个拐点的一系列点，且这一系列点的字典序也要最小，在这些共线点中可以用单调栈来维护需要选择的字典序最小的点的编号。

### 过题代码

```c++
#include <cstdio>
#include <map>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 200000 + 100;
struct Point {
    int x, y;
    int Index;
    Point() {}
    Point(int xx, int yy) {
        x = xx;
        y = yy;
    }
};

bool operator<(const Point &a, const Point &b) {
    return a.x == b.x? a.y < b.y: a.x < b.x;
}

LL operator^(const Point &a, const Point &b) {
    return (LL)a.x * b.y - (LL)a.y * b.x;
}

Point operator-(const Point &a, const Point &b) {
    return Point(a.x - b.x, a.y - b.y);
}

bool operator!=(const Point &a, const Point &b) {
    return a.x != b.x || a.y != b.y;
}

int T, n, top, cnt, Mon_top;
Point point[maxn], sta[maxn], p[maxn], Mon_sta[maxn];
map<int, int> mp;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        mp.clear();
        cnt = 0;
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) {
            scanf("%d%d", &point[i].x, &point[i].y);
            point[i].Index = i;
            mp[point[i].x] = max(mp[point[i].x], point[i].y);
        }
        sort(point + 1, point + 1 + n);
        p[0].x = p[0].y = -1;
        for (int i = 1; i <= n; ++i) {
            if (point[i].y == mp[point[i].x]) {
                if (point[i] != p[cnt]) {
                    p[++cnt] = point[i];
                } else if (point[i].Index < p[cnt].Index) {
                    p[cnt] = point[i];
                }
            }
        }
        top = 0;
        for (int i = 1; i <= cnt; ++i) {
            while (top > 1 && ((sta[top - 1] - sta[top - 2]) ^ (p[i] - sta[top - 2])) > 0) {
                --top;
            }
            sta[top++] = p[i];
        }
        printf("1");
        for (int i = 0; i < top - 1; ) {
            int End = i + 1;
            while (End + 1 < top && ((sta[End + 1] - sta[End]) ^ (sta[End] - sta[i])) == 0) {
                ++End;
            }
            Mon_top = 0;
            for (int j = i + 1; j <= End; ++j) {
                while (Mon_top > 0 && sta[j].Index < Mon_sta[Mon_top - 1].Index) {
                    --Mon_top;
                }
                Mon_sta[Mon_top++] = sta[j];
            }
            for (int j = 0; j < Mon_top; ++j) {
                printf(" %d", Mon_sta[j].Index);
            }
            i = End;
        }
        printf("\n");
    }

    return 0;
}
```

## H. [Monster Hunter](https://acm.hdu.edu.cn/showproblem.php?pid=6326)

### 题意

> 小 $Q$ 在一棵 $n$ 个节点的树上，他最初的位置在节点 $1$ 处，除了 $1$ 以外，其他节点都有一个怪兽，小 $Q$ 需要沿着树上路径行走，每到达一个节点 $i$，就要与这个节点上的怪兽进行战斗，战斗将会损失 $a_i$ 点 $HP$，战斗过程中若 $HP$ 值小于 $0$ 则游戏结束，战斗结束后若小 $Q$ 的 $HP$ 值不小于 $0$，则他的 $HP$ 值将会增加 $b_i$，每个节点上的怪兽被消灭后，再次走到这个节点时就不需要再进行战斗，问如果要消灭树上所有的怪兽，小 $Q$ 最初至少需要有多少点 $HP$ 值。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq2000)$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (2\leq n\leq10^5)$，接下去 $n-1$ 行每行两个整数 $a_i,b_i\ (0\leq a_i,b_i\leq10^9)$，第 $i$ 行整数表示节点 $i+1$ 上的战斗 $HP$ 增减值，接下去 $n-1$ 行每行两个整数 $u_i,v_i\ (1\leq u_i,v_i\leq n)$，表示节点 $u_i$ 和 $v_i$ 之间有一条边，数据保证 $\sum n\leq10^6$。

### 输出

> 输出通关所需要的最小的初始 $HP$ 值。

### 题解

> 首先不考虑树上路径的限制，如果每个怪兽可以以任意次序进行战斗，$a<b$ 的怪兽必然要比 $a\geq b$ 的怪兽先进行攻击，这样才能获得最大的 $HP$，在 $a<b$ 的所有怪兽中，必然要先攻击 $a$ 值小的，在 $a\geq b$ 大的所有怪兽中，如果先攻击第 $i$ 只怪兽再攻击第 $j$ 只怪兽，在攻击后一只怪兽的战斗过程中的 $HP$ 值为 $HP-a_i+b_i-a_j$，如果反过来先攻击第 $j$ 只怪兽，在后一只怪兽的战斗过程中的 $HP$ 值为 $HP-a_j+b_j-a_i$，可以发现攻击的次序与 $a_i$ 和 $a_j$ 无关，而只与 $b_i$ 和 $b_j$ 有关，必然要先攻击 $b$ 值大的。
>
> 通过以上规则可以确定一个最优的攻击怪兽的次序 $p_1,p_2,\cdots,p_{n-1}$，现在考虑树上路径的限制，攻击每只怪兽之前必须要先打败它所有的祖父节点，因此对于每个节点 $p_i$，判断该节点的父节点是否为 $1$，若为 $1$，则可以直接攻击，否则将 $p_i$ 的 $a_i,b_i$ 值合并到它的父节点的 $a_f,b_f$ 上表示在攻击完它的父节点后必须立即攻击第 $i$ 个节点，父节点的 $a,b$ 值应更新为
>
> $$a_f'=-\min(-a_f,-a_f+b_f-a_i)$$
>
> $$b_f'=-a_f+b_f-a_i+b_i+a_f'$$
>
> 然后将所有 $a_i$ 的子节点的父节点改为 $f$，更新父节点并快速排序的过程可以用堆来维护，更改父节点的操作可以用并查集来维护。

### 过题代码

```c++
#include <cstdio>
#include <vector>
#include <queue>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
struct Node {
    LL a, b;
    int fa, Index;
};

bool operator<(const Node &a, const Node &b) {
    bool flag_a = a.a < a.b;
    bool flag_b = b.a < b.b;
    if (flag_a != flag_b) {
        return flag_a < flag_b;
    }
    if (flag_a) {
        return a.a > b.a;
    }
    return a.b < b.b;
}

bool operator!=(const Node &a, const Node &b) {
    return a.a != b.a || a.b != b.b || a.fa != b.fa || a.Index != b.Index;
}

int T, n, u, v;
int fa[maxn];
Node node[maxn];
vector<int> G[maxn];
priority_queue<Node> que;

void Init() {
    for (int i = 1; i <= n; ++i) {
        fa[i] = i;
        G[i].clear();
    }
}

int Find(int x) {
    return x == fa[x]? x: fa[x] = Find(fa[x]);
}

void unit(int x, int y) {
    int xx = Find(x);
    int yy = Find(y);
    fa[xx] = yy;
}

void dfs(int f, int x) {
    node[x].fa = f;
    int len = G[x].size();
    for (int i = 0; i < len; ++i) {
        int pos = G[x][i];
        if (pos != f) {
            dfs(x, pos);
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        Init();
        node[1].a = node[1].b = 0;
        node[1].Index = 1;
        for (int i = 2; i <= n; ++i) {
            scanf("%I64d%I64d", &node[i].a, &node[i].b);
            node[i].Index = i;
        }
        for (int i = 1; i < n; ++i) {
            scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }
        dfs(1, 1);
        for (int i = 2; i <= n; ++i) {
            que.push(node[i]);
        }
        while (!que.empty()) {
            Node tmp = que.top();
            que.pop();
            if (node[tmp.Index] != tmp || tmp.Index == 1) {
                continue;
            }
            int f = Find(tmp.fa);
            unit(tmp.Index, f);
            LL a = -min(-node[f].a, -node[f].a + node[f].b - tmp.a);
            LL b = -node[f].a + node[f].b - tmp.a + tmp.b + a;
            node[f].a = a;
            node[f].b = b;
            que.push(node[f]);
        }
        printf("%I64d\n", node[1].a);
    }

    return 0;
}
```

## I. [Random Sequence](https://acm.hdu.edu.cn/showproblem.php?pid=6327)

### 题意

> 有一个长度为 $n$ 的序列 $a_1,a_2,\cdots,a_n\ (0\leq a_i\leq m)$ 和长度为 $m$ 的序列 $v_1,v_2,\cdots,v_m$，如果 $a_i=0$，则 $a_i$ 的值可能为 $1$ 到 $m$ 之间的任意值，取得任意值的概率为 $\frac{1}{m}$，求公式：
>
> $$\prod_{i=1}^{n-3}v_{\gcd(a_i,a_{i+1},a_{i+2},a_{i+3})}$$
>
> 的期望值。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10)$，接下去有 $T$ 组数据，每组数据第一行为两个整数 $n,m\ (4\leq n\leq100,1\leq m\leq100)$，第二行为 $n$ 个整数 $a_1,a_2,\cdots,a_n\ (0\leq a_i\leq m)$，第三行为 $m$ 个整数 $v_1,v_2,\cdots,v_m\ (1\leq v_i\leq10^9)$。

### 输出

> 输出所求公式的期望值对 $10^9+7$ 取模后的结果，若期望值无法用整数表示，则先将期望值化简为 $\frac{A}{B}$ 后，输出 $C\ (0\leq C<10^9+7)$ 的值，其中 $C$ 满足 $A=B\times C\ (\mod 10^9+7)$。

### 题解

> 由于任意两个 $v$ 之间的选择是相互独立的，所以 $E(v_1v_2)=E(v_1)E(v_2)$。
>
> 如果定义状态 $dp[i][x][y][z]$ 表示到第 $i$ 位 $a_i=x,a_{i-1}=y,a_{i-2}=z$ 时 $\prod_{j=4}^iv_{\gcd(a_j,a_{j-1},a_{j-2},a_{j-3})}$ 的所有合法情况的和，则可以通过枚举第 $a_{i+1}$ 位的所有合法值 $xx$ 计算 $dp[i+1][xx][x][y]$ 的值，其递推公式为：
>
> $$dp[i+1][xx][x][y]=\sum_{x,y,z\in[1,m]}dp[i][x][y][z]\times v_{\gcd(xx,x,y,z)}$$
>
> 注意当 $a_i$ 的值非 $0$ 时 $x$ 的值只能取 $a_i$，只有 $a_i$ 的值为 $0$ 时 $x$ 才可以从 $1$ 到 $m$ 枚举所有值，对 $xx,y,z$ 同理。这种递推式的时间复杂度为 $O(nm^4)$，必然超时。
>
> 我们可以定义状态 $dp[i][x][y][z]$ 表示到第 $i$ 位 $a_i=x,\gcd(x,a_{i-1})=y,\gcd(x,a_{i-1},a_{i-2})=z$ 时所有合法情况下 $\prod_{j=4}^iv_{\gcd(a_j,a_{j-1},a_{j-2},a_{j-3})}$ 的和，由公约数关系可以得到，$x,y,z$ 必须满足 $z$ 能整除 $y$，$y$ 整除 $x$，于是有下列递推式：
>
> $$dp[i+1][xx][\gcd(xx,x)][\gcd(xx,y)]=\sum_{x,y,z\in[1,m],z|y,y|x}dp[i][x][y][z]\times v_{\gcd(xx,z)}$$
>
> 其中 $xx,x,y,z$ 在 $a_i$ 非 $0$ 时的取值限制同上，$a|b$ 表示 $a$ 能整除 $b$，在 $z|y,y|x$ 的限制下，$x,y,z$ 所有合法的状态可以减少到 $1471$ 种，因此时间复杂度为 $O(1471\times nm)$，其中 $\gcd$ 的计算可以 $O(m^2\log m)$ 通过预处理将复杂度降低到 $O(1)$。
>
> 最后是 $dp$ 的初始值的确定：
>
> $$dp[3][x][\gcd(x,y)][\gcd(x,y,z)]=\sum_{x,y,z\in[1,m]}1$$
>
> 其中 $x,y,z$ 的取值限制同上，$dp$ 初始值即表示取前 $3$ 位为 $x,y,z$ 的合法状态数，系数为 $1$，初始化方式为暴力，时间复杂度为 $O(m^3)$。
>
> 最后将 $\sum_{x,y,z\in[1,m],z|y,y|x}dp[n][x][y][z]$ 乘上总方案数对 $10^9+7$ 的逆元就是答案，总方案数为 $m^{cnt}$，其中 $cnt$ 为 $a$ 序列中 $0$ 的个数。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const int maxn = 101;
const int MOD = 1000000007;
int T, n, m, ans, cnt, inv;
int a[maxn], v[maxn];
int Gcd[maxn][maxn], dp[maxn][maxn][maxn][maxn];

int add(int a, int b) {
    a += b;
    if (a >= MOD) {
        return a - MOD;
    }
    if (a < 0) {
        return a + MOD;
    }
    return a;
}

int gcd(int x, int y) {
    return y == 0? x: gcd(y, x % y);
}

void prepare_gcd() {
    for (int i = 1; i < maxn; ++i) {
        Gcd[0][i] = Gcd[i][0] = i;
        for (int j = 1; j < maxn; ++j) {
            Gcd[i][j] = gcd(i, j);
        }
    }
}

void Init() {
    for (int i = 3; i <= n; ++i) {
        for (int z = 1; z <= m; ++z) {
            for (int y = z; y <= m; y += z) {
                for (int x = y; x <= m; x += y) {
                    dp[i][x][y][z] = 0;
                }
            }
        }
    }
    for (int x = 1; x <= m; ++x) {
        if (a[3] != 0 && x != a[3]) {
            continue;
        }
        for (int y = 1; y <= m; ++y) {
            if(a[2] != 0 && a[2] != y) {
                continue;
            }
            for (int z = 1; z <= m; ++z) {
                if (a[1] != 0 && z != a[1]) {
                    continue;
                }
                ++dp[3][x][Gcd[x][y]][Gcd[Gcd[x][y]][z]];
            }
        }
    }
}

LL fast_pow(LL res, LL n) {
    LL ans;
    for (ans = 1; n != 0; n >>= 1) {
        if ((n & 1) == 1) {
            ans = (ans * res) % MOD;
        }
        res = (res * res) % MOD;
    }
    return ans;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    prepare_gcd();
    scanf("%d", &T);
    while (T--) {
        cnt = 0;
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; ++i) {
            scanf("%d", &a[i]);
            if (a[i] == 0) {
                ++cnt;
            }
        }
        for (int i = 1; i <= m; ++i) {
            scanf("%d", &v[i]);
        }
        Init();
        for (int i = 3; i < n; ++i) {
            for (int z = 1; z <= m; ++z) {
                for (int y = z; y <= m; y += z) {
                    for (int x = y; x <= m; x += y) {
                        if (dp[i][x][y][z] == 0) {
                            continue;
                        }
                        for (int xx = 1; xx <= m; ++xx) {
                            if (a[i + 1] != 0 && a[i + 1] != xx) {
                                continue;
                            }
                            dp[i + 1][xx][Gcd[xx][x]][Gcd[xx][y]] = add(dp[i + 1][xx][Gcd[xx][x]][Gcd[xx][y]], (LL)dp[i][x][y][z] * v[Gcd[xx][z]] % MOD);
                        }
                    }
                }
            }
        }
        ans = 0;
        for (int k = 1; k <= m; ++k) {
            for (int j = k; j <= m; j += k) {
                for (int i = j; i <= m; i += j) {
                    ans = add(ans, dp[n][i][j][k]);
                }
            }
        }
        ans = (LL)ans * fast_pow(fast_pow(m, cnt), MOD - 2) % MOD;
        printf("%d\n", ans);
    }

    return 0;
}
```

## L. [Visual Cube](https://acm.hdu.edu.cn/showproblem.php?pid=6330)

### 题意

> 给定一个长方体的长宽高 $a,b,c$，按照样例输出长方体。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq50)$，接下去 $T$ 行每行三个整数 $a,b,c\ (1\leq a,b,c\leq20)$。

### 输出

> 按样例输出。

### 题解

> 由于前面覆盖后面，上面覆盖下面，右边覆盖左边，所以可以依次从后往前，从下往上，从左往右输出网格。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
using namespace std;

typedef long long LL;
const int maxn = 100 + 100;
int T, a, b, c, row, col;
char str[maxn][maxn];

void Draw(int x, int y, bool back) {
    for (int i = 2 * c; i >= 0; i -= 2) {
        for (int j = 0; j <= 2 * a; j += 2) {
            str[x + i][y + j] = '+';
            if (!back) {
                str[x + i - 1][y + j] = '.';
                str[x + i - 1][y + j + 1] = '/';
                str[x + i][y + j + 1] = '.';
            }
            if (i != 2 * c) {
                str[x + i + 1][y + j] = '|';
            }
            if (j != 0) {
                str[x + i][y + j - 1] = '-';
            }
            if (i != 2 * c && j != 0) {
                str[x + i + 1][y + j - 1] = '.';
            }
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d", &a, &b, &c);
        row = 2 * c + 1 + 2 * b;
        col = 2 * a + 1 + 2 * b;
        for (int i = 0; i < row; ++i) {
            memset(str[i], '.', sizeof(char) * col);
            str[i][col] = '\0';
        }
        for (int i = 0; i <= 2 * b; i += 2) {
            int j = 2 * b - i;
            bool back = false;
            if (i == 0) {
                back = true;
            }
            Draw(i, j, back);
        }
        for (int i = 0; i < row; ++i) {
            printf("%s\n", str[i]);
        }
    }

    return 0;
}
```

## M. [Walking Plan](https://acm.hdu.edu.cn/showproblem.php?pid=6331)

### 题意

> 给一个 $n$ 个节点 $m$ 条边的有向图，第 $i$ 条边的两个端点为 $u_i,v_i$，边的长度为 $w_i$，$q$ 次询问，每次询问从节点 $s$ 到 $t$ 至少走过 $k$ 条路径的最小距离。

### 输入

> 第一行包含一个整数 $T\ (1\leq T\leq10)$，接下去有 $T$ 组数据，每组数据第一行为两个整数 $n,m\ (2\leq n\leq50,1\leq m\leq10^4)$，接下去 $m$ 行每行三个整数 $u_i,v_i,w_i\ (1\leq u_i,v_i\leq n,u_i\neq v_i,1\leq w_i\leq10^4)$，接着为一个整数 $q\ (1\leq q\leq10^5)$，接下去 $q$ 行每行三个整数 $s_i,t_i,k_i\ (1\leq s_i,t_i\leq n,1\leq k\leq10^4)$。

### 输出

> 对于每次询问，输出最短路径长度，如果无法从节点 $s_i$ 到达 $t_i$，则输出 $-1$。

### 题解

> 定义 $G[i][j]$ 为从节点 $i$ 恰好经过 $1$ 步到达节点 $j$ 的最短距离（即原图按输入取最小值），无法到达设为 $\infty$，$dis[k][i][j]$ 表示从 $i$ 点出发恰好经过 $k$ 步到达 $j$ 点的最短路径，则有递推式：
>
> $$dis[0][i][j]=\begin{cases}
> 0&i=j\\\\
> \infty&i\neq j
> \end{cases}$$
>
> $$dis[k][i][j]=\min(dis[k-1][i][u]+G[u][j]),u\in[1,n],k\neq0$$
>
> 定义 $A[x][i][j]$ 表示从 $i$ 恰好经过 $100x$ 步到达 $j$ 的最短距离，$B[x][i][j]$ 表示从 $i$ 最少经过 $x$ 次到达 $j$ 的最短距离，这样 $k$ 就可以分为 $\lfloor\frac{k}{100}\rfloor$ 和 $k\%100$ 两个部分，通过枚举中转点得到答案：
>
> $$ans=\min(A[\lfloor\frac{k}{100}\rfloor][i][u]+B[k\\%100][u][j]),u\in[1,n]$$
>
> 第一部分可以直接从上面的递推式得到，第二部分可以在原图上令 $G[i][i]=0,i\in[1,n]$ 后跑一遍 $floyd$，再将从 $i$ 恰好经过 $k$ 步到达 $j$ 的最短距离距离 $dis[k][i][j]$ 通过 $G[i][j]$ 转化为 $B[x][i][j]$：
>
> $$B[k][i][j]=\min(dis[k][i][u],G[u][j]),u\in[1,n]$$

### 过题代码
```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long LL;
const int Size = 51;
const int maxn = 101;
int n;
struct Matrix {
    int num[Size][Size];

    void Init() {
        for (int i = 1; i <= n; ++i) {
            memset(num[i], 0x3f, sizeof(int) * (n + 1));
        }
    }

    void Set_zero() {
        for (int i = 1; i <= n; ++i) {
            num[i][i] = 0;
        }
    }

    void operator=(const Matrix &m) {
        for (int i = 1; i <= n; ++i) {
            memcpy(num[i], m.num[i], sizeof(int) * (n + 1));
        }
    }

    void Combine(const Matrix &m, Matrix &ans) {
        Matrix ret;
        ret.Init();
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                for (int k = 1; k <= n; ++k) {
                    ret.num[i][j] = min(ret.num[i][j], num[i][k] + m.num[k][j]);
                }
            }
        }
        ans = ret;
    }

    void floyd() {
        for (int k = 1; k <= n; ++k) {
            for (int i = 1; i <= n; ++i) {
                for (int j = 1; j <= n; ++j) {
                    num[i][j] = min(num[i][j], num[i][k] + num[k][j]);
                }
            }
        }
    }
};

int T, m, u, v, dis, q, k, INF, ans;
Matrix A[maxn], B[maxn], G;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    memset(&INF, 0x3f, sizeof(int));
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        G.Init();
        for (int i = 0; i < m; ++i) {
            scanf("%d%d%d", &u, &v, &dis);
            G.num[u][v] = min(G.num[u][v], dis);
        }
        A[0].Init();
        A[0].Set_zero();
        B[0].Init();
        B[0].Set_zero();
        for (int i = 1; i < maxn; ++i) {
            B[i - 1].Combine(G, B[i]);
        }
        for (int i = 1; i < maxn; ++i) {
            A[i - 1].Combine(B[100], A[i]);
        }
        G.Set_zero();
        G.floyd();
        for (int i = 0; i < maxn; ++i) {
            G.Combine(B[i], B[i]);
        }
        scanf("%d", &q);
        while (q--) {
            scanf("%d%d%d", &u, &v, &k);
            ans = INF;
            for (int i = 1; i <= n; ++i) {
                ans = min(ans, A[k / 100].num[u][i] + B[k % 100].num[i][v]);
            }
            if (ans == INF) {
                printf("-1\n");
            } else {
                printf("%d\n", ans);
            }
        }
    }

    return 0;
}
```