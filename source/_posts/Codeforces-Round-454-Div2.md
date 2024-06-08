---
title: 'Codeforces Round #454 (Div.2)'
category_bar: true
mathjax: true
date: 2017-12-24 00:00:00
updated: 2024-06-09 03:50:04
index_img: /img/cover/Codeforces.jpg
author: ExRoc
categories: ["Codeforces"]
tags: ["算法", "ACM", "Codeforces"]
description: 过题数：3，排名：628/5566，题目：A/B/C
---

Contests 链接：[Codeforces Round #454 (Div.2)](http://codeforces.com/contest/907)

过题数：3

排名：628/5566

## A. Masha and Bears

### 题意

> 有三只熊，熊爸熊妈和熊儿子，熊爸能爬得上并且喜欢大车，熊妈能爬得上中车且喜欢中车，熊儿子能爬得上小车且喜欢小车，$Masha$ 能爬得上小车且只喜欢小车。对于一只体积为 $a$ 的角色，如果它能爬得上一辆体积为 $b$ 车，它必须满足条件 $a\geq b$，如果它喜欢一辆车，那么它必须满足条件 $a\leq2b$，现在给出三只熊和  $Masha$ 的体积 $V_1,V_2,V_3,V_m$，问能否找到满足以上条件的三辆车，如果能找得到，则输出这三辆车的体积，否则输出 $-1$。

### 数据范围

> $\displaylines{1\leq V_i\leq 100\\\\ V_1>V_2>V_3}$

#### 题解

> 对 $V_m$ 分几种情况讨论一下就行了，情况比较多，所以 $if\cdots else\cdots$ 语句比较多。要注意 $Masha$ 只喜欢小车的条件。

### 过题代码
```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
int a, b, c, d;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    while(scanf("%d%d%d%d", &a, &b, &c, &d) != EOF) {
        a *= 2;
        b *= 2;
        if(d <= c) {
            if(2 * d >= c && 2 * d < b) {
                printf("%d\n%d\n%d\n", a, b, c);
            } else {
                printf("-1\n");
            }
        } else {
            if(d <= 2 * c) {
                c = d;
                if(d * 2 < b) {
                    printf("%d\n%d\n%d\n", a, b, c);
                } else {
                    printf("-1\n");
                }
            } else {
                printf("-1\n");
            }
        }
    }

    return 0;
}
```

## B. Tic-Tac-Toe

### 题意

> 给定一个 $3\times3$ 的九宫格，对于每个宫格，都是一个 $3\times3$ 的方阵，两个人轮流下落子，如果一个人落在某个宫格的第 $i$ 行第 $j$ 列，那么另一个人就可以在第 $i$ 行第 $j$ 列的宫格中的任意一个位置落子，如果第 $i$ 行第 $j$ 列的宫格已经落满了子，那么另一个人可以在整个宫格中的任意一个地方落子。给定初始局面，和最后一个落子的坐标，输出另一个人可能可以落子的位置。

### 数据范围

> $1\leq x,y\leq9$

#### 题解

> 按照题意模拟就行了。

### 过题代码
```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
int n, m;
char str[3][3][3][10];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    for(int i = 0; i < 9; ++i) {
        for(int j = 0; j < 3; ++j) {
            scanf("%s", str[i / 3][j][i % 3]);
        }
    }
    scanf("%d%d", &n, &m);
    --n;
    --m;
    int row = n % 3;
    int col = m % 3;
    bool flag = false;
    for(int i = 0; i < 3; ++i) {
        for(int j = 0; j < 3; ++j) {
            if(str[row][col][i][j] == '.') {
                flag = true;
            }
        }
    }
    if(flag) {
        for(int i = 0; i < 3; ++i) {
            for(int j = 0; j < 3; ++j) {
                if(str[row][col][i][j] == '.') {
                    str[row][col][i][j] = '!';
                }
            }
        }
    } else {
        for(int i = 0; i < 3; ++i) {
            for(int j = 0; j < 3; ++j) {
                for(int ii = 0; ii < 3; ++ii) {
                    for(int jj = 0; jj < 3; ++jj) {
                        if(str[i][j][ii][jj] == '.') {
                            str[i][j][ii][jj] = '!';
                        }
                    }
                }
            }
        }
    }
    for(int i = 0; i < 9; ++i) {
        for(int j = 0; j < 3; ++j) {
            if(j != 0) {
                printf(" ");
            }
            printf("%s", str[i / 3][j][i % 3]);
        }
        printf("\n");
        if(i % 3 == 2) {
            printf("\n");
        }
    }

    return 0;
}
```

## C. Shockers

### 题意

> 有一个人，他要猜一个字符，如果他说的单词中包含了这个字符，他就会被 $Shock$ 一下，如果他说的单词中没有包含这个字符，他就没事，他还可以猜这个字符，如果他猜错了，他仍然会被 $Shock$ 一下。因为这个人反应比较慢，所以他本来早就可以猜出答案了，结果却要被多 $Shock$ 几下才能猜到最后的答案，问如果按照他的提问顺序，他可以少被 $Shock$ 几下？

### 数据范围

> $1\leq n\leq10^5$

#### 题解

> 用两个集合 $ans$ 和 $Not$ 来维护，先把 $ans$ 初始化，把 $a$ 到 $z$ 的小写字符全都放进去，对于后面每一次被 $Shock$ 的提问，都取当前单词和 $ans$ 里的字符的交集，对于每一次没有被 $Shock$ 的提问，都把当前的单词记录到 $Not$ 集合里，每次都从 $ans$ 集合里删去 $Not$ 集合里的字符。当 $ans$ 集合的大小为 $1$ 时，就可以得到答案，对于后面的提问，就按照题意来模拟就行了。

### 过题代码
```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 100000 + 100;
int n;
char ch[2];
char str[maxn];
set<char> st, Not;
char ans;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    while(scanf("%d", &n) != EOF) {
        ans = '\0';
        int ccnt = 0;
        st.clear();
        Not.clear();
        for(char c = 'a'; c <= 'z'; ++c) {
            st.insert(c);
        }
        for(int i = 0; i < n; ++i) {
            scanf("%s%s", ch, str);
            if(ans != '\0') {
                if(ch[0] == '!') {
                    ++ccnt;
                } else if(ch[0] == '?' && str[0] != ans) {
                    ++ccnt;
                }
            } else {
                if(ch[0] == '!') {
                    set<char> tmp;
                    for(int j = 0; str[j]; ++j) {
                        if(st.find(str[j]) != st.end()) {
                            tmp.insert(str[j]);
                        }
                    }
                    st = tmp;
                } else if(ch[0] == '.') {
                    for(int j = 0; str[j]; ++j) {
                        Not.insert(str[j]);
                    }
                } else if(ch[0] == '?') {
                    for(int j = 0; str[j]; ++j) {
                        Not.insert(str[j]);
                    }
                }
                for(set<char>::iterator it = Not.begin(); it != Not.end(); ++it) {
                    st.erase(*it);
                }
                if(st.size() == 1) {
                    set<char>::iterator it = st.begin();
                    ans = *it;
                }
            }
        }
        printf("%d\n", ccnt);
    }

    return 0;
}
```