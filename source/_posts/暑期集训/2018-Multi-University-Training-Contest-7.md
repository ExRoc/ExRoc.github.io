---
title: 2018 Multi-University Training Contest 7
category_bar: true
mathjax: true
date: 2018-08-15 21:10:00
updated: 2024-06-24 01:01:10
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第七场多校题解
---

链接：[2018 Multi-University Training Contest 7](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+7&source=1&searchmode=source)

过题数：4

排名：87

成员：官展鹏，冯彦博，孙昊哲

## A. [Age of Moyu](https://acm.hdu.edu.cn/showproblem.php?pid=6386)

### 题意

> 在一张 $N$ 个节点 $M$ 条边的无向图上，每条边的权值为 $C$，求从节点 $1$ 到节点 $N$ 的最小代价路径，每当路径经过的边权值发生改变时，代价加  $1$。

### 输入

> 多组输入，第一行为两个整数 $N, M\ (1 \leq N \leq 10^5, 0 \leq M \leq 2 \times 10^5)$，接下去 $M$ 行每行三个整数 $A, B, C\ (1 \leq A, B \leq N, 1 \leq C \leq 10^6)$，表示节点 $A$ 到 $B$ 之间有一条边，边的权值为 $C$，输入到文件尾。

### 输出

> 对于每组数据，输出最小权值，如果无法从 $1$ 到达 $N$，输出 $-1$。

### 题解

> 用一个 `set` 保存从节点 $1$ 到当前节点所有代价最小的路径中的前置边权值集合，到遍历节点的下一条边时，从集合中寻找是否已经有匹配的边，如果有则按照迪杰斯特拉进行最短路的更新。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
#include <vector>
#include <set>
#include <queue>
using namespace std;

typedef long long LL;
const int SIZE = 100000;
const int maxn = 100000 + 100;

inline char nc() {
    static char buf[SIZE];
    static char *p1 = buf;
    static char *p2 = buf;
    return p1 == p2 && (p2 = (p1 = buf) + fread(buf, 1, SIZE, stdin), p1 == p2)? EOF : *p1++;
}

inline int _read() {
    char ch = nc();
    if (ch == EOF) {
        return EOF;
    }
    int sum = 0;
    while (!(ch >= '0' && ch <= '9')) {
        ch = nc();
    }
    while (ch >= '0' && ch <= '9') {
        sum = sum * 10 + ch - 48;
        ch = nc();
    }
    return sum;
}

struct Node {
    int pos, w;
    Node() {}
    Node(int p, int ww) {
        pos = p;
        w = ww;
    }
};

struct Edge {
    int pos, dis, w;
    Edge() {}
    Edge(int p, int d, int ww) {
        pos = p;
        dis = d;
        w = ww;
    }
};

bool operator<(const Edge &a, const Edge &b) {
    return a.dis > b.dis;
}

int n, m, u, v, w, INF;
int dis[maxn];
set<int> st[maxn];
vector<Node> G[maxn];
priority_queue<Edge> que;

void dij() {
    que.push(Edge(1, 0, -1));
    st[1].insert(-1);
    dis[1] = 0;
    while (!que.empty()) {
        Edge tmp = que.top();
        que.pop();
        int len = G[tmp.pos].size();
        for (int i = 0; i < len; ++i) {
            int pos = G[tmp.pos][i].pos;
            int w = G[tmp.pos][i].w;
            int d = (w == tmp.w? 0 : 1);
            if (dis[pos] > tmp.dis + d) {
                dis[pos] = tmp.dis + d;
                que.push(Edge(pos, dis[pos], w));
                st[pos].clear();
                st[pos].insert(w);
            } else if (dis[pos] == tmp.dis + d && st[pos].find(w) == st[pos].end()) {
                st[pos].insert(w);
                que.push(Edge(pos, dis[pos], w));
            }
        }
    }
}

int main() {
    memset(&INF, 0x3f, sizeof(int));
    while (n = _read(), n != EOF) {
        m = _read();
        for (int i = 1; i <= n; ++i) {
            dis[i] = INF;
            G[i].clear();
            st[i].clear();
        }
        for (int i = 0; i < m; ++i) {
            u = _read();
            v = _read();
            w = _read();
            G[u].push_back(Node(v, w));
            G[v].push_back(Node(u, w));
        }
        dij();
        if (dis[n] == INF) {
            printf("-1\n");
            continue;
        }
        printf("%d\n", dis[n]);
    }

    return 0;
}
```

## E. [GuGuFishtion](https://acm.hdu.edu.cn/showproblem.php?pid=6390)

### 题意

> 给定 $n, m, p$，求
>$$
> (\sum\limits_{a=1}^m\sum\limits_{b=1}^n\frac{\phi(ab)}{\phi(a)\phi(b)})\pmod p
> $$

### 输入

> 第一行一个正整数 $T\ (1 \leq T \leq 3)$，接下去 $T$ 行每行 $3$ 个整数 $m, n, p\ (1 \leq m, n \leq 10^6, \max(m, n) < p \leq 10^9 + 7)$，数据保证 $p$ 为质数，含义如题。

### 输出

> 输出 $T$ 行，每行对应一组输入的答案。

### 题解

> 根据欧拉公式可得分子为：
> $$
> \phi(ab)=(a\times\prod\limits_{i=1}^{t_1}(1-\frac{1}{p_i}))\times(b\times\prod\limits_{j=1}^{t_2}(1-\frac{1}{p_j}))\times(\prod\limits_{k=1}^{t_3}(1-\frac{1}{p_k}))
> $$
> 其中 $p_i$ 表示是 $a$ 的质因数但不是 $b$ 的质因数的整数集合，共 $t_1$ 个元素，$p_j$ 表示是 $b$ 的质因数但不是 $a$ 的质因数的整数集合，共 $t_2$ 个元素，$p_k$ 表示同时属于 $a$ 与 $b$ 的质因数的整数集合，共 $t_3$ 个。
>
> 分母相乘得：
> $$
> \phi(a)\phi(b)=(a\times\prod\limits_{i=1}^{t_1}(1-\frac{1}{p_i}))\times(b\times\prod\limits_{j=1}^{t_2}(1-\frac{1}{p_j}))\times(\prod\limits_{k=1}^{t_3}(1-\frac{1}{p_k}))^2
> $$
> 约去分子分母相同元素，再同时乘上 $\gcd(a,b)$ 可得：
> $$
> \frac{\phi(ab)}{\phi(a)\phi(b)}=\frac{\gcd(a,b)}{\phi(\gcd(a,b))}
> $$
>
> 故原式等价于
> $$
> \sum\limits_{a=1}^{n}\sum\limits_{b=1}^{m}\sum\limits_{k=1}^{\min(n,m)}[k==\gcd(a,b)]\frac{k}{\phi(k)}=\sum\limits_{k=1}^{\min(n,m)}\frac{k}{\phi(k)}\sum\limits_{a=1}^{n}\sum\limits_{b=1}^{m}[k==\gcd(a,b)]
> $$
>
> 右式等价于统计 $a\in[1,2\cdots n]$ 与 $b\in[1,2\cdots m]$ 中最大公因数为 $k$ 的个数，可用莫比乌斯反演求出。
>
> 题目保证质数比 $n$ 与 $m$ 大，故直接求逆元最后乘起来即可。

### 过题代码

```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 1000000 + 100;
int T, n, m, MOD, cnt, ub;
bool vis[maxn];
LL inv[maxn], ans;
int prime[maxn], mu[maxn], phi[maxn];

void Prime(int n) {
    mu[1] = 1;
    phi[1] = 1;
    for (LL i = 2; i < n; ++i) {
        if (!vis[i]) {
            prime[cnt++] = i;
            mu[i] = -1;
            phi[i] = i - 1;
        }
        for (int j = 0; j < cnt && i * prime[j] < n; ++j) {
            int k = i * prime[j];
            vis[k] = true;
            if (i % prime[j] == 0) {
                mu[k] = 0;
                phi[k] = phi[i] * prime[j];
                break;
            } else {
                mu[k] = -mu[i];
                phi[k] = phi[i] * (prime[j] - 1);
            }
        }
    }
}

LL solve(int Gcd) {
    int nn = n / Gcd;
    int mm = m / Gcd;
    int ub = min(nn, mm);
    LL ans = 0;
    for (int k = 1; k <= ub; ++k) {
        ans += (LL)mu[k] * (nn / k) * (mm / k);
    }
    return ans;
}

LL gcd(LL x, LL y) {
    return y == 0? x: gcd(y, x % y);
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Prime(maxn);
    scanf("%d", &T);
    while (T--) {
        ans = 0;
        scanf("%d%d%d", &n, &m, &MOD);
        ub = min(n, m);
        inv[1] = 1;
        for (int i = 2; i <= ub; ++i) {
            inv[i] = (LL)(MOD - MOD / i) * inv[MOD % i] % MOD;
        }
        for (int i = 1; i <= ub; ++i) {
            ans += (solve(i) * i % MOD) * inv[phi[i]] % MOD;
            ans %= MOD;
        }
        printf("%I64d\n", ans);
    }

    return 0;
}
```

## J. [Sequence](https://acm.hdu.edu.cn/showproblem.php?pid=6395)

### 题意

> 给定 $A,B,C,D,P,n$，求
> $$
> \begin{cases}
> 	F_1=A \\\\
> 	F_2=B \\\\
> 	F_n=C\cdot F_{n-2}+D\cdot F_{n-1}+\lfloor\frac{P}{n}\rfloor
> \end{cases}
> $$
> 
> 对 $10^9+7$ 取模的结果

### 输入

> 第一行一个正整数 $T\ (1 \leq T \leq 20)$，接下去 $T$ 行每行 $6$ 个整数 $A,B,C,D,P,n\ (0 \leq A,B,C,D \leq 10^9,1\leq P,n\leq 10^9 + 7)$，含义如题。

### 输出

> 输出 $T$ 行，每行对应一组输入的答案。

### 题解

> $\lfloor\frac{P}{i}\rfloor$ 不同的取值在 $\sqrt{P}$ 级别，然后按此分段用矩乘快速幂递推,注意 $P$ 和 $n$ 的大小。
> 

### 过题代码

```c++
#include <cstdio>
#include <cstring>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
const int MOD = 1000000007;
int T, n;
LL P, A, B, C, D, f1, f2, f3;

LL add(LL a, LL b) {
    a += b;
    if (a >= MOD) {
        return a - MOD;
    }
    return a;
}

struct Matrix {
    LL num[3][3];

    void Init() {
        memset(num, 0, sizeof(num));
        for (int i = 0; i < 3; ++i) {
            num[i][i] = 1;
        }
    }

    void Set() {
        memset(num, 0, sizeof(num));
        num[0][0] = D;
        num[0][1] = C;
        num[0][2] = 1;
        num[1][0] = 1;
        num[2][2] = 1;
    }

    void operator*=(const Matrix &m) {
        static Matrix ans;
        for (int i = 0; i < 3; ++i) {
            for (int j = 0; j < 3; ++j) {
                ans.num[i][j] = 0;
                for (int k = 0; k < 3; ++k) {
                    ans.num[i][j] = add(ans.num[i][j], num[i][k] * m.num[k][j] % MOD);
                }
            }
        }
        memcpy(num, ans.num, sizeof(num));
    }

    void fast_pow(LL n) {
        static Matrix ans;
        for (ans.Init(); n != 0; n >>= 1) {
            if ((n & 1) == 1) {
                ans *= (*this);
            }
            (*this) *= (*this);
        }
        memcpy(num, ans.num, sizeof(num));
    }
} m;

LL For(int n) {
    for (int i = 3; i <= n; ++i) {
        f3 = add(add(C * f1 % MOD, D * f2 % MOD), P / i);
        f1 = f2;
        f2 = f3;
    }
    return f3;
}

bool judge(LL x, LL mid) {
    return P / mid == P / x;
}

LL Bsearch(LL x) {
    LL low = x;
    LL high = n + 1;
    LL mid;
    while (high - low > 1) {
        mid = (high + low) >> 1;
        if (judge(x, mid)) {
            low = mid;
        } else {
            high = mid;
        }
    }
    return low;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%I64d%I64d%I64d%I64d%I64d%d", &A, &B, &C, &D, &P, &n);
        f1 = A;
        f2 = B;
        f3 = 0;
        if (n <= 100000) {
            printf("%I64d\n", For(n));
            continue;
        }
        For(100000);
        LL x = 100000;
        LL xx;
        while (x != n) {
            xx = Bsearch(x + 1);
            m.Set();
            m.fast_pow(xx - x);
            f3 = add(m.num[0][0] * f2 % MOD, add(m.num[0][1] * f1 % MOD, m.num[0][2] * (P / xx) % MOD));
            f2 = add(add(m.num[1][0] * f2 % MOD, m.num[1][1] * f1 % MOD), m.num[1][2] * (P / xx) % MOD);
            f1 = f2;
            f2 = f3;
            x = xx;
        }
        printf("%I64d\n", f3);
    }

    return 0;
}
```

## K. [Swordsman](https://acm.hdu.edu.cn/showproblem.php?pid=6396)

### 题意

> Lawson 有 $k$ 个魔法属性 $v_1,v_2,\cdots,v_k$，总共有 $n$ 只怪物，每只怪物也有 $k$ 个属性 $a_1,a_2,\cdots,a_k$，只有满足 $v_i\geq a_i,\forall i\in[1, k]$ 时，Lawson 才能打败怪物，并且将自己的所有属性值增加为 $v_i+b_i$，每只怪物的 $k$ 个属性及被打败后增加的属性值各不相同，问 Lawson 最多能打败多少只怪物，且最终 $k$ 个属性的值为多少。

### 输入

> 第一行一个正整数 $T$，接下去 $T$ 有 $T$ 组数据，每组数据第一行为两个整数 $n,k\ (1\leq n\leq10^5,1\leq k\leq5)$，表示 $n$ 只怪物与 $k$ 个属性，第二行为 $k$ 个非负整数 $v_1,v_2,\cdots,v_k$ 表示 Lawson 的 $k$ 个属性，接下去 $n$ 行，每行 $2k$ 个非负整数 $a_1,a_2,\cdots,a_k,b_1,b_2,\cdots,b_k$，含义如题。
>
> 数据保证所有输入数据都不超过 $10^9$，且 $v_j+\sum\limits_{i=1}^n b_{i,j}\leq10^9,\forall j\in[1,k]$。
>
> 数据规模很大，注意使用快速读入。

### 输出

> 每组数据两行输出，第一行一个整数表示 Lawson 最多能杀死的怪物数，第二行为 $k$ 个整数 $v_1,v_2,\cdots,v_k$ 表示 Lawson 最终的属性值。

### 题解

> 以每个属性为关键字从小到大排序，然后使用 $k$ 个指针从小到大扫描，是否能打败对应的怪物，最多扫描 $nk$ 次。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
const int maxk = 5;
int T, n, k, iidx, cnt;
int v[maxk];
int a[maxn][maxk], b[maxn][maxk];
int idx[maxk][maxn], ed[maxk];
bool vis[maxn];

int read() {
    int ret = 0;
    char ch;
    do {
        ch = getchar();
    } while (ch < '0' || ch > '9');
    do {
        ret = ret * 10 + ch - '0';
        ch = getchar();
    } while (ch >= '0' && ch <= '9');
    return ret;
}

bool cmp(int x, int y) {
    return a[x][iidx] < a[y][iidx];
}

bool judge(int idx) {
    for (int i = 0; i < k; ++i) {
        if (v[i] < a[idx][i]) {
            return false;
        }
    }
    return true;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    T = read();
    while (T--) {
        cnt = 0;
        n = read();
        k = read();
        for (int i = 0; i < k; ++i) {
            v[i] = read();
        }
        memset(vis, 0, sizeof(bool) * n);
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < k; ++j) {
                a[i][j] = read();
            }
            for (int j = 0; j < k; ++j) {
                b[i][j] = read();
                idx[j][i] = i;
            }
        }
        for (int i = 0; i < k; ++i) {
            iidx = i;
            sort(idx[i], idx[i] + n, cmp);
        }
        memset(ed, 0, sizeof(int) * k);
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < k; ++j) {
                iidx = idx[j][ed[j]];
                if (vis[iidx]) {
                    ++ed[j];
                    continue;
                }
                if (judge(iidx)) {
                    for (int kk = 0; kk < k; ++kk) {
                        v[kk] += b[iidx][kk];
                    }
                    vis[iidx] = true;
                    ++ed[j];
                    ++cnt;
                }
            }
        }
        printf("%d\n", cnt);
        for (int i = 0; i < k; ++i) {
            if (i != 0) {
                printf(" ");
            }
            printf("%d", v[i]);
        }
        printf("\n");
    }

    return 0;
}
```

