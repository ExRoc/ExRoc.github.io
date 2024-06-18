---
title: 2018 Multi-University Training Contest 2
category_bar: true
mathjax: true
date: 2018-08-10 21:10:00
updated: 2024-06-18 01:32:40
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第二场多校题解
---

链接：[2018 Multi-University Training Contest 2](http://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+2&source=1&searchmode=source)

过题数：3

排名：139 / 821

成员：官展鹏，冯彦博，孙昊哲

## C. [Cover](http://acm.hdu.edu.cn/showproblem.php?pid=6311)

### 题意

> 在一个 $n$ 个节点 $m$ 条边的图中，用最少的一笔画把图上所有的边都覆盖一次，问最少要画多少划，并输出方案。

### 输入

> 多组输入（不超过 $20$ 组），每组输入第一行为两个整数 $n,m\ (1\leq n,m\leq10^5)$，接下去 $m$ 行每行两个整数 $u,v\ (1\leq u,v\leq n)$，表示在节点 $u$ 和 $v$ 之间有一条路径，数据保证没有重边与自环。

### 输出

> 对于每组输入，第一行输出一个整数 $p$，表示最少的笔画数，接下去 $p$ 行，每行表示一条路径，每行的第一个数字 $x$ 表示这条路径经过的边的数量，后面跟着 $x$ 个整数，每个整数表示经过编号为 $abs(x)$ 的边，如果经过第 $i$ 条路径时是从 $u_i$ 到达 $v_i$（路径方向与输入相同），则输出 $i$，否则（路径方向与输入相反）输出 $-i$，路径需要按照笔画顺序输出。如果有多组输出任意一组。

### 题解

> 对每个连通块分开处理，如果某个连通块内只有一个点，则不需要笔画，否则统计这个连通块内度为奇数的点的数量 $cnt$，则需要的最少笔画数量为 $\max(\frac{cnt}{2},1)$（欧拉路径性质），求和就是整张图的最少笔画数。然后对于每个连通块内度为奇数的点（点的个数一定为偶数），两两加边，这样就能使得所有点的度都为偶数，在新图上跑出欧拉路径，再将这个路径上之前加上去的边从环上删掉，就可以得到 $\max(\frac{cnt}{2},1)$ 条路径。

### 过题代码

```c++
#include <cstdio>
#include <cmath>
#include <vector>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
struct Node {
    int pos, Index;
    Node() {}
    Node(int p, int I) {
        pos = p;
        Index = I;
    }
};

struct Edge {
    int u, v;
};

int n, m, u, v, cnt, mm, cnttmp, cas;
int fa[maxn], num[maxn], deg[maxn], ans[maxn << 2], anstmp[maxn];
vector<Node> G[maxn];
vector<int> GG[maxn];
Edge edge[maxn << 2];
int vis[maxn << 2];

bool cmp(const int &a, const int &b) {
    return deg[a] > deg[b];
}

void Init() {
    cnt = 0;
    mm = m + 1;
    for (int i = 1; i <= n; ++i) {
        fa[i] = i;
        num[i] = 0;
        deg[i] = 0;
        G[i].clear();
        GG[i].clear();
    }
}

int Find(int x) {
    return (x == fa[x]? x: fa[x] = Find(fa[x]));
}

void unit(int x, int y) {
    int xx = Find(x);
    int yy = Find(y);
    if (deg[xx] % 2 == 1) {
        swap(xx, yy);
    }
    if (xx != yy) {
        fa[xx] = yy;
        num[yy] += num[xx];
    }
}

void dfs(int x) {
    int len = G[x].size();
    for (int i = 0; i < len; ++i) {
        int pos = G[x][i].pos;
        int Index = G[x][i].Index;
        if (vis[abs(Index)] != cas) {
            vis[abs(Index)] = cas;
            dfs(pos);
            ans[cnt++] = Index;
        }
    }
}

int Next(int x, int mod) {
    return ((x - 1) % mod + mod) % mod;
}

void solve(int pos) {
    cnt = 0;
    sort(GG[pos].begin(), GG[pos].end(), cmp);
    int len = GG[pos].size();
    for (int i = 0; i < len; i += 2) {
        if (deg[GG[pos][i]] == 1) {
            u = GG[pos][i];
            v = GG[pos][i + 1];
            G[u].push_back(Node(v, mm));
            G[v].push_back(Node(u, -mm));
            ++mm;
        } else {
            break;
        }
    }
    dfs(pos);
    int End = -1;
    for (int i = 0; i < cnt; ++i) {
        if (abs(ans[i]) > m) {
            End = i;
            break;
        }
    }
    if (End == -1) {
        printf("%d", cnt);
        for (int i = cnt - 1; i >= 0; --i) {
            printf(" %d", ans[i]);
        }
        printf("\n");
        return ;
    }
    for (int i = Next(End, cnt); i != End; ) {
        cnttmp = 0;
        bool flag = false;
        for (int j = i; j != End; j = Next(j, cnt)) {
            if (abs(ans[j]) > m) {
                i = Next(j, cnt);
                flag = true;
                break;
            }
            anstmp[cnttmp++] = ans[j];
        }
        if (!flag) {
            i = End;
        }
        if (cnttmp == 0) {
            continue;
        }
        printf("%d", cnttmp);
        for (int j = 0; j < cnttmp; ++j) {
            printf(" %d", anstmp[j]);
        }
        printf("\n");
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    while (scanf("%d%d", &n, &m) != EOF) {
        ++cas;
        Init();
        for (int i = 1; i <= m; ++i) {
            scanf("%d%d", &u, &v);
            G[u].push_back(Node(v, i));
            G[v].push_back(Node(u, -i));
            ++deg[u];
            ++deg[v];
            edge[i].u = u;
            edge[i].v = v;
        }
        for (int i = 1; i <= n; ++i) {
            deg[i] %= 2;
            if (deg[i] == 1) {
                ++num[i];
            }
        }
        for (int i = 1; i <= m; ++i) {
            unit(edge[i].u, edge[i].v);
        }
        for (int i = 1; i <= n; ++i) {
            GG[Find(i)].push_back(i);
        }
        for (int i = 1; i <= n; ++i) {
            if ((int)GG[i].size() <= 1) {
                continue;
            }
            cnt += max(num[i] / 2, 1);
        }
        printf("%d\n", cnt);
        for (int i = 1; i <= n; ++i) {
            if ((int)GG[i].size() <= 1) {
                continue;
            }
            solve(i);
        }
    }

    return 0;
}
```

## D. [Game](http://acm.hdu.edu.cn/showproblem.php?pid=6312)

### 题意

> $Alice$ 和 $Bob$ 两人玩一个博弈游戏，由 $Alice$ 先开始，两人轮流进行下面的操作：在 $1$ 到 $n$ 个数字构成的集合中选择一个整数 $x$，然后将 $x$ 及 $x$ 的所有约数都从集合中删去，无法进行操作的一方失败。问如果双方都采取最优策略，$Alice$ 能否取得胜利。

### 输入

> 多组输入（不超过 $10$ 组），每组输入包含一个整数 $n\ (1\leq n\leq500)$。

### 输出

> 对于每组输入，如果 $Alice$ 可以获胜，则输出 $Yes$，否则输出 $No$。

### 题解

> ~~通过暴力打表到前~8~个数字可以发现，直接输出 Yes。~~
> 
> 假设初始集合中只有 $2$ 到 $n$ 个整数，若 $Alice$ 选择了数字 $x$ 可以获胜，那么对于 $1$ 到 $n$ 的集合选择 $x$ 之后剩下的数字与初始集合为 $2$ 到 $n$ 的下一个状态完全相同，都是必败态，若 $Alice$ 初始集合 $2$ 到 $n$ 是一种必败态，那么 $Alice$ 只要选择数字 $1$ 就可以将必败态转移给 $Bob$，所以无论如何 $Alice$ 都将获胜。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
int n;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    while (scanf("%d", &n) != EOF) {
        printf("Yes\n");
    }

    return 0;
}
```

## E. [Hack It](http://acm.hdu.edu.cn/showproblem.php?pid=6313)

### 题意

> 构造一个 $n\times n\ (1\leq n\leq2000)$ 的 $01$ 矩阵，要求矩阵内的 $1$ 的个数不小于 $85000$ 且对于矩阵内任意一个子矩阵，子矩阵中的四个角的数字不同时为 $1$。

### 输入

> 无。

### 输出
> 第一行为一个整数 $n$，表示将要输出的矩阵大小，接下来 $n$ 行 $n$ 列的字符串，字符串内每个字符只能为 $0$ 或者 $1$。

### 题解

> 关于构造方法的证明思路，不会，详见大佬给出的证明（也可以直接跳过证明看构造的方法）：
>
> ![证明 1](img/2018-Multi-University-Training-Contest-2/Hack-It-证明-1.jpg)
>
> ![证明 2](img/2018-Multi-University-Training-Contest-2/Hack-It-证明-2.jpg)
>
> 文字描述构造方案比较麻烦，直接看图找规律比较好些，构造方案如下（$n\times n=256$ 的情况下）：
>
> $$\begin{matrix}
> 1000&1000&1000&1000\\\\
> 1000&0100&0010&0001\\\\
> 1000&0010&1000&0010\\\\
> 1000&0001&0010&0100\\\\
> \\\\
> 0100&0100&0100&0100\\\\
> 0100&0010&0001&1000\\\\
> 0100&0001&0100&0001\\\\
> 0100&1000&0001&0010\\\\
> \\\\
> 0010&0010&0010&0010\\\\
> 0010&0001&1000&0100\\\\
> 0010&1000&0010&1000\\\\
> 0010&0100&1000&0001\\\\
> \\\\
> 0001&0001&0001&0001\\\\
> 0001&1000&0100&0010\\\\
> 0001&0100&0001&0100\\\\
> 0001&0010&0100&1000\\\\
> \end{matrix}$$
>
> 于是对于 $2000\times2000$ 的情况，找到子矩阵大小为 $47\times47$ 的就可以满足条件，将多出 $2000$ 的部分截断即可。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
using namespace std;

typedef long long LL;
const int n = 47;
const int maxn = n * n + 100;
char str[maxn][maxn];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    memset(str, '0', sizeof(str));
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            str[i][j * n + (i * j) % n] = '1';
        }
    }
    for (int i = 1; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            for (int k = 0; k < n; ++k) {
                for (int l = 0; l < n; ++l) {
                    str[i * n + j][k * n + l] = str[(i - 1) * n + j][k * n + ((l - 1) % n + n) % n];
                }
            }
        }
    }
    printf("2000\n");
    for (int i = 0; i < 2000; ++i) {
        str[i][2000] = '\0';
        printf("%s\n", str[i]);
    }

    return 0;
}
```

## F. [Matrix](http://acm.hdu.edu.cn/showproblem.php?pid=6314)

### 题意

> 对于一个 $n\times m$ 的网格，每一个小格只能涂上黑色或者白色，问所有涂色方案中，至少有 $A$ 行 $B$ 列为黑色的方案数。

### 输入

> 多组输入（不超过 $5$ 组），每组为四个整数 $n,m,A,B\ (1\leq n,m,A,B\leq3000)$

### 输出

> 输出方案数对 $998244353$ 取模的结果。

### 题解

> 根据容斥原理可以知道，至少 $A$ 行 $B$ 列为黑色的方案数为 $\sum_{i=A}^nf_{ai}C_n^i\sum_{j=B}^mf_{bj}C_m^j$，如果 $i$ 和 $j$ 是从 $1$ 开始的，那么 $f_{ai}f_{bj}$ 的值就为 $(-1)^{i+j}$，然而 $i$ 和 $j$ 不是从 $1$ 开始的，而是从某个数字开始的，于是该系数就为 $f_{ai}=1-\sum_{k=A}^iC_i^kf_{ak},~f_{bj}=1-\sum_{k=B}^jC_j^kf_{bk}$。系数 $f_{ai}$ 具体的推导可以根据在 $k\in[A,i)$ 容斥的加减计算过程中，至少为 $i$ 行的状态被重复计算的次数来得到。最后对 $f_{ai}$ 和 $f_{bj}$ 进行 $O(n^2+m^2)$ 次预处理，再 $O(nm)$ 地计算容斥公式，就可以得到答案。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const int maxn = 3001;
const int MOD = 998244353;
int n, m, A, B, ans;
int C[maxn][maxn], two[maxn * maxn];
int fa[maxn], fb[maxn];

void Init() {
    for (int i = 0; i < maxn; ++i) {
        for (int j = 0; j <= i; ++j) {
            if (j == i || j == 0) {
                C[i][j] = 1;
            } else {
                C[i][j] = C[i - 1][j - 1] + C[i - 1][j];
                if (C[i][j] >= MOD) {
                    C[i][j] -= MOD;
                }
            }
        }
    }
    two[0] = 1;
    for (int i = 1; i < maxn * maxn; ++i) {
        two[i] = two[i - 1] * 2;
        if (two[i] >= MOD) {
            two[i] -= MOD;
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Init();
    while (scanf("%d%d%d%d", &n, &m, &A, &B) != EOF) {
        ans = 0;
        for (int i = A; i <= n; ++i) {
            fa[i] = 0;
            for (int j = A; j < i; ++j) {
                fa[i] = (fa[i] + (LL)C[i][j] * fa[j]) % MOD;
            }
            fa[i] = 1 - fa[i];
            if (fa[i] < 0) {
                fa[i] += MOD;
            }
        }
        for (int i = B; i <= m; ++i) {
            fb[i] = 0;
            for (int j = B; j < i; ++j) {
                fb[i] = (fb[i] + (LL)C[i][j] * fb[j]) % MOD;
            }
            fb[i] = 1 - fb[i];
            if (fb[i] < 0) {
                fb[i] += MOD;
            }
        }
        for (int i = A; i <= n; ++i) {
            LL tmp = (LL)fa[i] * C[n][i] % MOD;
            for (int j = B; j <= m; ++j) {
                ans = (ans + ((tmp * fb[j] % MOD) * C[m][j] % MOD) * two[(n - i) * (m - j)] % MOD) % MOD;
            }
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## G. [Naive Operations](http://acm.hdu.edu.cn/showproblem.php?pid=6315)

### 题意

> 有两个长度为 $n$ 的序列 $a$ 和 $b$，$b$ 是一个 $1$ 到 $n$ 的排列，$a$ 序列初始每个值都为 $0$，有 $q$ 次操作，每次操作为以下两种之一：
>
> 1. $add\ l\ r$：将区间 $[l,r]$ 内的所有数字都加上 $1$；
> 2. $query\ l\ r$：询问 $\sum_{i=l}^r\lfloor\frac{a_i}{b_i}\rfloor$ 的值。
### 输入

> 多组输入（不超过 $5$ 组），每组输入第一行包含两个整数 $n,q\ (1\leq n,q\leq10^5)$，接下去一行为 $n$ 个整数 $b_1,b_2,\cdots,b_n$，序列 $b$ 是 $1$ 到 $n$ 的一个排列，接下去 $q$ 行每行为一个字符串和两个区间的端点 $s\ l\ r\ (1\leq l\leq r\leq n)$，若 $s=add$，则执行操作 $1$，否则执行操作 $2$。

### 输出

> 对于每组询问，输出询问的答案。

### 题解

> 由于每次操作都只对区间 $+1$，最坏情况下，如果每次操作都对整个区间 $+1$，那么 $q$ 次操作后区间的和为 $\frac{q}{1}+\frac{q}{2}+\cdots+\frac{q}{n}\approx q\ln n\approx10^6$，因此对于每次 $\lfloor\frac{a_i}{b_i}\rfloor$ 增加 $1$ 时都更新一次（用树状数组更新复杂度为 $O(q\ln n\log n)\approx2\times10^7$），这样区间更新就用区间增减区间最小值的线段树维护，线段树上每个数初始为 $b_i$，每次操作 $1$ 就将区间 $[l,r]$ 内的数字都 $-1$，每当区间最小值为 $0$ 时就单点更新这个最小值，并将这个值赋值为 $b_i$，总的时间复杂度约为 $O(2q\ln n\log n)$。

### 过题代码

```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
int n, q, x, y;
char str[20];
int num[maxn];
int Min[maxn << 2], lazy[maxn << 2];
int sum[maxn];

int lowbit(int x) {
    return x & (-x);
}

void update(int Index, int x) {
    while (Index <= n) {
        sum[Index] += x;
        Index += lowbit(Index);
    }
}

int query(int Index) {
    int ret = 0;
    while (Index > 0) {
        ret += sum[Index];
        Index -= lowbit(Index);
    }
    return ret;
}

int query(int l, int r) {
    return query(r) - query(l - 1);
}

void push_up(int rt) {
    Min[rt] = min(Min[rt << 1], Min[rt << 1 | 1]);
}

void push_down(int rt) {
    if (lazy[rt] != 0) {
        lazy[rt << 1] += lazy[rt];
        lazy[rt << 1 | 1] += lazy[rt];
        Min[rt << 1] += lazy[rt];
        Min[rt << 1 | 1] += lazy[rt];
        lazy[rt] = 0;
    }
}

void build(int l, int r, int rt) {
    lazy[rt] = 0;
    if (l == r) {
        sum[l] = 0;
        Min[rt] = num[l];
        return ;
    }
    int m = (l + r) >> 1;
    build(l, m, rt << 1);
    build(m + 1, r, rt << 1 | 1);
    push_up(rt);
}

void solve(int l, int r, int rt) {
    if (l == r) {
        update(l, 1);
        Min[rt] = num[l];
        return ;
    }
    push_down(rt);
    int m = (l + r) >> 1;
    if (Min[rt << 1] == 0) {
        solve(l, m, rt << 1);
    }
    if (Min[rt << 1 | 1] == 0) {
        solve(m + 1, r, rt << 1 | 1);
    }
    push_up(rt);
}

void update(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) {
        --Min[rt];
        --lazy[rt];
        if (Min[rt] == 0) {
            solve(l, r, rt);
        }
        return ;
    }
    push_down(rt);
    int m = (l + r) >> 1;
    if (L <= m) {
        update(L, R, l, m, rt << 1);
    }
    if (m < R) {
        update(L, R, m + 1, r, rt << 1 | 1);
    }
    push_up(rt);
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    while (scanf("%d%d", &n, &q) != EOF) {
        for (int i = 1; i <= n; ++i) {
            scanf("%d", &num[i]);
        }
        build(1, n, 1);
        for (int i = 0; i < q; ++i) {
            scanf("%s%d%d", str, &x, &y);
            if (str[0] == 'a') {
                update(x, y, 1, n, 1);
            } else {
                printf("%d\n", query(x, y));
            }
        }
    }

    return 0;
}
```

## J. [Swaps and Inversions](http://acm.hdu.edu.cn/showproblem.php?pid=6318)

### 题意

> 对于一个长度为 $n$ 的序列，如果序列中存在一个逆序对，则需要花费 $x$ 的代价，为了减少代价，可以先将任意个相邻的数字进行交换，每次交换需要花费 $y$ 的代价，问总的最小代价为多少？

### 输入

> 有 $10$ 组输入，每组输入的第一行为三个整数 $n,x,y\ (1\leq n,x,y\leq10^5)$，第二行为 $n$ 个整数 $a_1,a_2,\cdots,a_n\ (-10^9\leq a_i\leq10^9)$。

### 输出

> 输出最小代价。

### 题解

> 由于每次代价为 $y$ 的操作最多能减少一个逆序对，所以代价为 $y$ 的操作次数与最终序列中剩下的逆序对的数量和，等于初始序列的逆序对数，因此答案就是总的逆序对数乘上 $\min(x,y)$。对整个序列离散化然后用树状数组求逆序对。

### 过题代码

```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
struct Node {
    int num, Index;
};

bool operator<(const Node &a, const Node &b) {
    return a.num < b.num;
}

int n, cnt;
Node node[maxn];
LL x, y, ans;
int num[maxn], sum[maxn];

int lowbit(int x) {
    return (x & (-x));
}

void update(int Index, LL x) {
    while (Index <= n) {
        sum[Index] += x;
        Index += lowbit(Index);
    }
}

int query(int Index) {
    LL ret = 0;
    while (Index > 0) {
        ret += sum[Index];
        Index -= lowbit(Index);
    }
    return ret;
}

int query(int l, int r) {
    return query(r) - query(l - 1);
}


int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    while (scanf("%d%I64d%I64d", &n, &x, &y) != EOF) {
        ans = 0;
        for (int i = 1; i <= n; ++i) {
            scanf("%d", &num[i]);
            node[i].num = num[i];
            node[i].Index = i;
            sum[i] = 0;
        }
        sort(node + 1, node + n + 1);
        cnt = 1;
        num[node[1].Index] = cnt;
        for (int i = 2; i <= n; ++i) {
            if (node[i].num != node[i - 1].num) {
                ++cnt;
            }
            num[node[i].Index] = cnt;
        }
        for (int i = 1; i <= n; ++i) {
            ans += query(num[i] + 1, n);
            update(num[i], 1);
        }
        printf("%I64d\n", min(x, y) * ans);
    }

    return 0;
}
```