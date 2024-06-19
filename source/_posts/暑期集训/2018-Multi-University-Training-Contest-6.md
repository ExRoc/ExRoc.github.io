---
title: 2018 Multi-University Training Contest 6
category_bar: true
mathjax: true
date: 2018-08-14 21:10:00
updated: 2024-06-20 01:29:59
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第六场多校题解
---

链接：[2018 Multi-University Training Contest 6](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+6&source=1&searchmode=source)

过题数：3

排名：135 / 718

成员：官展鹏，冯彦博，孙昊哲

## A. [oval-and-rectangle](https://acm.hdu.edu.cn/showproblem.php?pid=6362)

### 题意

> 在椭圆 $\frac{x^2}{a^2}+\frac{y^2}{b^2}=1$ 上，取一个矩形，要求满足以下条件：
>
> 1. 矩形的四个点在椭圆上；
> 2. 矩形的两条边平行于坐标轴；
> 3. 矩形的一条边为 $y=c$；
>
> 若 $c$ 可能为 $[0, b]$ 内随机的一个点，求矩形周长的期望值。
### 输入

> 第一行为一个整数 $T\ (T\leq10)$，接下去有 $T$ 行，每行两个整数 $a,b\ (0<b<a<10^5)$，用一个空白符分隔。

### 输出

> 对于每组数据，输出一个实数，只取小数点后 $6$ 位，多余位数直接舍去。

### 题解

> 用 $y$ 表示矩形的周长后，对 $y$ 从 $0$ 到 $b$ 积分，将积分后的结果除以 $b$ 就是答案。
>
> $$l=\int_0^b(4y+\frac{4a}{b}\sqrt{b^2-y^2})\ {\rm d}y$$
>
> 其中 $\int_0^b4y\ {\rm d}y=2b^2$，令 $y=b\sin x$ 则有：
>
> $$\int_0^b\frac{4a}{b}\sqrt{b^2-y^2}\ {\rm d}y=\int_0^\frac{\pi}{2}4ab\cos^2x\ {\rm d}x=\int_0^\frac{\pi}{2}2ab(1+\cos 2x) {\rm d}x=\pi ab$$
>
> 因此 $l=2b^2+\pi ab$，$ans=\frac{l}{b}=2b+\pi a$。

### 过题代码

```c++
#include <cstdio>
#include <cmath>
using namespace std;

typedef long long LL;
double PI = acos(-1.0);
int T;
double a, b, ans;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%lf%lf", &a, &b);
        ans = PI * a + 2 * b;
        ans = floor(ans * 1000000) / 1000000;
        printf("%.6f\n", ans);
    }

    return 0;
}
```

## B. [bookshelf](https://acm.hdu.edu.cn/showproblem.php?pid=6363)

### 题意

> 有 $N$ 本完全相同的书要放到一个有 $K$ 层的书架上，每层书架可以放无限本书：
>
> 1. 设第 $i$ 层的书的数量为 $cnt_i\ (0\leq cnt_i\leq N)$；
> 2. 设 $f[i]$ 为斐波那契数列的第 $i$ 项（$f[0]=0,f[1]=1,f[2]=1,f[i]=f[i-1]+f[i-2]$）；
> 3. 第 $i$ 层的稳定值为 $stable_i=f[cnt_i]$；
> 4. 第 $i$ 层的美丽值为 $beauty_i=2^{stable_i}-1$；
> 5. 书架整体的美丽值为 $score=Gcd_{i=1}^Kbeauty_i$，其中 $\gcd(0,x)=x$。
>
> 如果现在随机地把 $N$ 本书全部放到 $K$ 层书架上，求 $score$ 的期望。

### 输入

> 第一行为一个整数 $T\ (T\leq10)$，接下去 $T$ 行，每行两个整数 $n,k\ (0<n,k\leq10^6)$。

### 输出

> 对每组数据输出期望值对 $10^9+7$ 取模的结果，如果期望值为有理数 $\frac{p}{q}$，$p,q$ 都为整数且它们互质，则输出一个 $[0,10^9+6]$ 以内的整数 $a$ 使得 $p-aq$ 能被 $10^9+7$ 整除。

### 题解

> 将 $n$ 本完全相同的书分到 $k$ 层的总方案数为 $C_{n+k-1}^n$。
>
> 由斐波那契数列性质可得：$\gcd(beauty_i,beauty_j)=2^{f[\gcd(cnt_i,cnt_j)]}-1$，因此只要统计每一个可能的 $\gcd$ 值及对应的方案数相乘即可。将 $n$ 本书放到书架上所有 $\gcd$ 的可能值为 $n$ 的所有约数，对于某一个 $\gcd$ 的值，其方案数如果直接用 $C_{\frac{n}{\gcd}+k-1}^{\frac{n}{\gcd}}$ 来算的话，会将 $\gcd$ 的所有倍数重复计算（若每一层的 $cnt_i$ 都是 $k\times\gcd\ (k>1)$，则该方案数会在统计 $\gcd$ 时被计算一次，在统计 $k\times\gcd$ 时又被统计一次），为减去重复的统计，应该用容斥将所有是 $n$ 的约数且又是 $\gcd$ 倍数 $p$ 的方案数除去，方案数为 $C_{\frac{n}{p}+k-1}^{\frac{n}{p}}$，系数为莫比乌斯函数 $mu[\frac{p}{\gcd}]$，因此答案为：
>
> $$ans=\frac{\left(\sum_{\gcd|n}\left(2^{f[\gcd]}-1\right)\sum_{\gcd|p\\&p|n}C_{\frac{n}{p}+k-1}^{\frac{n}{p}}mu[\frac{p}{\gcd}]\right)}{C_{n+k-1}^n}$$

### 过题代码

```c++
#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;

typedef long long LL;
const int MOD = 1000000007;
const int phi_MOD = MOD - 1;
const int maxn = 2000000 + 100;
int T, n, k, cnt;
int prime[maxn], mu[maxn], fib[maxn];
bool vis[maxn];
int inv[maxn], pro[maxn], invpro[maxn];
vector<int> fac;

void Prepare_C() {
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

void Prime(int n) {
    mu[1] = 1;
    for (int i = 2; i <= n; ++i) {
        if (!vis[i]) {
            prime[cnt++] = i;
            mu[i] = -1;
        }
        for (int j = 0; j < cnt && i <= n / prime[j]; ++j) {
            int k = i * prime[j];
            vis[k] = true;
            if (i % prime[j] == 0) {
                mu[k] = 0;
                break;
            } else {
                mu[k] = -mu[i];
            }
        }
    }
}

int fast_pow(LL res, LL n) {
    LL ans;
    for (ans = 1; n != 0; n >>= 1) {
        if ((n & 1) == 1) {
            ans = (ans * res) % MOD;
        }
        res = (res * res) % MOD;
    }
    return ans;
}

int get_inv(int x) {
    return fast_pow(x, MOD - 2);
}

inline int add(int a, int b, int m) {
    a += b;
    if (a >= m) {
        return a - m;
    }
    if (a < 0) {
        return a + m;
    }
    return a;
}

void Prepaer_fib() {
    bool flag = false;
    int Index = 0;
    fib[0] = 0;
    fib[1] = 1;
    for (int i = 2; i < maxn; ++i) {
        if (flag) {
            fib[i] = add(fib[i - 1], fib[i - 2], phi_MOD);
        } else {
            fib[i] = fib[i - 1] + fib[i - 2];
            if (fib[i] > phi_MOD) {
                flag = true;
                Index = i;
                fib[i] -= phi_MOD;
            }
        }
    }
    for (int i = Index; i < maxn; ++i) {
        fib[i] += phi_MOD;
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Prime(maxn - 1);
    Prepare_C();
    Prepaer_fib();
    scanf("%d", &T);
    while (T--) {
        fac.clear();
        scanf("%d%d", &n, &k);
        for (int i = 1; i <= n / i; ++i) {
            if (n % i == 0) {
                fac.push_back(i);
                if (i != n / i) {
                    fac.push_back(n / i);
                }
            }
        }
        sort(fac.begin(), fac.end());
        int len = fac.size();
        LL ans = 0;
        for (int i = 0; i < len; ++i) {
            int tmp = add(fast_pow(2, fib[fac[i]]), -1, MOD);
            int x = 0;
            for (int j = i; j < len; ++j) {
                if (fac[j] % fac[i] == 0) {
                    x = add(x, get_C(n / fac[j] + k - 1, n / fac[j]) * mu[fac[j] / fac[i]], MOD);
                }
            }
            ans = add(ans, (LL)tmp * x % MOD, MOD);
        }
        printf("%d\n", (int)(ans * get_inv(get_C(n + k - 1, n)) % MOD));
    }

    return 0;
}
```

## D. [Shoot Game](https://acm.hdu.edu.cn/showproblem.php?pid=6365)

### 题意

> 在一个平面直角坐标系上有 $n$ 个障碍物，第 $i$ 个障碍物用三个整数 $(H_i,L_i,R_i)$ 描述，表示这个障碍物在高度为 $H_i$ 处，左端点为 $L_i$，右端点为 $R_i$，它的防御力为 $W_i$，一个人在 $(0,0)$ 处发射激光，每次可以选择一个能量 $X$ 的激光进行发射，激光可以穿透障碍物，所有防御力不大于激光能量的障碍物都会被消灭，问要消灭所有障碍物，最少需要消耗多少能量。

### 输入

> 第一行为一个整数 $T\ (T\leq10)$，接下去为 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq300)$，接下去 $n$ 行每行 $4$ 个整数 $H_i,L_i,R_i,W_i\ (1\leq H_i\leq10^9,−10^9\leq L_i\leq R_i\leq10^9,0\leq W_i\leq10^9)$。

### 输出

> 对于每组数据，输出最少需要小号的能量值。

### 题解

> 从 $(0,0)$ 处发射激光，则可以将所有端点按极角序进行离散化，这样一条射线可以同时射中的障碍物就转化为在一个一维坐标轴上的相互覆盖的线段了，用 $dp[i][j]$ 表示要将从第 $i$ 个端点到第 $j$ 个端点内所有线段消灭所需要的最小能量，则有 $dp$ 方程：
>
> $$dp[i][j]=\min(dp[i][k-1]+dp[k+1][j]+W_{max})\ k\in[i,j]$$
>
> 其中 $W_{max}$ 为从端点 $i$ 到端点 $j$ 之间所有线段中需要的最大能量。

### 过题代码
```c++
#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 600 + 10;
struct Point {
    LL x, y;
    Point() {}
    Point(LL xx, LL yy) {
        x = xx;
        y = yy;
    }
};

LL operator^(const Point &a, const Point &b) {
    return a.x * b.y - a.y * b.x;
}

bool operator<(const Point &a, const Point &b) {
    return (a ^ b) < 0;
}

bool operator==(const Point &a, const Point &b) {
    return (a ^ b) == 0;
}

struct Line {
    LL l, r, h, w;
};

int T, n;
LL INF;
vector<Point> sand;
Line line[maxn];
LL dp[maxn][maxn];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    memset(&INF, 0x3f, sizeof(LL));
    scanf("%d", &T);
    while (T--) {
        sand.clear();
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) {
            scanf("%I64d%I64d%I64d%I64d", &line[i].h, &line[i].l, &line[i].r, &line[i].w);
            sand.push_back(Point(line[i].l, line[i].h));
            sand.push_back(Point(line[i].r, line[i].h));
        }
        sort(sand.begin(), sand.end());
        sand.erase(unique(sand.begin(), sand.end()), sand.end());
        for (int i = 1; i <= n; ++i) {
            line[i].l = lower_bound(sand.begin(), sand.end(), Point(line[i].l, line[i].h)) - sand.begin() + 1;
            line[i].r = lower_bound(sand.begin(), sand.end(), Point(line[i].r, line[i].h)) - sand.begin() + 1;
        }
        for (int i = 1; i <= 2 * n; ++i) {
            memset(dp[i], 0, sizeof(LL) * (i + 1));
        }
        for (int len = 1; len <= 2 * n; ++len) {
            for (int i = 1; i + len <= 2 * n; ++i) {
                int j = i + len;
                int Max = 0;
                for (int k = 1; k <= n; ++k) {
                    if (line[k].l < i || line[k].r > j) {
                        continue;
                    }
                    if (Max < line[k].w) {
                        Max = line[k].w;
                    }
                }
                dp[i][j] = INF;
                for (int k = i; k <= j; ++k) {
                    dp[i][j] = min(dp[i][j], dp[i][k - 1] + dp[k + 1][j] + Max);
                }
            }
        }
        printf("%I64d\n", dp[1][2 * n]);
    }

    return 0;
}
```

## I. [Werewolf](https://acm.hdu.edu.cn/showproblem.php?pid=6370)

### 题意

> 有 $n$ 个人玩一个狼人游戏，每个人的身份只可能是“村民”或者“狼人”，如果一个人是村民，那么他只能说真话，如果他是狼人，则他可以说真话也可以说假话，现在每个人都要指认另一个人的身份，问有多少人的身份可以确定是村民，有多少人的身份可以确定是狼人。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10)$，接下去有 $T$ 组数据，每组数据第 $1$ 行为一个整数 $n\ (1\leq n\leq10^5)$，接下去有 $n$ 行，第 $i$ 行为一个整数 $x\ (1\leq x\leq n)$ 与一个字符串 $S\ (S\in\{"villager","werewolf"\})$，表示第 $i$ 个人指认第 $x$ 个人的身份为村民 / 狼人。

### 输出

> 对于每组数据，依次输出一定为村民的玩家数量，与一定为狼人的玩家数量。

### 题解

> 不论什么情况下，所有人都有可能为狼人，因此一定为村民的玩家数量等于 $0$，现在分析一定为狼人的玩家数量。如果 $a$ 指认 $b$ 为村民，则从 $a$ 向 $b$ 连一条有向的村民边，如果 $a$ 指认 $b$ 为狼人，则从 $a$ 向 $b$ 连一条有向的狼人边，将所有以村民边相连的加入到同一个连通块内，如果是一个 $6$ 字形的连通块，则连通块内所有玩家都有可能是村民。如果是一棵树（若 $a$ 有一条村民边指向 $b$，则将 $b$ 作为 $a$ 的父节点），则狼人边一定是从树根连出的（因为所有点的出度都为 $1$，只有树根的出度为 $0$），假设树根为狼人，则所有指向树根的节点都是狼人，以此类推则整棵树都是狼人，假设树根为村民，则树根指向的节点是狼人，则以该节点为父节点的整颗子树上的节点都是狼人，因此两种假设下，一定为狼人的节点就是树根指向的节点所代表的子树。

### 过题代码

```c++
#include <cstdio>
#include <vector>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
int T, n;
int pos;
char str[20];
int fa[maxn], son[maxn];
bool vis[maxn];
vector<int> G[maxn];
vector<pair<int, int> > edge;

void Init() {
    edge.clear();
    for (int i = 1; i <= n; ++i) {
        fa[i] = i;
        vis[i] = false;
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

void dfs(int x) {
    if (vis[x]) {
        return ;
    }
    vis[x] = true;
    son[x] = 1;
    int len = G[x].size();
    for (int i = 0; i < len; ++i) {
        int pos = G[x][i];
        dfs(pos);
        son[x] += son[pos];
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
        for (int i = 1; i <= n; ++i) {
            scanf("%d %s", &pos, str);
            if (str[0] == 'w') {
                edge.push_back(make_pair(i, pos));
            } else {
                unit(i, pos);
                G[pos].push_back(i);
            }
        }
        for (int i = 1; i <= n; ++i) {
            if (!vis[Find(i)]) {
                dfs(Find(i));
            }
        }
        int ans = 0;
        int len = edge.size();
        for (int i = 0; i < len; ++i) {
            int u = edge[i].first;
            int v = edge[i].second;
            if (Find(u) == Find(v)) {
                ans += son[v];
            }
        }
        printf("0 %d\n", ans);
    }

    return 0;
}
```

## L. [Pinball](https://acm.hdu.edu.cn/showproblem.php?pid=6373)

### 题意

> 在以下平面直角坐标系中，有一经过点 $(-a,b)$ 的斜面，斜面底部在原点，在斜面上方 $(x,y)$ 处有一小球做自由落体运动，在碰到斜面后小球做完全弹性碰撞，碰撞过程无能量损失，重力加速度为 $g=9.8m/s^2$，数据保证小球最少撞到斜面上一次。
>
> ![](/img/2018-Multi-University-Training-Contest/Pinball.jpg)
>
> 问在小球离开斜面之前，将与斜面发生多少次碰撞。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq100)$，接下去有 $T$ 组数据，每组数据为四个整数 $a,b,x,y\ (1\leq a,b,-x,y\leq100)$，含义如题。

### 输出

> 输出答案，数据保证答案不超过 $50$。

### 题解

> 将速度按与斜面平行与垂直方向分解，则在与斜面平行方向上，小球在做匀加速直线运动，且加速度为 $a_x=\frac{gb}{\sqrt{a^2+b^2}}$，在垂直于斜面方向上，第一次接触斜面之前做自由落体运动，在接触到斜面之后做周期性的上抛运动（加速度均为 $a_y=\frac{ga}{\sqrt{a^2+b^2}}$）。
>
> 可以先计算出小球在沿平行于斜面方向上运动的时间 $tx$，第一次接触斜面之前的时间 $t_{y1}$ 与上抛运动的周期 $t_{y2}$，则小球与斜面碰撞的次数为：$ans=\lfloor\frac{t_x-t_{y1}}{t_{y2}}\rfloor+1$。

### 过题代码

```c++
#include <cstdio>
#include <cmath>
using namespace std;

typedef long long LL;
const double g = 9.8;
int T;
double a, b, c, d;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%lf%lf%lf%lf", &a, &b, &c, &d);
        c = -c;
        double e = (a * b * d + a * a * c) / (a * a + b * b);
        double f = (b * b * d + a * b * c) / (a * a + b * b);
        double sx = sqrt(e * e + f * f);
        double ax = g * b / sqrt(a * a + b * b);
        double tx = sqrt(2 * sx / ax);
        double ay = g * a / sqrt(a * a + b * b);
        double sy = sqrt((c - e) * (c - e) + (d - f) * (d - f));
        double ty1 = sqrt(2 * sy / ay);
        double vy = ay * ty1;
        double ty2 = vy / ay * 2;
        int ans = (tx - ty1) / ty2 + 1;
        printf("%d\n", ans);
    }

    return 0;
}
```

