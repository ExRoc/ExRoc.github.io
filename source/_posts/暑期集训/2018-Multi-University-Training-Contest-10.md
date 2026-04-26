---
title: 2018 Multi-University Training Contest 10
category_bar: true
mathjax: true
date: 2018-11-20 20:15:55
index_img: /img/cover/ACM.jpg
banner_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第十场多校题解
---

链接：[2018 Multi-University Training Contest 10](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+10&source=1&searchmode=source)

过题数：4

排名：232

成员：孙昊哲、官展鹏

## E. [TeaTree](https://acm.hdu.edu.cn/showproblem.php?pid=6430)

### 题意

> 一棵 $n$ 个节点的树，树根为 $1$，第 $i$ 个节点权值为 $v_i$，对于每个节点，求出以该节点为最近公共祖先的所有节点对权值 $\gcd$ 的最大值。

### 输入

> 第一行为一个整数 $n\ (1 \leq n \leq 10^5)$，第二行为 $n - 1$ 个整数 $f_i\ (f_i \leq i)$（$i$ 从 $1$ 开始），表示节点 $i + 1$ 的父节点编号，第三行为 $n$ 个整数 $v_i\ (1 \leq v_i \leq 10^5)$，表示每个节点的权值。

### 输出

> 输出 $n$ 行，第 $i$ 行为题目所求关于节点 $i$ 的答案。

### 题解

> 先 $O(n \ln n)$ 预处理出 $10^5$ 以内所有数字的约数，再将每个节点按权值映射为约数集合；深搜整棵树，每个节点完成搜索后存的是当前节点对应子树的所有节点权值的约数集合，处理到某个节点时，尝试将所有子节点对应的约数集合合并，在合并的过程中计算以当前节点为最近公共祖先的节点对权值最大公约数：遍历其中一个子节点的约数集合，从另一个集合中查找是否存在相同约数，如果存在说明该约数是一个满足条件的约数，取所有遍历结果的最大值就是答案。
>
> 最后使用启发式合并可将时间复杂度降低至 $O(n \log n)$，注意：使用集合合并完成后，需要删除小的集合，否则会 MLE。

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 100000 + 100;
int n, f, x;
int ans[maxn];
set<int> fac[maxn], node[maxn];
vector<int> G[maxn];

void dfs(int root) {
    ans[root] = -1;
    for (int pos : G[root]) {
        dfs(pos);
        if (node[root].size() < node[pos].size()) {
            swap(node[root], node[pos]);
        }
        for (int x : node[pos]) {
            if (node[root].find(x) != node[root].end()) {
                ans[root] = max(ans[root], x);
            }
        }
        for (int x : node[pos]) {
            node[root].insert(x);
        }
        node[pos].clear();
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    int mx = 0;
    for (int i = 1; i < maxn; ++i) {
        for (int j = i; j < maxn; j += i) {
            fac[j].insert(i);
            mx = max(mx, (int) fac[j].size());
        }
    }
    cin >> n;
    for (int i = 2; i <= n; ++i) {
        cin >> f;
        G[f].push_back(i);
    }
    for (int i = 1; i <= n; ++i) {
        cin >> x;
        node[i] = fac[x];
    }
    dfs(1);
    for (int i = 1; i <= n; ++i) {
        cout << ans[i] << endl;
    }

    return 0;
}
```

## G. [Cyclic](https://acm.hdu.edu.cn/showproblem.php?pid=6432)

### 题意

> 计算满足以下条件的**本质不同**的全排列的数量：
>
> 长度为 $n$，下标范围和数字范围都是 $[0, n)$，对于任意 $i \in [0, n)$ 都不满足 $(p_i + 1) \% n = p_{(i + 1) \% n}$。
>
> 若一个排列通过循环右移能变成另一个排列，则认为这两个排列本质相同。

### 输入

> 第一行为一个整数 $T\ (1 \leq T \leq 20)$ 表示数据组数，每组数据一个整数 $n\ (1 \leq n \leq 10^5)$，含义如题。

### 输出

> 对于每组数据输出一行表示方案数对 $998244353$ 取余的结果。

### 题解

> 首先考虑如何处理**本质不同**，由于两个排列只要循环移动后完全相同就认为本质相同，那我们可以将第一个数字 $0$ 固定在第 $0$ 位，再排列其它数字，这样计算的所有排列都是本质不同的。
>
> 完全不包含约束条件 $P: (p_i + 1) \% n = p_{(i + 1) \% n}$ 的全排列方案数比较难求，尝试求其补集：至少有一个 $i$ 满足条件 $P$ 的方案数，再用全集方案数 $(n - 1)!$ 减去补集方案数就是答案（这里已固定数字 $0$ 的位置，所以只有 $n - 1$ 个数字可排列）。
>
> 考虑求至少有一个 $i$ 满足条件 $P$ 的方案数，相当于先从 $n$ 个数字里选一个 $i$ 与 $(i + 1) \% n$ 绑定（方案数 $C_n^1$），再将剩下能自由排列的 $n - 2$ 个元素做全排列（方案数 $(n - 2)!$，注意这里已经将数字 $0$ 的位置固定），最终方案数为 $C_n^1 (n - 2)!$；但这种计算又包含了至少 $2$ 个、至少 $3$ 个……至少 $n$ 个 $i$ 满足条件 $P$ 的方案数，因此还需要继续排除之排除，由此考虑到容斥。
>
> 同理，要计算至少 $k$ 个 $i$ 满足条件的方案数公式为 $C_n^k (n - k - 1)!$，再使用容斥原理即可得到公式：
> $$
> \begin{align*}
>     ans = & (n - 1)! + \sum_{k = 1} ^ n (-1)^k C_n^k (n - k - 1)! \\
>         = & (-1)^0 C_n^0 (n - 0 - 1)! + \sum_{k = 1} ^ n (-1)^k C_n^k (n - k - 1)! \\
>         = & \sum_{k = 0} ^ n (-1)^k C_n^k (n - k - 1)! \\
> \end{align*}
> $$
> 当 $k = n$ 时公式会出现 $(-1)!$，考虑其实际含义，即 $n$ 个数字都满足条件 $P$，此时只可能是 $0, 1, 2, \cdots, n - 1$ 一种排列，所以定义 $(-1)! = 1$ 可满足题意，可单独提出该项，得到：
> $$
> ans = \sum_{k = 0} ^ {n - 1} (-1)^k C_n^k (n - k - 1)! + (-1)^n
> $$

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 100000 + 100;
const LL MOD = 998244353;
int T, n;
LL ans, f;
LL inv[maxn], pro[maxn], invpro[maxn];

void init() {
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

LL C(int n, int m) {
    if(n < m) {
        return 0;
    }
    return pro[n] * invpro[m] % MOD * invpro[n - m] % MOD;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    init();
    cin >> T;
    while (T--) {
        cin >> n;
        ans = (n % 2 == 0 ? 1 : -1);
        f = 1;
        for (int i = 0; i < n; ++i) {
            ans += f * C(n, i) * pro[n - i - 1] % MOD;
            ans = (ans % MOD + MOD) % MOD;
            f *= -1;
        }
        cout << ans << endl;
    }

    return 0;
}
```

## H. [Pow](https://acm.hdu.edu.cn/showproblem.php?pid=6433)

### 题意

> $n$ 个数字分别为 $3^0, 3^1, \cdots, 3^{n - 1}$，要求从中选出一个子集（允许为空），将子集内的所有数字相加，求所有子集内数字相加后的结果的集合共有多少个不同的数字。

### 输入

> 第一行为一个整数 $T$，表示有 $T\ (1 \leq T \leq 20)$ 组数据，第二行为 $n\ (0 \leq n \leq 1000)$，含义如题。

### 输出

> 每组数据输出一行表示答案。

### 题解

> 所有子集内数字的和相当于所有三进制数中，每一位为 $0$ 或者为 $1$ 的情况，总共有 $n$ 位，这些数字的值一定都不相等，所以答案为 $2^n$，用大数乘法加快速幂可 AC。

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 500 + 100;
struct BigInteger {
    int dig;
    LL num[maxn];

    BigInteger() {
        dig = 0;
        memset(num, 0, sizeof(num));
    }
};

ostream& operator<<(ostream& cout, const BigInteger& bigInteger) {
    for (int i = bigInteger.dig; i >= 0; --i) {
        cout << bigInteger.num[i];
    }
    return cout;
}

BigInteger operator*(const BigInteger &a, const BigInteger &b) {
    BigInteger ans;
    for (int i = 0; i <= a.dig; ++i) {
        for (int j = 0; j <= b.dig; ++j) {
            ans.num[i + j] += a.num[i] * b.num[j];
        }
    }
    for (int i = 0; i < maxn - 1; ++i) {
        ans.num[i + 1] += ans.num[i] / 10;
        ans.num[i] %= 10;
        if (ans.num[i] != 0) {
            ans.dig = i;
        }
    }
    return ans;
}

BigInteger fastPow(BigInteger res, int n) {
    BigInteger ans;
    for (ans.num[0] = 1; n != 0; n >>= 1) {
        if ((n & 1) == 1) {
            ans = ans * res;
        }
        res = res * res;
    }
    return ans;
}

int T, n;
BigInteger two;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    two.num[0] = 2;
    cin >> T;
    while (T--) {
        cin >> n;
        cout << fastPow(two, n) << endl;
    }

    return 0;
}
```

## I. [Count](https://acm.hdu.edu.cn/showproblem.php?pid=6434)

### 题意

> 给定 $n$，计算以下公式：
> $$
> \sum_{i = 1}^n \sum_{j = 1}^{i - 1} \left[\gcd(i + j, i - j) = 1 \right]
> $$
> 其中当 $P$ 值为 $true$ 时表达式 $[P]$ 结果为 $1$，否则为 $0$。

### 输入

> 第一行为一个整数 $T\ (1 \leq T \leq 10^5)$，表示有 $T$ 组数据，每组数据一个整数 $n\ (1 \leq n \leq 2 \times 10^7)$，即所给定 $n$ 值。

### 输出

> 每组数据输出一行表示答案。

### 题解

> 由 $\gcd(a, b) = \gcd(a - b, b)$ 可得：
> $$
> \gcd(i + j, i - j) = \gcd(2i, i - j)
> $$
> 令 $k = i - j$，则原式替换为：
> $$
> \sum_{i = 1}^n \sum_{k = 1}^{i - 1} \left[\gcd(2i, k) = 1 \right]
> $$
> 在 $[1, i - 1]$ 范围内 $i$ 互质的所有奇数一定都与 $2i$ 互质，证明如下：
>
> - 如果为偶数则与 $2i$ 的最大公约数为 $2$
> - 如果一个奇数与 $i$ 存在大于 $1$ 的约数，那么这个约数一定不为 $2$，且同样是 $2i$ 的约数，剩余的奇数一定同时与 $i$ 和 $2i$ 都互质
>
> 如果 $i$ 为奇数，则 $[1, i - 1]$ 范围内与 $i$ 互质的奇数个数与偶数个数相等，因为 $\gcd(a, b) = \gcd(a - b, b)$，如果 $i$ 与某个奇数 $x$ 互质，那么 $i$ 一定也与 $i - x$ 互质，而 $i$ 是奇数，所以 $x$ 与 $i - x$ 的奇偶性相反，所以满足条件的数字个数为 $\frac{\phi(i)}{2}$。
>
> 如果 $i$ 为偶数，则 $[1, i - 1]$ 范围内所有与 $i$ 互质的数字一定都是奇数，所以满足条件的数字个数为 $\phi(i)$。
> $$
> ans = \sum_{i = 1}^n f(i)
> $$
> 其中：
> $$
> f(x) = \begin{cases}
>             \phi(x)  & (x \% 2 = 0) \\
>             \frac{\phi(x)}{2} & (x \% 2 = 1)
>         \end{cases}
> $$

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 20000000 + 100;
int T, n, cnt;
int prime[maxn], phi[maxn];
bool vis[maxn];
LL ans[maxn];

void init(int n) {
    phi[1] = 1;
    for(int i = 2; i <= n; ++i) {
        if(!vis[i]) {
            prime[cnt++] = i;
            phi[i] = i - 1;
        }
        for(int j = 0; j < cnt && i <= n / prime[j]; ++j) {
            int k = i * prime[j];
            vis[k] = true;
            if(i % prime[j] == 0) {
                phi[k] = phi[i] * prime[j];
                break;
            } else {
                phi[k] = phi[i] * (prime[j] - 1);
            }
        }
    }
    for (int i = 1; i < maxn; ++i) {
        if (i % 2 == 0) {
            ans[i] = ans[i - 1] + phi[i];
        } else {
            ans[i] = ans[i - 1] + phi[i] / 2;
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    init(maxn - 1);
    cin >> T;
    while (T--) {
        cin >> n;
        cout << ans[n] << endl;
    }

    return 0;
}
```

## J. [CSGO](https://acm.hdu.edu.cn/showproblem.php?pid=6435)

### 题意

> 有 $n$ 个主武器和 $m$ 个副武器，每个武器都有一个主属性值 $S$ 和 $k$ 个副属性值 $X_i$，你需要从中选择 $1$ 个主武器和 $1$ 个副武器，要求选出的武器组合让以下公式值最大（主武器属性用 $m$ 标记，副武器属性用 $s$ 标记）：
> $$
> S_m + S_s + \sum_{i = 1}^k |X_{m,i} - X_{s,i}|
> $$
> 

### 输入

> 第一行为一个整数 $T\ (1 \leq T \leq 100)$，表示有 $T$ 组数据，每组数据第一行为 $3$ 个整数 $n, m, k\ (1 \leq n, m \leq 10^5, 1 \leq k \leq 5)$，接下去 $n$ 行每行 $k + 1$ 个整数，第一个整数为 $S_m\ (0 \leq S_m \leq 10^9)$，后面 $k$ 个整数为 $X_{m, i}\ (|X_{m, i}| \leq 10^9)$，继续往后 $m$ 行格式与数据范围同前 $n$ 行，表示副武器各项属性值，所有输入含义如题。
>
> 数据保证 $\sum (n + m) \leq 3 \times 10^5$。

### 输出

> 每组数据输出一行整数表示答案。

### 题解

> 最远曼哈顿距离扩展版，仅推导 $2$ 维最远曼哈顿距离公式，然后再拓展至 $5$ 维：
> $$
> \begin{align*}
>     & \max_{i \in [1, n], j \in [1, m]}(|X_{i, 1} - X_{j, 1}| + |X_{i, 2} - X_{j, 2}|) \\
>         = & \max_{i \in [1, n], j \in [1, m]}(\max(X_{i, 1} - X_{j, 1}, X_{j, 1} - X_{i, 1}) + \max(X_{i, 2} - X_{j, 2}, X_{j, 2} - X_{i, 2})) \\
>         = & \max_{i \in [1, n], j \in [1, m]}(\max(X_{i, 1} - X_{j, 1} + X_{i, 2} - X_{j, 2}, X_{i, 1} - X_{j, 1} + X_{j, 2} - X_{i, 2}, \\
>             & X_{j, 1} - X_{i, 1} + X_{i, 2} - X_{j, 2}, X_{j, 1} - X_{i, 1} + X_{j, 2} - X_{i, 2})) \\
>         = & \max_{i \in [1, n], j \in [1, m]}(\max((X_{i, 1} + X_{i, 2}) - (X_{j, 1} + X_{j, 2}), (X_{i, 1} - X_{i, 2}) - (X_{j, 1} - X_{j, 2}), \\
>             & (-X_{i, 1} + X_{i, 2}) - (-X_{j, 1} + X_{j, 2}), (-X_{i, 1} - X_{i, 2}) - (-X_{j, 1} - X_{j, 2}))) \\
>         = & \max(\max_{i \in [1, n]}(X_{i, 1} + X_{i, 2}) - \min_{j \in [1, m]}(X_{j, 1} + X_{j, 2}), \max_{i \in [1, n]}(X_{i, 1} - X_{i, 2}) - \min_{j \in [1, m]}(X_{j, 1} - X_{j, 2}), \\
>             & \max_{i \in [1, n]}(-X_{i, 1} + X_{i, 2}) - \min_{j \in [1, m]}(-X_{j, 1} + X_{j, 2}), \max_{i \in [1, n]}(-X_{i, 1} - X_{i, 2}) - \min_{j \in [1, m]}(-X_{j, 1} - X_{j, 2}))
> \end{align*}
> $$
> 发现 $i$ 与 $j$ 可独立求解，且其中 $X_1$ 与 $X_2$ 的加减项组合即 $+X_1, -X_1$ 与 $+X_2, -X_2$ 的组合，用集合 $c$ 表示所有 $2^2$ 种 $\pm 1$ 系数组合的集合（即 $\{1, 1\}, \{1, -1\}, \{-1, 1\}, \{-1, -1\}$），第 $i$ 个组合为 $c_i$，第 $i$ 个组合中第 $j$ 个系数为 $c_{i, j}$，由此可以将上式简写为：
> $$
> \max_{j \in [1, 2^2]}\left(\max_{i \in [1, n]}\left(\sum_{l = 1}^2 c_{j, l} X_{i, l}\right) + \max_{i \in [1, m]}\left(-\sum_{l = 1}^2 c_{j, l} X_{i, l}\right)\right)
> $$
> 以上公式将 $2$ 改为 $k$，再加上 $S_m, S_s$，就是本题答案：
> $$
> \max_{j \in [1, 2^k]}\left(\max_{i \in [1, n]}\left(S_{m, i} + \sum_{l = 1}^k c_{j, l} X_{i, l}\right) + \max_{i \in [1, m]}\left(S_{s, i} - \sum_{l = 1}^k c_{j, l} X_{i, l}\right)\right)
> $$
> 

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 100 + 100;
int T, n, m, k;
LL s, ans;
LL num[maxn], ansm[maxn], anss[maxn];

void inputAndCalMax(int n, int k, int f, LL *ans) {
    fill(ans, ans + maxn, LONG_LONG_MIN);
    for (int i = 0; i < n; ++i) {
        cin >> s;
        for (int j = 0; j < k; ++j) {
            cin >> num[j];
        }
        for (int j = 0; j < (1 << k); ++j) {
            LL tmp = s;
            for (int kk = 0; kk < k; ++kk) {
                if (((j >> kk) & 1) == 1) {
                    tmp += num[kk] * f;
                } else {
                    tmp -= num[kk] * f;
                }
            }
            ans[j] = max(ans[j], tmp);
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    cin >> T;
    while (T--) {
        cin >> n >> m >> k;
        inputAndCalMax(n, k, 1, ansm);
        inputAndCalMax(m, k, -1, anss);
        ans = 0;
        for (int i = 0; i < (1 << k); ++i) {
            ans = max(ans, ansm[i] + anss[i]);
        }
        cout << ans << endl;
    }

    return 0;
}
```

## L. [Videos](https://acm.hdu.edu.cn/showproblem.php?pid=6437)

### 题意

> 有 $A, B$ 两种视频共 $m$ 个，一天共有 $n$ 小时，共有 $k$ 个人观看视频，每个视频只能给一个人看，每个视频的开始时间是 $s$，结束时间是 $t$，同一个人观看的视频播放时间不能重叠，例如：视频 $1$ 的播放时间区间是 $[2, 3]$，视频 $2$ 的播放时间是 $[3, 5]$，则这两个视频可以被同一个人观看，但若视频 $3$ 的播放时间为 $[4, 7]$，则视频 $2$ 和 $3$ 不能被同一个人观看。
>
> 一个人观看一个视频能获得这个视频对应的幸福指数 $w$，如果同一个人连续观看两个同类型的视频，则幸福指数会减少 $W$，例如他按顺序观看的视频类型为：$A, B, B, B, A, B, A, A$ 那么他将减少 $3W$ 幸福指数。
>
> 问所有人能获得的幸福指数总和的最大值是多少？

### 输入

> 第一行为一个整数 $T\ (1 \leq T \leq 20)$，表示有 $T$ 组数据，每组数据第一行为 $4$ 个整数 $n, m, k, W\ (1 \leq n, m, k \leq 200, 1 \leq W \leq 20)$，含义如题，接下去 $m$ 行每行 $4$ 个整数 $s_i, t_i, w_i, op_i\ (1 \leq s_i < t_i \leq n, W \leq w_i \leq 1000, op_i \in \{0, 1\})$，表示第 $i$ 个视频的开始时间、结束时间、幸福指数、类型，$op_i = 0$ 表示第 $i$ 个视频的类型是 $A$，否则为 $B$。

### 输出

> 每行输出一个整数表示答案。

### 题解

> 最小费用最大流构图，将人作为流量，将幸福指数的相反数作为费用，这样求出来的最小费用的相反数就是最大幸福指数。
>
> - 将每个视频拆成两个节点：入点与出点，从入点向出点连一条边，容量为 $1$ 表示只能 $1$ 个人观看此视频，费用为 $-w_i$，表示观看此视频能获得的幸福指数；
> - 从超级源点向次级源点连一条边，容量为 $k$ 费用为 $0$，用于限制观看总人数；
> - 从次级源点向每个视频的入点连一条边，容量为 $1$，费用为 $0$，表示所有人都可以从任意一个视频开始观看；
> - 从每个视频的出点向超级汇点连接一条边，容量为 $1$ 费用为 $0$，表示所有人都可从任意一个视频结束观看；
> - 对所有视频进行两两判定，如果同一个人可以先观看视频 $i$ 再观看视频 $j$（即两个视频的起止时间不重叠），则可以从视频 $i$ 的出点向视频 $j$ 的入点连一条边，容量为 $1$，费用由两个视频的类型决定，如果类型相同则费用为 $-W$，否则为 $0$。
>
> 最后跑一遍最小费用最大流，最小费用的相反数即答案。

### 过题代码

```c++
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'

typedef long long LL;
const int maxn = 400 + 100;
const int maxm = 50000 + 100;
const int INF = 0x3f3f3f3f;

struct Edge {
    int to, Next, cap, flow, cost;

    Edge() {}

    Edge(int t, int n, int ca, int f, int co) {
        to = t;
        Next = n;
        cap = ca;
        flow = f;
        cost = co;
    }
};

struct Node {
    int s, t, w, op;
};

int T, n, m, k, w, N, ans;
int head[maxn], ecnt;
int pre[maxn], dis[maxn];
bool inque[maxn];
Edge edge[maxm << 1];
Node node[maxn];

void init(int n) {
    N = n;
    ecnt = 0;
    memset(head, -1, sizeof(int) * N);
}

void addEdge(int u, int v, int cap, int cost) {
    edge[ecnt] = Edge(v, head[u], cap, 0, cost);
    head[u] = ecnt++;
    edge[ecnt] = Edge(u, head[v], 0, 0, -cost);
    head[v] = ecnt++;
}

bool spfa(int s, int t) {
    queue<int> que;
    for(int i = 0; i < N; i++) {
        dis[i] = INF;
        inque[i] = false;
        pre[i] = -1;
    }
    dis[s] = 0;
    inque[s] = true;
    que.push(s);
    while(!que.empty()) {
        int u = que.front();
        que.pop();
        inque[u] = false;
        for(int i = head[u]; i != -1; i = edge[i].Next) {
            int v = edge[i].to;
            if(edge[i].cap > edge[i].flow && dis[v] > dis[u] + edge[i].cost) {
                dis[v] = dis[u] + edge[i].cost;
                pre[v] = i;
                if(!inque[v]) {
                    inque[v] = true;
                    que.push(v);
                }
            }
        }
    }
    return pre[t] != -1;
}

int minCostMaxflow(int s, int t, int &cost) {
    int flow = 0;
    cost = 0;
    while(spfa(s,t)) {
        int Min = INF;
        for(int i = pre[t]; i != -1; i = pre[edge[i ^ 1].to]) {
            if(Min > edge[i].cap - edge[i].flow) {
                Min = edge[i].cap - edge[i].flow;
            }
        }
        for(int i = pre[t]; i != -1; i = pre[edge[i^1].to]) {
            edge[i].flow += Min;
            edge[i ^ 1].flow -= Min;
            cost += edge[i].cost * Min;
        }
        flow += Min;
    }
    return flow;
}

int sid() {
    return 0;
}

int tid() {
    return 2 * m + 1;
}

int midid() {
    return 2 * m + 2;
}

int inid(int x) {
    return 2 * x - 1;
}

int outid(int x) {
    return 2 * x;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    cin >> T;
    while (T--) {
        cin >> n >> m >> k >> w;
        init(2 * m + 3);
        addEdge(sid(), midid(), k, 0);
        for (int i = 1; i <= m; ++i) {
            cin >> node[i].s >> node[i].t >> node[i].w >> node[i].op;
            addEdge(midid(), inid(i), 1, 0);
            addEdge(inid(i), outid(i), 1, -node[i].w);
            addEdge(outid(i), tid(), 1, 0);
            for (int j = 1; j < i; ++j) {
                int ww = (node[i].op == node[j].op ? -w : 0);
                if (node[j].t <= node[i].s) {
                    addEdge(outid(j), inid(i), 1, -ww);
                } else if (node[j].s >= node[i].t) {
                    addEdge(outid(i), inid(j), 1, -ww);
                }
            }
        }
        minCostMaxflow(sid(), tid(), ans);
        cout << -ans << endl;
    }

    return 0;
}
```

