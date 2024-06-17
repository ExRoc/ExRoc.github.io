---
title: 2018 Multi-University Training Contest 1
category_bar: true
mathjax: true
date: 2018-08-09 21:10:00
updated: 2024-06-17 00:54:54
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第一场多校题解
---

链接：[2018 Multi-University Training Contest 1](http://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+1&source=1&searchmode=source)

过题数：6

排名：78 / 818

成员：官展鹏，冯彦博，孙昊哲

## A. [Maximum Multiple](http://acm.hdu.edu.cn/showproblem.php?pid=6298)

### 题意

> 给定一个整数 $n$，找到三个整数 $x,y,z$，要求满足 $x+y+z=n$ 且 $x,y,z$ 都能整除 $n$，要使得 $xyz$ 的值最大，求 $xyz$ 的最大值。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10^6)$，表示数据组数，接下去 $T$ 行每行一个整数 $n\ (1\leq n\leq10^6)$。

### 输出

> 如果存在满足条件的 $x,y,z$ 的值，输出其中 $xyz$ 的最大值，如果不存在满足条件的值，则输出 $-1$。

### 题解

> 暴力打表找规律，可以发现如果 $n$ 是 $3$ 的倍数，那么 $x,y,z$ 的值一定都是 $\frac{n}{3}$，如果 $n$ 是 $4$ 的倍数，那么 $x,y,z$ 其中一个数是 $\frac{n}{2}$，另外两个数是 $\frac{n}{4}$，其他情况都输出 $-1$。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
int T;
LL n, a, b, c, ans;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%I64d", &n);
        a = b = c = -1;
        if (n % 3 == 0) {
            a = b = c = n / 3;
        } else if (n % 4 == 0) {
            a = b = n / 4;
            c = a * 2;
        }
        if (a == -1) {
            printf("-1\n");
        } else {
            printf("%I64d\n", a * b * c);
        }
    }

    return 0;
}
```



## B. [Balanced Sequence](http://acm.hdu.edu.cn/showproblem.php?pid=6299)

### 题意

> 给出 $n$ 个只包含左右括号的字符串，将这些字符串拼起来，要求最终拼出来的括号序列中，最长的完全匹配括号子序列的长度最长，问最长子序列长度。

### 输入

> 第一行为一个整数 $T$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq10^5)$，后面 $n$ 行每行为一个只包含左右括号的字符串 $s_i\ (1\leq|s_i|\leq10^5)$，数据保证 $|s_i|$ 的和不超过 $5\times10^6$。

### 输出

> 输出最长完全匹配子序列长度。

### 题解

> 可以发现，每个字符串中，已经匹配的左右括号，不论以任何顺序与其他字符串连接，这些已经匹配的左右括号对都不会改变，用栈模拟匹配后，最后只会剩下最前面的 $l$ 个 `)` 和最后面的 $r$ 个 `(`，类似 `)))((` 的形式，只有这些括号在和其他字符串匹配的时候才会产生新的括号匹配子序列。
>
> 如果字符串 $a$ 有 $a_l$ 个 `)` 和 $a_r$ 个 `(`，字符串 $b$ 有 $b_l$ 个 `)` 和 $b_r$ 个 `(`，将 $a$ 放在 $b$ 的前面（不必相邻），则会产生 $x=\min(a_r,b_l)$ 对新的括号匹配子序列，将 $b$ 放在 $a$ 的前面，则会产生 $y=\min(b_r,a_l)$ 对新的括号匹配子序列，现在贪心地对所有字符串进行排序，如果 $x>y$，则把 $a$ 排在 $b$ 的前面，如果 $x<y$ 大，则把 $b$ 排在 $a$ 的前面，如果 $x=y$，则把含有 `(` 个数多的放在前面，最后扫一遍整个排序拼接后的字符串，就可以计得到答案。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
struct Node {
    int l, m, r;
    Node() {
        l = m = r = 0;
    }
    Node(int L, int M, int R) {
        l = L;
        m = M;
        r = R;
    }
};

bool operator<(const Node &a, const Node &b) {
    int aa = min(a.r, b.l);
    int bb = min(b.r, a.l);
    if(aa == bb) {
        return a.r > b.r;
    }
    return aa > bb;
}

int T, n;
char str[maxn];
Node node[maxn];

Node Get() {
    int len = strlen(str + 1);
    Node ret;
    ret.l = ret.r = 0;
    for (int i = 1; i <= len; ++i) {
        if (str[i] == ')') {
            if (ret.r != 0) {
                --ret.r;
            } else {
                ++ret.l;
            }
        } else {
            ++ret.r;
        }
    }
    ret.m = len - ret.l - ret.r;
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
            scanf("%s", str + 1);
            node[i] = Get();
        }
        sort(node, node + n);
        Node tmp = node[0];
        Node ll = node[0];
        Node rr;
        for (int i = 1; i < n; ++i) {
            rr = node[i];
            tmp.l = rr.l + ll.l - min(ll.r, rr.l);
            tmp.r = ll.r + rr.r - min(ll.r, rr.l);
            tmp.m = ll.m + rr.m + 2 * min(ll.r, rr.l);
            ll = tmp;
        }
        printf("%d\n", tmp.m);
    }

    return 0;
}
```

## C. [Triangle Partition](http://acm.hdu.edu.cn/showproblem.php?pid=6300)

### 题意

> 在平面直角坐标系上给出 $3n$ 个点，每 $3$ 个点连成一个三角形，共连成 $n$ 个三角形，要求任意两个三角形都没有公共点，输出连接三角形的方案。

### 输入

> 第一行为一个整数 $T$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq1000)$，接下去的 $3n$ 行每行两个整数 $x_i,y_i\ (-10^9\leq x_i,y_i\leq10^9)$，数据保证所有 $n$ 的和不超过 $10000$，且不存在三点共线。

### 输出

> 输出 $n$ 行，每行 $3$ 个整数 $a,b,c\ (1\leq a,b,c\leq3n)$，表示选择第 $a,b,c$ 个点构成一个三角形。

### 题解

> 将所有点按横坐标从小到大排序，选取连续的三个点，就能构成合法的三角形。

### 过题代码

```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 3000 + 100;
struct Node {
    int x, y;
    int Index;
};

bool operator<(const Node &a, const Node &b) {
    return (a.x == b.x? a.y < b.y: a.x < b.x);
}

int T, n;
Node node[maxn];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        n *= 3;
        for (int i = 1; i <= n; ++i) {
            scanf("%d%d", &node[i].x, &node[i].y);
            node[i].Index = i;
        }
        sort(node + 1, node + 1 + n);
        for (int i = 1; i <= n; ++i) {
            if (i != 1) {
                printf(" ");
            }
            printf("%d", node[i].Index);
        }
        printf("\n");
    }

    return 0;
}
```

## D. [Distinct Values](http://acm.hdu.edu.cn/showproblem.php?pid=6301)

### 题意

> 构造一个长度为 $n$ 的正整数序列，要求这个序列内对于所有给定的 $m$ 个区间 $[l_i,r_i]$，都满足区间内所有数字两两互不相等，求字典序最小的合法序列。

### 输入

> 第一行为一个整数 $T$，接下去 $T$ 组数据，每组数据第一行为两个整数 $n,m\ (1\leq n,m\leq10^5)$，接下去 $m$ 行每行两个整数 $l_i,r_i\ (1\leq l_i,r_i\leq n)$，数据保证所有 $n$ 和 $m$ 至少一个的和不超过 $10^6$。

### 输出

> 输出一个长度为 $n$ 的合法序列。

### 题解

> 将所有区间按 $l_i$ 为主关键字，$r_i$ 为次关键字排序，然后 $O(n)$ 预处理出每个数字对应的最靠前的左区间下标 $head_i$，如果某个点不在任何一个区间内，则 $head_i=i$，然后先把 $1$ 到 $n$ 所有数字放到小顶堆内，从前往后扫，每次取出堆顶的值放入当前位置，如果出现 $head_i\neq head_{i+1}$，就先将区间 $[head_i,head_{i+1})$ 内的所有放下去的数字收回到小顶堆，就可以得到答案。

### 过题代码

```c++
#include <cstdio>
#include <vector>
#include <queue>
#include <algorithm>
#include <functional>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
struct Node {
    int l, r;
};

bool operator<(const Node &a, const Node &b) {
    return (a.l == b.l? a.r < a.r: a.l < b.l);
}

int T, n, m;
Node node[maxn];
int ans[maxn], head[maxn];
priority_queue<int, vector<int>, greater<int> > que;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        while (!que.empty()) {
            que.pop();
        }
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; ++i) {
            head[i] = i;
            que.push(i);
        }
        for (int i = 0; i < m; ++i) {
            scanf("%d%d", &node[i].l, &node[i].r);
        }
        sort(node, node + m);
        int End = 1;
        for (int i = 0; i < m; ++i) {
            End = max(End, node[i].l);
            while (End <= node[i].r) {
                head[End] = node[i].l;
                ++End;
            }
        }
        ans[1] = 1;
        que.pop();
        for (int i = 2; i <= n; ++i) {
            if (head[i] != head[i - 1]) {
                for (int j = head[i - 1]; j < head[i]; ++j) {
                    que.push(ans[j]);
                }
            }
            ans[i] = que.top();
            que.pop();
        }
        for (int i = 1; i <= n; ++i) {
            if (i != 1) {
                printf(" ");
            }
            printf("%d", ans[i]);
        }
        printf("\n");
    }

    return 0;
}
```

## G. [Chiaki Sequence Revisited](http://acm.hdu.edu.cn/showproblem.php?pid=6304)

### 题意

> 定义一个序列
>
> $$a_n=\begin{cases}1&n=1,2\\\\a_{n-a_{n-1}}+a_{n-1-a_{n-2}}&n\geq3\end{cases}$$
>
> 求 $(\sum_{i=1}^na_i)\%(10^9+7)$ 的值。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq10^5)$，表示有 $T$ 组数据，每组数据包含一个整数 $n\ (1\leq n\leq10^{18})$。

### 输出

> 每组数据输出 $(\sum_{i=1}^na_i)\%(10^9+7)$。

### 题解

> 先打出前 $40$ 个 $a_n$ 的表：$1\ 1\ 2\ 2\ 3\ 4\ 4\ 4\ 5\ 6\ 6\ 7\ 8\ 8\ 8\ 8\ 9\ 10\ 10\ 11\ 12\ 12\ 12\ 13\ 14\ 14\ 15\ 16\ 16\ 16\ 16\ 16\ 17\ 18\ 18\ 19\ 20\ 20\ 20\ 21$ 首先对于任意两个相邻且不相等的数字，它们的差都为 $1$。我将除了第 $1$ 个数字和第 $40$ 个数字的连续相同的数字统计个数以及个数的前缀和打表：
>
> |  ${\bf a}$   |   $1$    |   $2$    | $3$  |   $4$    | $5$  | $6$  | $7$  |   $8$    | $9$  | $10$ | $11$ | $12$ | $13$ | $14$ | $15$ |   $16$   | $17$ | $18$ | $19$ | $20$ |
> | :----------: | :------: | :------: | :--: | :------: | :--: | :--: | :--: | :------: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :------: | :--: | :--: | :--: | ---- |
> | ${\bf cnt}$  |   $1$    |   $2$    | $1$  |   $3$    | $1$  | $2$  | $1$  |   $4$    | $1$  | $2$  | $1$  | $3$  | $1$  | $2$  | $1$  |   $5$    | $1$  | $2$  | $1$  | $3$  |
> | ${\bf sum}$  |   $1$    |   $3$    | $4$  |   $7$    | $8$  | $10$ | $11$ |   $15$   | $16$ | $18$ | $19$ | $22$ | $23$ | $25$ | $26$ |   $31$   | $32$ | $34$ | $35$ | $38$ |
> | ${\bf mark}$ | $\Delta$ | $\Delta$ |      | $\Delta$ |      |      |      | $\Delta$ |      |      |      |      |      |      |      | $\Delta$ |      |      |      |      |
> 
>
> 把 $cnt$ 中每个数字第一次出现的位置打上标记，可以发现这是一个 $2^k$ 的循环，对于每一个数字 $n$，我们首先可以找到小于等于它的最大的 $2^k-1$ 的 $sum$ 值，$2^k-1$ 个数字其对应的 $a$ 的值只增加了 $2^{k-1}$ 个数字，将 $a_n$ 加上 $2^{k-1}$，$n$ 减去 $2^k-1$，不断查找下去，就可以在 $O(\log n\log(\log n))$ 次内确定 $a_n$ 的值。而从 $a_2$ 到 $a_n$ 的所有数字的前缀和，通过观察可以发现，如果 $a_n$ 是某段连续相同数字的最后一项，可以先计算 $2^0$ 的等差数列求和，再计算 $2^1$ 等差数列求和……每次都计算一个首项为 $2^k$，末项为 $\lfloor\frac{n}{2^k}\rfloor\times2^k$，项数为 $\lfloor\frac{n}{2^k}\rfloor$，的一个等差数列，直到 $2^k>a_n$，停止计算，就能得到 $a_2$ 到 $a_n$ 的所有数字的前缀和；而如果 $a_n$ 不是某段连续相同数字的最后一项，就要先计算出 $a_n$ 的前一个连续的相同数字的最后一项，按照上述方法计算后，加上 $a_n$ 这段连续的数字中，$a_n$ 前面的数字个数。由于前面的规律是在去掉第 $1$ 个数字找到的，所以计算最后答案的时候要 $+1$。
>
> 最后注意答案会乘爆 `long long`，所以在乘法之前应该先取模一次。

### 过题代码

```c++
#include <cstdio>
#include <algorithm>
using namespace std;

typedef long long LL;
const LL MOD = 1000000007;
const LL inv = 500000004;
int T;
LL n;
LL two[64], two_1[64];
LL *it;

LL get_a(LL n) {
    if (n == 1) {
        return 0;
    }
    --n;
    LL ret = 0;
    while (n != 0) {
        it = upper_bound(two_1, two_1 + 62, n);
        --it;
        ret += two[it - two_1 - 1];
        n -= *it;
    }
    return ret;
}

LL solve(LL d, LL n) {
    return (((d + (n / d * d % MOD)) % MOD) * ((n / d) % MOD) % MOD) * inv % MOD;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    two[0] = 1;
    for (int i = 1; i < 62; ++i) {
        two[i] = two[i - 1] * 2;
    }
    for (int i = 0; i < 62; ++i) {
        two_1[i] = two[i] - 1;
    }
    scanf("%d", &T);
    while (T--) {
        scanf("%I64d", &n);
        if (n == 1) {
            printf("1\n");
            continue;
        }
        LL a_n = get_a(n);
        LL n_1 = n;
        LL a_n_1;
        do {
            --n_1;
            a_n_1 = get_a(n_1);
        } while (a_n_1 == a_n);
        LL ans = 0;
        for (int i = 0; two[i] <= a_n_1; ++i) {
            ans += solve(two[i], a_n_1);
            ans %= MOD;
        }
        ans = (ans + (a_n % MOD) * (n - n_1) % MOD) % MOD;
        ans = (ans + 1) % MOD;
        printf("%I64d\n", ans);
    }

    return 0;
}
```

## H. [RMQ Similar Sequence](http://acm.hdu.edu.cn/showproblem.php?pid=6305)

### 题意

> 首先给定一个长度为 $n$ 的整数序列 $A=\lbrace a_1,a_2,\cdots,a_n\rbrace$，定义 $RMQ\lbrace A,l,r\rbrace$ 表示查找区间 $[l,r]$ 内的最大值的下标，如果存在多个最大值，则取最左边的数字的下标。现在要构造一个长度为 $n$ 的序列 $B=\lbrace b_1,b_2,\cdots,b_n\rbrace$，要求每个数字都是属于区间 $[0,1]$ 内的实数，且对于每一个 $RMQ\lbrace B,l,r\rbrace$，其询问结果都和 $RMQ\lbrace A,l,r\rbrace$ 相同，问所有满足条件的序列 $B$ 的 $\sum_{i=1}^nb_i$ 的期望值。

### 输入

> 第一行为一个整数 $T$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq10^6)$，第二行为 $n$ 个整数 $a_1,a_2,\cdots,a_n\ (1\leq a_i\leq n)$，数据保证所有 $n$ 的和不超过 $3\times10^6$。

### 输出

> 输出期望对 $10^9+7$ 取模的结果，如果期望无法被 $10^9+7$ 整除，先化简期望为 $\frac{p}{q}$，保证 $p$ 和 $q$ 互质且都为正整数的情况下，输出 $a\ (0\leq a\leq10^9+6)$ 的值，要求 $p-aq$ 能够被 $10^9+7$ 整除。

### 题解

> 根据题意建立一棵序列 $A$ 的笛卡尔树，题目即求笛卡尔树与数列 $A$ 同构的序列 $B$ 的序列和的期望，由于 $B$ 内每个数字都是实数，所以任意两个数字相等的概率趋近于 $0$，对于每一个节点对应的区间，节点的数字大于该区间内所有数字的概率为 $\frac{1}{son_i}$，其中 $son_i$ 为节点 $i$ 对应子树的节点数，将所有节点的满足条件的概率相乘，再乘上序列求和的期望值 $\frac{n}{2}$，就是答案。最后要求期望对 $10^9+7$ 的取模的结果，就是将结果 $\frac{p}{q}$ 转化成 $p\times q^{-1}\mod10^9+7$。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
using namespace std;

typedef long long LL;
const int maxn = 1000000 + 100;
const LL MOD = 1000000007;

LL ans;
LL inv[maxn];

struct Tree {
    int root, top, n;
    int sta[maxn], l[maxn], r[maxn];
    bool vis[maxn];

    void build(int *num, int nn) {
        n = nn;
        top = 0;
        memset(l, 0, sizeof(int) * (n + 1));
        memset(r, 0, sizeof(int) * (n + 1));
        memset(vis, 0, sizeof(bool) * (n + 1));
        for (int i = 1; i <= n; ++i) {
            int tmp = top;
            while (top > 0 && num[sta[top - 1]] < num[i]) {
                --top;
            }
            if (top != 0) {
                r[sta[top - 1]] = i;
            }
            if (top < tmp) {
                l[i] = sta[top];
            }
            sta[top++] = i;
        }
        for (int i = 1; i <= n; ++i) {
            vis[l[i]] = vis[r[i]] = true;
        }
        for (int i = 1; i <= n; ++i) {
            if(!vis[i]) {
                root = i;
            }
        }
    }

    int dfs(int x) {
        int cnt = 1;
        if (l[x] != 0) {
            cnt += dfs(l[x]);
        }
        if (r[x] != 0) {
            cnt += dfs(r[x]);
        }
        ans = (ans * inv[cnt]) % MOD;
        return cnt;
    }
};

int T, n;
int num[maxn];
Tree t;

void Init() {
    inv[1] = 1;
    for (int i = 2; i < maxn; ++i) {
        inv[i] = (MOD - MOD / i) * inv[MOD % i] % MOD;
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Init();
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        ans = (n * inv[2]) % MOD;
        for (int i = 1; i <= n; ++i) {
            scanf("%d", &num[i]);
        }
        t.build(num, n);
        t.dfs(t.root);
        printf("%I64d\n", ans);
    }

    return 0;
}
```

## K. [Time Zone](http://acm.hdu.edu.cn/showproblem.php?pid=6308)

### 题意

> 给出北京时间，要求计算其他时间。

### 输入

> 第一行为一个整数 $T$，接下去 $T$ 行每行前两个数字为 $h,m\ (0\leq h\leq23,0\leq m\leq59)$，表示北京时间，接着是一个格式为 `UTC+X` / `UTC-X` / `UTC+X.Y` / `UTC-X.Y` 的字符串，其中 $0\leq X,X.Y\leq14,0\leq Y\leq9$。

### 输出

> 输出给定 `UTC-X[.Y]` 对应的时间。

### 题解

> 将所有数字都转化为分钟后按照题意模拟，注意精度误差，不要读入小数。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
int T;
int h, m, a, b;
char ch;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while(T--) {
        b = 0;
        scanf("%d %d UTC%c%d.%d", &h, &m, &ch, &a, &b);
        m += h * 60;
        b = b * 6 + a * 60;
        if(ch == '-') {
            b *= -1;
        }
        m += b - 480;
        m = (m % (24 * 60) + 24 * 60) % (24 * 60);
        h = m / 60;
        m = m % 60;
        printf("%02d:%02d\n", h, m);
    }

    return 0;
}
```