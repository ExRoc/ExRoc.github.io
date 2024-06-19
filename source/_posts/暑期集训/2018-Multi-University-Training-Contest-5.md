---
title: 2018 Multi-University Training Contest 5
category_bar: true
mathjax: true
date: 2018-08-13 21:10:00
updated: 2024-06-19 02:38:32
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["暑期集训"]
tags: ["ACM", "题解", "HDU"]
description: 2018 第五场多校题解
---

链接：[2018 Multi-University Training Contest 5](https://acm.hdu.edu.cn/search.php?field=problem&key=2018+Multi-University+Training+Contest+5&source=1&searchmode=source)

过题数：1

排名：470 / 704

成员：官展鹏，冯彦博，孙昊哲

## B. [Beautiful Now](https://acm.hdu.edu.cn/showproblem.php?pid=6351)

### 题意

> 给定一个没有前导零的整数 $n$，其十进制表示为 $(x_1x_2\cdots x_m)\_{10}$，即 $n=\sum_{i=1}^m 10^{m-i} x_i$，可以对这个整数进行 $k$ 次操作，每次操作选择两个整数 $i,j\ (1\leq i\leq j\leq m,x_j\neq0)$ 并将这两个位置上的数字交换，问最终能够交换得到的最小的数字和最大的数字分别是多少。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq100)$，接下去 $T$ 行每行两个整数 $n,k\ (1\leq n,k\leq10^9)$。

### 输出

> 每组数据输出经过 $k$ 次合法交换后能够得到的最大值和最小值。

### 题解

> 由于每次交换可以选择 $1\leq i\leq j\leq m$，其中 $i$ 可以等于 $j$，因此答案可以从任意小于等于 $k$ 次的合法交换中取最小值得到，题目要求在交换过程中不允许出现前导零，可以证明，如果交换的结果不含有前导零，就一定存在一种方式在不出现前导零的情况下从原数字得到结果数字。最后就是暴力枚举所有交换次数小于等于 $k$ 的全排列，每次 $O(n)$ 生成全排列会超时，可以预处理 $9$ 以内的全排列（如果长度达到 $10$ 就只有一种答案），全排列的交换次数可以用并查集找环计算。如果 $k$ 大于等于 $m-1$ 直接生成不含前导零的最小字典序和最大字典序作为答案。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxn = 20;
int T, k, len;
char n[maxn], Max[maxn], Min[maxn], tmp[maxn];
int fa[maxn], per[maxn], ten[maxn];
vector<int> G[maxn][maxn];

void Init(int len) {
    for (int i = 0; i < len; ++i) {
        fa[i] = i;
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

int Count(int *per, int len) {
    int cnt = len;
    Init(len);
    for (int i = 0; i < len; ++i) {
        if (Find(per[i]) != Find(i)) {
            unit(per[i], i);
            --cnt;
        }
    }
    return len - cnt;
}

void Init() {
    ten[0] = 1;
    for (int i = 1; i <= 9; ++i) {
        ten[i] = ten[i - 1] * 10;
    }
    for (int len = 0; len <= 9; ++len) {
        for (int i = 0; i < len; ++i) {
            per[i] = i;
        }
        do {
            int cnt = Count(per, len);
            int num = 0;
            for (int i = 0; i < len; ++i) {
                num += per[i] * ten[i];
            }
            G[len][cnt].push_back(num);
        } while (next_permutation(per, per + len));
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    Init();
    scanf("%d", &T);
    while (T--) {
        scanf("%s%d", n, &k);
        len = strlen(n);
        if (len == 10) {
            printf("%s %s\n", n, n);
            continue;
        }
        strcpy(Min, n);
        strcpy(Max, n);
        if (k >= len - 1) {
            sort(Min, Min + len);
            sort(Max, Max + len);
            int Index = 0;
            for (int i = 0; i < len; ++i) {
                if (Min[i] != '0') {
                    Index = i;
                    break;
                }
            }
            swap(Min[0], Min[Index]);
            for (int i = 0; i < len / 2; ++i) {
                swap(Max[i], Max[len - i - 1]);
            }
            printf("%s %s\n", Min, Max);
            continue;
        }
        for (int i = 1; i <= k; ++i) {
            int llen = G[len][i].size();
            for (int j = 0; j < llen; ++j) {
                int num = G[len][i][j];
                if (n[num % 10] == '0') {
                    continue;
                }
                for (int k = 0; k < len; ++k) {
                    tmp[k] = n[num / ten[k] % 10];
                }
                tmp[len] = '\0';
                if (strcmp(Min, tmp) > 0) {
                    strcpy(Min, tmp);
                } else if(strcmp(Max, tmp) < 0) {
                    strcpy(Max, tmp);
                }
            }
        }
        printf("%s %s\n", Min, Max);
    }

    return 0;
}
```

## E. [Everything Has Changed](https://acm.hdu.edu.cn/showproblem.php?pid=6354)

### 题意

> 在平面直角坐标系内有一个圆心在原点，半径为 $R$ 的初始圆，以及 $m$ 个圆心在 $(x_i,y_i)$，半径为 $r_i$ 的圆，这 $m$ 个圆两两互不相交且不会包含整个初始圆，这 $m$ 个小圆将对初始圆进行切割，问最终初始圆的周长。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq1000)$，接下去有 $T$ 组数据，每组数据第一行为两个整数 $m,R\ (1\leq m\leq100)$，接下去 $m$ 行每行三个整数 $x_i,y_i,r_i\ (-1000\leq x_i,y_i\leq1000,1\leq R,r_i\leq1000)$。

### 输出

> 输出最终圆的周长，误差在 $10^{-6}$ 以内都认为答案正确。

### 题解

> 对于两圆相离或者外切的情况，对周长的贡献都为 $0$，对于两圆内含的情况，贡献也为零，因此只要考虑两圆相交与内切的情况，先考虑第一种相交（两圆圆心在交点连线的两侧）：
>
> ![外交](/img/2018-Multi-University-Training-Contest/Everything-Has-Changed-外交.jpg)
>
> 设 $AG=h,O_1O_2=d,O_1G=a,O_2G=b$，则可以联立方程：
>
> $$\left\lbrace
> \begin{array}{c}
> a^2+h^2=r_1^2\\\\
> b^2+h^2=r_2^2\\\\
> a+b=d
> \end{array}\right.$$
>
> 解方程得到 $b=\frac{r_2^2-r_1^2+d^2}{2d}$，而对于两圆圆心在交点连线同侧的情况：
>
> ![内交](/img/2018-Multi-University-Training-Contest/Everything-Has-Changed-内交.jpg)
>
> 只需要将上面方程组的第 $3$ 个方程改为 $a-b=d$ 即可，得到 $b=-\frac{r_2^2-r_1^2+d^2}{2d}$，发现和上式差一个负号，要求圆 $O_2$ 的贡献，就是求圆 $O_2$ 在圆 $O_1$ 内部的圆弧长度，因此如果直接对 $\frac{b}{r_2}$ 取 $\arccos$，第二种情况的负号正好使得圆 $O_2$ 的圆心角计算得到大于 $90°$ 的部分。
>
> 最后还需要减掉圆 $O_1$ 在圆 $O_2$ 内部的圆弧长度，计算方式与第一种情况相同。

### 过题代码

```c++
#include <cstdio>
#include <cmath>
using namespace std;

typedef long long LL;
const double eps = 1e-8;
const double PI = acos(-1.0);
const int maxn = 100 + 100;
struct Circle {
    double x, y;
    double r, val;
};

int  T, n;
double R;
Circle c[maxn];

int sign(const double &x) {
    if (fabs(x) < eps) {
        return 0;
    }
    if (x < 0) {
        return -1;
    }
    return 1;
}

void get_val(Circle &c, int Index) {
    double dis = c.x * c.x + c.y * c.y;
    int sgn = sign(dis - (R + c.r) * (R + c.r));
    if (sgn > 0) {
        c.val = 0;
        return ;
    }
    sgn = (R - c.r) * (R - c.r) - dis;
    if (sgn > 0) {
        c.val = 0;
        return ;
    }
    double a = (c.r * c.r - R * R + dis) / (2 * sqrt(dis));
    double theta = acos(a / c.r) * 2;
    c.val = c.r * theta;
    double b = (R * R - c.r * c.r + dis) / (2 * sqrt(dis));
    theta = acos(b / R) * 2;
    c.val -= R * theta;
}

bool Set(Circle &a, Circle &b) {
    double dis = (a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y);
    int sgn = sign(dis - (a.r + b.r) * (a.r + b.r));
    if (sgn > 0) {
        return false;
    }
    sgn = (a.r - b.r) * (a.r - b.r) - dis;
    if (sgn > 0) {
        return false;
    }
    return true;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d%lf", &n, &R);
        for (int i = 1; i <= n; ++i) {
            scanf("%lf%lf%lf", &c[i].x, &c[i].y, &c[i].r);
            get_val(c[i], i);
        }
        double ans = 2 * PI * R;
        for (int i = 1; i <= n; ++i) {
            ans += c[i].val;
        }
        printf("%.10f\n", ans);
    }

    return 0;
}
```

## G. [Glad You Came](https://acm.hdu.edu.cn/showproblem.php?pid=6356)

### 题意

> 有一个长度为 $n$ 的序列，序列中每个数字的初始值都为 $0$，接下来对这个序列进行 $m$ 次操作，每次操作将区间 $[l_i,r_i]$ 之间的所有数字 $a_i$，都更新为 $\max(a_i,v_i)$，输出 $m$ 次操作后 $\bigoplus_{i-1}^ni\times a_i$ 的值。为了避免大数据输入，给出初始随机种子 $X,Y,Z$，每次随机数字由以下伪代码生成：
>
> ![伪代码](/img/2018-Multi-University-Training-Contest/Glad-You-Came-伪代码.png)
>
> 总共生成 $3m$ 个随机数 $f_1,f_2,\cdots,f_{3m}$，第 $i$ 次操作的 $l_i,r_i,v_i$ 由以下规则得到：
>
> $$\begin{align}
> l_i&=\min((f_{3i−2}\\%n)+1,(f_{3i−1}\\%n)+1)\\\\
> r_i&=\max((f_{3i−2}\\%n)+1,(f_{3i−1}\\%n)+1)\\\\
> v_i&=f_{3i}\\%2^{30}
> \end{align}$$

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq100)$，接下去 $T$ 行每行 $5$ 个整数 $n,m,X,Y,Z\ (1\leq n\leq10^5,1\leq m\leq5\times10^6,0\leq X,Y,Z<2^{30})$。

### 输出

> 对于每组数据，输出最终结果。

### 题解

> 反向用 $ST$ 表 $O(1)$ 地标记最大值更新，最后从大到小将最大值标记往下打，就可以得到答案，时间复杂度为 $O(m+n\log n)$。

### 过题代码

```c++
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
const int Log = 20;
const unsigned int one = (unsigned int)1 << 30;
int T, n, m;
unsigned int x, y, z, l, r, v;
unsigned int f1, f2, f3;
LL stmax[maxn][Log], mn[maxn];

void Init() {
    mn[0] = -1;
    for (int i = 1; i <= n; ++i) {
        mn[i] = ((i & (i - 1)) == 0)? mn[i - 1] + 1: mn[i - 1];
        memset(stmax[i], 0, sizeof(stmax[i]));
    }
}

unsigned int Rand(unsigned int &x, unsigned int &y, unsigned int &z) {
    x = x ^ (x << 11);
    x = x ^ (x >> 4);
    x = x ^ (x << 5);
    x = x ^ (x >> 14);
    unsigned int w = x ^ (y ^ z);
    x = y;
    y = z;
    z = w;
    return z;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    cin >> T;
    while (T--) {
        cin >> n >> m >> x >> y >> z;
        Init();
        for (int i = 1; i <= m; ++i) {
            f1 = Rand(x, y, z);
            f2 = Rand(x, y, z);
            f3 = Rand(x, y, z);
            f1 = f1 % (unsigned int)n + 1;
            f2 = f2 % (unsigned int)n + 1;
            l = min(f1, f2);
            r = max(f1, f2);
            v = f3 % one;
            int k = mn[r - l + 1];
            stmax[l][k] = max(stmax[l][k], (LL)v);
            stmax[r - (1 << k) + 1][k] = max(stmax[r - (1 << k) + 1][k], (LL)v);
        }
        for (int j = Log - 1; j > 0; --j) {
            for (int i = 1; i + (1 << (j - 1)) <= n; ++i) {
                stmax[i][j - 1] = max(stmax[i][j - 1], stmax[i][j]);
                stmax[i + (1 << (j - 1))][j - 1] = max(stmax[i + (1 << (j - 1))][j - 1], stmax[i][j]);
            }
        }
        LL ans = 0;
        for (int i = 1; i <= n; ++i) {
            ans ^= (LL)i * stmax[i][0];
        }
        cout << ans << endl;
    }

    return 0;
}
```

## H. [Hills And Valleys](https://acm.hdu.edu.cn/showproblem.php?pid=6357)

### 题意

> 给定一个长度为 $n$ 的序列，可以选择序列上的一个区间 $[l,r]\ (1\leq l\leq r\leq n)$，将这个区间上的所有数字翻转，求进行一次操作后能够得到最长非递减子序列的长度。

### 输入

> 第一行为一个整数 $T\ (1\leq T\leq100)$，接下去有 $T$ 组数据，每组数据第一行为一个整数 $n\ (1\leq n\leq10^5)$，第二行包含一个长度为 $n$ 的数字字符串 $A_1A_2\cdots A_n\ (0\leq A_i\leq9)$。

### 输出

> 输出经过一次交换后能够得到的最长非递减子序列的长度，以及翻转区间的左右端点。

### 题解

> 假设将最长非递减子序列中的每段连续相同数字都缩成一个数字，如：$0123456789$，则问题可以转化为求原序列与这个序列的最长“公共”子序列，这个序列中的每个数字可以匹配零次或多次，这个问题可以用 $dp$ 来解决，状态定义为 $dp[i][j]$，表示第一个序列前 $i$ 位与第二个序列前 $j$ 位的最大匹配长度，时间复杂度为 $O(nm)$，$m$ 为第二个序列的长度。
>
> 如果原序列翻转最长非递减子序列的区间为 $[l,r]$，则第二个序列应构造为 $012\cdots(l-1)lr(r-1)(r-2)\cdots(l+1)lr(r+1)\cdots89$，如翻转区间为 $[3,7]$，就要构造 $0123{\bf{76543}}789$，再与第一个序列进行最长“公共”子序列匹配，$7$ 之前的 $3$ 是因为如果翻转的子序列区间为 $[3,7]$，那么在第一个 $7$ 之前的那部分 $3$ 在翻转后也对最长非递减子序列有贡献，如果直接忽略这个 $3$，那部分贡献就没有算上去，会导致漏算。
>
> 最后输出翻转的区间，只要将翻转区间跟着 $dp$ 一起更新即可，用 $dp_l$ 和 $dp_r$ 表示 $dp$ 取得最大值时翻转的区间，其中 $dp_l$ 在多个 $dp$ 取最大值时应取最靠前的位置，$dp_r$ 只要跟着更新，就可以得到最后一个翻转的位置。

### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
int T, n, ans, ansl, ansr;
char str[maxn], stmp[20];
int dp[maxn][20], dpl[maxn][20], dpr[maxn][20];

int solve(int l, int r, int len) {
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= len; ++j) {
            dp[i][j] = dp[i - 1][j];
            dpl[i][j] = dpl[i - 1][j];
            dpr[i][j] = dpr[i - 1][j];
            if (str[i] == stmp[j]) {
                ++dp[i][j];
                if (j == l && dpl[i][j] == 0) {
                    dpl[i][j] = i;
                }
                if (j == r) {
                    dpr[i][j] = i;
                }
            }
            if (dp[i][j] < dp[i][j - 1]) {
                dp[i][j] = dp[i][j - 1];
                dpl[i][j] = dpl[i][j - 1];
                dpr[i][j] = dpr[i][j - 1];
            }
        }
    }
    return dp[n][len];
}

void Create(int l, int r) {
    int Index = 1;
    for (int i = 0; i <= l; ++i) {
        stmp[Index++] = i + '0';
    }
    for (int i = r; i >= l; --i) {
        stmp[Index++] = i + '0';
    }
    for (int i = r; i < 10; ++i) {
        stmp[Index++] = i + '0';
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    scanf("%d", &T);
    while (T--) {
        scanf("%d%s", &n, str + 1);
        for (int i = 0; i < 10; ++i) {
            stmp[i + 1] = i + '0';
        }
        ans = solve(0, 0, 10);
        ansl = ansr = 1;
        for (int i = 0; i < 10; ++i) {
            for (int j = i + 1; j < 10; ++j) {
                Create(i, j);
                int tmp = solve(i + 2, j + 2, 12);
                if (tmp > ans && dpl[n][12] != 0 && dpr[n][12] != 0) {
                    ans = tmp;
                    ansl = dpl[n][12];
                    ansr = dpr[n][12];
                }
            }
        }
        printf("%d %d %d\n", ans, ansl, ansr);
    }

    return 0;
}
```

