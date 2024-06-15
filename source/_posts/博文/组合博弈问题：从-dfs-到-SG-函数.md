---
title: 组合博弈问题：从 dfs 到 SG 函数
category_bar: true
mathjax: true
date: 2020-08-24 23:44:00
updated: 2024-06-15 00:38:05
index_img: /img/cover/ACM.jpg
author: ExRoc
categories: ["博文"]
tags: ["ACM", "算法"]
description: 博弈入门
---

## 定义

1. 只有两个玩家轮流进行游戏；
2. 游戏规则对游戏双方是平等的；
3. 游戏能在有限的步骤内达到其中一方胜/败的局面，没有平局；
4. 双方都采取最优策略，面对相同的状态，对于游戏双方的必胜/必败态是确定的。

## 必胜态与必败态

首先，存在一个状态，使得先手为**必胜态**或者**必败态**，再根据以下两点，就可以确定所有状态的**必胜 / 败态**。

1. 如果一个状态的后继状态中，存在一个**必败态**，则该状态为**必胜态**；
2. 如果一个状态的所有后继状态都为**必胜态**，则该状态为**必败态**。

对于当前状态而言，如果它的后继状态中存在一个必败态，先手（先手后手是相对与当前状态而言，当前状态的操作者即为先手，而当前状态的后手，便是后继状态的先手）只需要将当前状态改变成这个必败态，则后手必败，先手必胜；如果它的所有后继状态都是必胜态，那么无论先手做什么操作，后手都必胜，因此先手必败。

## 深度优先搜索

博弈问题最基本最暴力的解法，就是深度优先搜索，其依据就是以上的必胜态与必败态的确定方法，以下给出博弈问题深度优先搜索的伪代码（函数返回值为 $true$ 表示搜索当前状态的结果为必胜态）：

```c++
bool dfs(/*当前状态*/) {
    if (/*当前状态已知为必胜/败态*/) {
        return true; / return false;
    }
    for (/*遍历所有后继状态*/) {
        if (dfs(/*后继状态*/) == false) {
            return true;
        }
    }
    return false;
}
```

其中的递归停止条件，一般题目都会给出，例如：当甲达到某一条件时，则甲 / 乙获胜。

## 记忆化搜索

一般情况下，深搜过程中会搜索到许多重复的状态，而对于相同的状态，其必胜/败态已经确定，不必重复搜索，如果把这些状态都记录下来，就可以大大减少搜索的次数，这里给出博弈问题记忆化搜索的伪代码（假设所有状态初始化为 $-1$，必胜为 $1$，必败为 $0$）：

```c++
int dfs(/*当前状态*/) {
    if (/*当前状态已知为必胜/败态*/) {
        return true; / return false;
    }
    if (mp[/*当前状态*/] != -1) {
        return mp[/*当前状态*/];
    }
    for (/*遍历所有后继状态*/) {
        if (dfs(/*后继状态*/) == 0) {
            mp[/*当前状态*/] = 1;
            return 1;
        }
    }
    mp[/*当前状态*/] = 0;
    return 0;
}
```

其实只是在上面的深度优先搜索代码中加了一些记录状态的语句，这样写或许有些抽象，下面用记忆化搜索来解决一道博弈问题。

### [Codeforces 69D: Dot](http://codeforces.com/problemset/problem/69/D)

#### 题意

> 最初有一个点在坐标 $(x,y)$ 处，现在 $Dasha$ 和 $Anton$ 两人轮流从 $n$ 个向量中取一个向量 $(x_i,y_i)$，将这个点的坐标更新为 $(x+x_i,y+y_i)$，若某一方操作后，使得点 $(x,y)$ 离原点的距离大于 $d$，则当前操作方失败。在游戏过程中，两个人分别有一次机会将 $(x,y)$ 点关于 $y=x$ 对称（横纵坐标互换）。现在给定 $n$ 个向量以及点的初始位置 $(x,y)$，$Anton$ 先手，问哪一方将获胜。

#### 数据范围

> $-200\leq x,y\leq200,1\leq d\leq200,1\leq n\leq20$
>
> $0\leq x_i,y_i\leq200$

#### 题解

> 从 $(x,y)$ 开始深搜，对于每个状态，除了到达的点的坐标，还需要记录一个双方使用“对称”的情况，这里用一个 $flag$ 为 $0,1,2,3$ 分别表示双方都未使用对称、先手使用了对称、后手使用了对称、双方都使用了对称，注意在后继状态中，先手成为后手，后手成为先手，则使用“对称”的情况需要反过来。
>
> 当坐标 $(x,y)$ 满足 $x^2+y^2>d^2$ 时，操作方失败，即当前状态为“先手胜”。这里用 $map$ 来存状态与必胜/败之间的映射，因此所有值初始化为 $0$，将必胜态记为 $1$，必败态记为 $2$。
>
> 接着调用 $dfs(x,y,0)$ 判断是否返回 $1$ 即可。

#### 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 100;
struct Point {
    int x, y;
};

struct Node {
    int x, y;
    int flag;

    // 0 为双方没有使用过对称，1 为先手使用过对称，2 为后手使用过对称，3 为双方都使用过对称
    Node() {}
    Node(int xx, int yy, int f) {
        x = xx;
        y = yy;
        flag = f;
    }
};

bool operator<(const Node &a, const Node &b) {
    if (a.flag == b.flag) {
        if (a.x == b.x) {
            return a.y < b.y;
        }
        return a.x < b.x;
    }
    return a.flag < b.flag;
}

int n, d, x, y;
Point point[maxn];
map<Node, int> mp;  // 0 为未定义，1 为必胜态，2 为必败态

int op_flag(int flag) {
    // 将双方使用过对称的状态交换
    if (flag == 0 || flag == 3) {
        return flag;
    } else {
        return 3 - flag;
    }
}

int dfs(int x, int y, int flag) {
    int &ret = mp[Node(x, y, flag)];
    if (ret != 0) {
        return ret;
    }
    if (x * x + y * y > d * d) {
        return 1;
    }
    // 如果不进行对称
    for (int i = 0; i < n; ++i) {
        if (dfs(x + point[i].x, y + point[i].y, op_flag(flag)) == 2) {
            ret = 1;
            return ret;
        }
    }
    if ((flag & 1) == 0) {
        // 如果可以使用对称
        if (dfs(y, x, op_flag(flag | 1)) == 2) {
            ret = 1;
            return ret;
        }
    }
    ret = 2;
    return ret;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    scanf("%d%d%d%d", &x, &y, &n, &d);
    for (int i = 0; i < n; ++i) {
        scanf("%d%d", &point[i].x, &point[i].y);
    }
    if (dfs(x, y, 0) == 1) {
        printf("Anton\n");
    } else {
        printf("Dasha\n");
    }

    return 0;
}
```

注意程序的第 $56$ 与 $63$ 行，都是对后继状态的遍历，只是转移到后继状态的操作不同，不能写在一个 $for$ 循环当中罢了。

实际上不必记录双方关于“对称”的使用情况，因为如果先手认为“对称”操作对自己是有利的，则后手必然会在接下来的一步中使用“对称”回到原来的状态，因此对于双方而言，“对称”操作是无效的，读者可以将代码中关于 $flag$ 标记的部分去掉，提交一下看结论是否正确。

## $Nim$ 博弈

$Nim$ 博弈是一个经典的博弈游戏，我将从 $Nim$ 博弈引入到 $SG$ 函数。首先来介绍一下该游戏的规则：

1. 有 $n$ 堆石子，第 $i$ 堆有 $a_i$ 个石子；
2. 双方轮流进行取石子操作；
3. 每次操作，一方可以从任意一堆中任取 $x$ $(1\leq x\leq a_i)$ 个石子；
4. 取走最后一个石子的一方获胜。

这个问题将所有堆石子的个数记为一种状态，状态压缩一下用记忆化搜索，或许可以解决，但是当 $n$ 很大时，记忆化搜索也将很快就爆时间复杂度。

这个问题的必胜策略直到 $20$ 世纪初才被哈佛大学的一个叫做 $Charles$ $Leonard$ $Bouton$ 的数学家找到，以下给出其结论：

> 当所有石子个数 $a_i$ 的[异或](https://baike.baidu.com/item/%E5%BC%82%E6%88%96/10993677?fr=aladdin)值为 $0$ 时，为必败态，否则为必胜态。

结论的证明如下：

1. 当游戏结束时，$n$ 堆的石子个数 $a_i$ 都为 $0$，其异或值也为 $0$，是必败态；
2. 若当前状态为必败态： $a_1\bigoplus a_2\bigoplus\cdots\bigoplus a_n=0$，从第 $i$ 堆石子中取走 $k$ 个石子，等价于将第 $i$ 堆石子的数量 $a_i$ 异或一个非零值 $x$，使得 $a_i\bigoplus x=a_i-k$，所有石子的异或值为 $a_1\bigoplus a_2\bigoplus\cdots\bigoplus a_i\bigoplus x\bigoplus\cdots\bigoplus a_n=0\bigoplus x=x\neq0$，即所有后继状态的异或值都不等于 $0$，是必胜态；
3. 若当前状态为必胜态： $a_1\bigoplus a_2\bigoplus\cdots\bigoplus a_n=k\neq0$，则总能找到一个 $k$ 的最高位等于 $1$ 的数字 $a_i$，将其更新为 $a_i'=a_i\bigoplus k<a_i$，使得所有石子的异或值为 $a_1\bigoplus a_2\bigoplus\cdots\bigoplus a_i\bigoplus k\bigoplus\cdots\bigoplus a_n=k\bigoplus k=0$，即后继状态中存在一种异或值为 $0$ 的状态，是必败态。证毕。

如果再见到 $Nim$ 游戏，就可以直接用上述结论来解决，当然要先理解其证明过程。现在的话，$Nim$ 游戏这样的直接结论题不太可能出现，一般是以变体的形式出现。为了更深入地理解其证明过程，这里来一道 $Nim$ 游戏的改编题：

### [HDU 1850: Being a Good Boy in Spring Festival](http://acm.hdu.edu.cn/showproblem.php?pid=1850)

#### 题意

> $M$ 堆扑克牌，第 $i$ 堆有 $N_i$ 张扑克牌，两人玩 $Nim$ 游戏，问如果先手想要获胜，有几种可能的取扑克牌的方式。

#### 数据范围

> $1<M\leq100$
>
> $1\leq N_i\leq1000000$

#### 题解

> 算出所有 $N_i$ 的异或值 $k$，遍历计算所有 $k$ 的最高位为 $1$ 的 $N_i$ 的个数，即答案。

#### 过题代码

```c++
#include <cstdio>
using namespace std;

typedef long long LL;
const int maxn = 200;
int n, k, dig, ans;
int num[maxn];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    while (scanf("%d", &n), n != 0) {
        k = 0;
        for (int i = 0; i < n; ++i) {
            scanf("%d", &num[i]);
            k ^= num[i];
        }
        int tmp = k;
        dig = 0;
        while (tmp != 0) {
            tmp >>= 1;
            ++dig;
        }
        --dig;
        ans = 0;
        for (int i = 0; i < n; ++i) {
            ans += (num[i] >> dig) & 1;
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

本小节最后简单介绍一下 $Anti-Nim$ 游戏，游戏规则除了将 $Nim$ 游戏最后一条改为“取走最后一个石子的人失败”，其他游戏规则都相同，则可以用以下结论判定必胜态：

1. 所有堆的石子数都为 $1$ 且它们的异或值为 $0$；
2. 有些堆的石子数大于 $1$ 且它们的异或值不为 $0$。

该证明的方法也是从必胜态与必败态的转化入手，假设当前状态为必胜态（分石子数都为 $1$ 与有些堆石子数大于 $1$ 两种情况），证明其后继状态中必存在一个必败态，假设当前状态为必败态，证明其所有的后继状态都为必胜态，详细过程留给读者自行证明。

## $SG$ 函数

以上的条件比较宽松，即对于每一堆石子，都可以取 $1\leq x\leq a_i$ 个石子，若题目中给定一些约束条件，我们应该如何解决？

如果将每个状态都映射为一个值，这个值表示该状态为必胜态或者必败态，类比 $Nim$ 游戏，我们将这个状态的函数定义为 $SG$ 函数：

$f(终止状态)=0$

$f(x)=mex(f(y)|y为x的后继状态)$

$mex\{A\}=\min\{k\notin A\wedge k\in N\}$

于是，我们就可以将上面的 $Nim$ 游戏每一堆的石子数 $a_i$ 对应地等于 $SG_i$，因为对于 $a_i$ 的后继状态集合为 $[0,a_i-1]$，对于多个这样的游戏，我们可以将其 $SG_i$ 值异或起来，即整个游戏的 $SG$ 值。

相反地，我们可以将带限制的多个并行游戏，打出单个游戏的 $SG$ 表，就可以等价于 $Nim$ 游戏了。

多数情况下，我们将 $SG=0$ 定义为必败态， $SG\neq0$ 定义为必胜态。

有了 $SG$ 函数，我们做起博弈问题来，也就可以更加得心应手了，实际上，$SG$ 函数对于单个游戏而言，时间复杂度与记忆化搜索相同，但是在特殊情况下，我们可以通过 $SG$ 函数打表找规律或者预处理，来大大降低时间复杂度。而对于多个并行游戏， $SG$ 函数是再好用不过的了，但是一定要注意，使用 $SG$ 函数条件的严格证明。这里给出 $SG$ 打表的代码（来自[板子-博弈论](https://www.zybuluo.com/Dmaxiya/note/985324)）：

```c++
const int maxn = 10000 + 100;
int n;
int Array[maxn], SG[maxn];
bool visit[maxn];
// maxn 为“石子”数，n 为 Array 数组大小，Array 数组需从小到大排序

void get_SG() {
    SG[0] = 0;
    for (int i = 1; i <= 10000; ++i) {
        memset(visit, 0, sizeof(visit));
        for (int j = 0; j < k; ++j) {
            if(i >= Array[j]) {
                visit[SG[i - Array[j]]] = true;
            }
        }
        for (int j = 0; j <= 10000; ++j) {
            if(!visit[j]) {
                SG[i] = j;
                break;
            }
        }
    }
}
```

最后来用一道 $SG$ 打表的裸题运用一下：

### [HDU 1536: S-Nim](http://acm.hdu.edu.cn/showproblem.php?pid=1536)

#### 题意

> 两个人玩多组变体 $Nim$ 游戏，每组 $Nim$ 游戏中，有 $m$ 个状态：给定石子的堆数 $l$ 以及每一堆石子的数量 $h_i$，以及每次可以从一堆当中取的石子的个数 $s_i$，$s_i$ 有 $k$ 个不同的值，对于每组的 $m$ 个状态，判断该状态为必胜态还是必败态。

#### 数据范围

> $0<k\leq100,0<m\leq100,0<l\leq100$
>
> $0<s_i\leq10000,0\leq h_i\leq10000$

#### 题解

> 对于每组给定的可以取的石子的数量，打一个 $0$ 到 $10000$ 的 $SG$ 表，然后对每个状态进行 $SG$ 值异或判断。

#### 过题代码

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long LL;
const int maxk = 200;
const int maxn = 10000 + 100;
int k, m, l, num;
int Array[maxk];
int SG[maxn];
bool visit[maxn];

void get_SG() {
    SG[0] = 0;
    for (int i = 1; i <= 10000; ++i) {
        memset(visit, 0, sizeof(visit));
        for (int j = 0; j < k; ++j) {
            if (i >= Array[j]) {
                visit[SG[i - Array[j]]] = true;
            }
        }
        for (int j = 0; j <= 10000; ++j) {
            if (!visit[j]) {
                SG[i] = j;
                break;
            }
        }
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc

    while (scanf("%d", &k), k != 0) {
        for (int i = 0; i < k; ++i) {
            scanf("%d", &Array[i]);
        }
        sort(Array, Array + k);
        get_SG();
        scanf("%d", &m);
        for (int i = 0; i < m; ++i) {
            int ans = 0;
            scanf("%d", &l);
            for (int i = 0; i < l; ++i) {
                scanf("%d", &num);
                ans ^= SG[num];
            }
            if (ans == 0) {
                printf("L");
            } else {
                printf("W");
            }
        }
        printf("\n");
    }

    return 0;
}
```

## 其他博弈问题及结论

### 巴什博弈

#### 题目

一堆物品有 $n$ 个，两个人轮流从这堆物品中取物，规定每次至少取一个，最多取 $m$ 个，取走最后一个物品者获胜。

#### 结论

当 $n\%(m+1)=0$ 时，先手必败，否则先手必胜。

### 威佐夫博奕

#### 题目

有两堆物品，分别有 $a$ 个与 $b$ 个，两个人轮流取物品，每次可以从任意一堆中取走任意个物品，或者从两堆物品中取走任意多个相同数量的物品，每次最少取一个物品，取走最后一个物品者获胜。

#### 结论

若 $b>a$， $a=\lfloor(b-a)\times\frac{\sqrt5+1}{2}\rfloor$，则先手必败，否则先手必胜。