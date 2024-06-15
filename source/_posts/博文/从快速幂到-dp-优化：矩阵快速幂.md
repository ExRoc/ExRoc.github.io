---
title: 从快速幂到 dp 优化：矩阵快速幂
category_bar: true
mathjax: true
date: 2020-08-24 23:42:00
updated: 2024-06-09 01:47:07
index_img: /img/cover/从快速幂到-dp-优化：矩阵快速幂.jpg
author: ExRoc
categories: ["博文"]
tags: ["算法", "ACM"]
description: 由浅入深学习矩阵快速幂
---

## 幂运算

> $n$ 个 $a$ 相加我们当然不会写成一个循环，$n$ 个 $a$ 相乘我们自然要用幂运算。

### 幂运算裸题

#### 题目链接

[PAT L1-012: 计算指数](https://www.patest.cn/contests/gplt/L1-012)

#### 题意

> 输入数字 $n$，求出 $2^n$ 的值，$n\in[1,10]$。

#### 解法

> 用上 `cmath` 头文件中的 `pow` 函数即可，由于本题数据范围极小，所以根本不会出现什么精度问题。

#### 过题代码

```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    int n;
    cin >> n;
    cout << 2 << "^" << n << " = " << pow(2, n) << endl;

    return 0;
}
```

## 快速幂运算

> 这只是相对于比较小的数据而言，但是如果要计算的数据范围较大，大到连 `double` 都存不下时，用 `pow` 函数显然会输出错误的结果，而如果写一个循环，显然不可能在规定时间内得到结果，如果题目没有要求结果对某个数取模，算一算结果的位数，大概就要用到 `Java` 的 `BigInteger` 类，不过以下仅讨论结果对某个数取模时的写法，即使要用到 `BigInteger` 类，也是相同的写法，只是换个语言罢了。
>
> 先在这里提以下两个[取模公式](http://baike.baidu.com/item/%E5%8F%96%E6%A8%A1%E8%BF%90%E7%AE%97?fr=aladdin)：
>
> $$\displaylines{(a\times b)\\% c=(a\\% c\times b\\%c)\\%c\\\\(a+b)\\%c=(a\\%c+b\\%c)\\%c}$$
>
> 证明略。

### 算法思想

> 对于幂运算我们知道，
>
> $$\displaylines{a^{bc}=(a^b)^c\\\\a^{b+c}=a^b\times a^c}$$
> 
>有了上面两个公式，我们就可以将幂运算进行下面的转换：
> 
>$$x^n=\begin{cases}
> (x^{\frac{n-1}{2}})^2\times x\quad(n\\%2=1)\\\\
> (x^{\frac{n}{2}})^2\quad\quad\quad(n\\%2=0)
> \end{cases}$$
> 
>而右边小括号里的 $x^{\frac{n-1}{2}}$ 和 $x^{\frac{n}{2}}$ 又是一个幂运算，于是我们就可以递归转换下去，直到 $n=0$。现在来看一下这样转换之后的计算次数有多少次：
> 
>如果是要计算 $x^n$，就要先计算 $x^{\frac{n}{2}}$ 或 $x^{\frac{n-1}{2}}$，要计算这个值，就要先计算 $x^{\frac{n}{4}}$ 或 $x^{\frac{n-1}{4}}$ 或……以这样的方式递减下去，我们可以看到，将在 $\log_2n$ 的次数内下降至 $x^0$，所以要计算 $x^n$ 的时间复杂度就为 $O(\log n)$。

### 幂运算题改

#### 题意

> 当 $n=18654203254124875$ 时求出 $2^n$ 的结果，由于该结果非常大，所以只要求输出 $\mod(2^n,10^9+7)$ 的值。

#### 题解：快速幂

#### 代码

```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const LL MOD = 1000000007;
LL n;

LL fastPow(LL res, LL n) {
    LL ans;
    for (ans = 1; n != 0; n >>= 1) {
        if (n % 2 == 1) {
            ans = (ans * res) % MOD;
        }
        res = (res * res) % MOD;
//        cout << "ans = " << res << "^" << n / 2 << " * " << ans << endl << endl;
//        cout << "ans = " << ans << "    res = " << res << "    n = " << n << endl << endl;
    }
    return ans;
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    cin >> n;
    cout << "2^" << n << " = " << fastPow(2, n) << endl;

    return 0;
}
```

> 对代码快速幂函数部分有疑问的，可以去掉循环中两个 `cout` 语句的注释，来看看每次循环的值的变化是什么样的。

### 快速幂裸题

#### 题目链接

[POJ 1995: Raising Modulo Numbers](http://poj.org/problem?id=1995)

#### 题意

> 给定 $H$ 组 $A$ 和 $B$ 的值以及 $M$，求出
> 
> $$(A_1^{B_1}+A_2^{B_2}+A_3^{B_3}+...+A_H^{B_H})\mod M$$
> 
> 的值，其中 $M,H\in[1,45000]$，$A$ 和 $B$ 不同时为 $0$。

#### 题解：快速幂

#### 过题代码

```C++
#include <iostream>
#include <cstdio>
using namespace std;

typedef long long LL;
const LL MOD = 1000000007;
LL Z, M, N;
LL ans, a, b;

LL fastPow(LL res, LL n, LL MOD) {
    LL ans;
    for (ans = 1; n != 0; n >>= 1) {
        if (n % 2 == 1) {
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
    ios::sync_with_stdio(false);

    cin >> Z;
    while (Z--) {
        ans = 0;
        cin >> M >> N;
        for (int i = 0; i < N; ++i) {
            cin >> a >> b;
            ans = (ans + fastPow(a, b, M)) % M;
        }
        cout << ans << endl;
    }

    return 0;
}
```

## 简单dp

### 关于动态规划

> dp 是动态规划的简称，动态规划大家第一次听大概是在上课的时候，老师教到递归求斐波那契数列时提到的动态规划记录状态的解法吧。动态规划什么原理什么性的，很多博客已经说得很清楚了，大多是复制粘贴，这里不再赘述。
>
> 这里来一个简单的题目：从楼下到楼上有 $n$ 层台阶，一只青蛙要上楼，它有两种跳法，一种是一步一台阶，一种是一步二台阶，问给定台阶的数量 $n$，这只青蛙有多少种跳法。
>
> 不太熟悉 dp 或者经常做蓝桥杯题目的，可能一看这题就开始写搜索的代码了，确实，很多 dp 也可以说是搜索的优化，因为搜索的状态太多，于是就将重复的搜索状态记录下来，这样就可以减少大量无用的搜索，这大概就是动态规划的作用吧。
>
> 从题中我们可以知道，除了第一层和第二层，每到一层台阶，都有两种到达这层台阶的方法，一种是从第 $n-2$ 层台阶跳两层上来，一种是从第 $n-1$ 层台阶跳一步上来，也就是说，到达第 $n$ 层台阶的方法数量就是到达第 $n-1$ 层的方法数量加上到达第 $n-2$ 层的方法数量，设 $dp(n)$ 为到达第 $n$ 层台阶的跳法，于是有以下递推公式 ：
>
> $$dp(n)=\begin{cases}1\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad(n=1)\\\\2\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad(n=2)\\\\dp(n-1)+dp(n-2)\quad(n>2)\end{cases}$$
>
> 咦，这不就是一个斐波那契吗，然后写个循环就出来了。
>
> 个人觉得斐波那契的动态规划解法只是一种循环写法，是个人都能想到，还不能和动态规划扯上什么关系，可能写完了也不知道动态规划是什么，这个例子应该能比较形象地体现出动态规划在算法的优化上的作用。
>
> 当然，动态规划可不全是斐波那契数列，这里只是讲个简单的例子。

### 斐波那契数列

#### 题目链接

[九度OJ 1387: 斐波那契数列](http://ac.jobdu.com/problem.php?pid=1387)

#### 题意

> 输出斐波那契数列第 $n$ 项，其中 $n\in[1,70]$。

#### 题解

> 没什么好解的，一个循环预处理，注意 `long long` 就行了。

#### 过题代码

```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
int n;
LL fib[100];

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    fib[0] = 0;
    fib[1] = 1;
    for (int i = 2; i <= 70; ++i) {
        fib[i] = fib[i - 1] + fib[i - 2];
    }
    while (cin >> n) {
        cout << fib[n] << endl;
    }

    return 0;
}
```

## 矩阵快速幂

> dp 已经这么快了，还可以优化？看标题 $\uparrow$。
> 
> 看到这里，还记得前面提过的快速幂吗？$n$ 个相同的数相乘可以用快速幂，$n$ 个相同的矩阵相乘，自然也可以用快速幂。可这和 dp 有什么关系呢？主要是因为：部分 dp 是可以推导出某个能用矩阵相乘的形式表示的递推公式，斐波那契数列就是一个很好的例子。

### 算法思想

> 快速幂的背景是大数和取模上面已经说过，不再重复，这里来看如何把斐波那契数列的递推公式表示成矩阵相乘的形式：
> 
> $$fib(n)=fib(n-1)+fib(n-2)\quad$$
> 
> $$\left(\begin{matrix}fib(n)\\\\fib(n-1)\end{matrix}\right)=\left(\begin{matrix}1&1\\\\1&0\end{matrix}\right)\left(\begin{matrix}fib(n-1)\\\\fib(n-2)\end{matrix}\right)$$
$$\left(\begin{matrix}fib(n)\\\\fib(n-1)\end{matrix}\right)=\left(\begin{matrix}1&1\\\\1&0\end{matrix}\right)^{n-1}\left(\begin{matrix}fib(1)\\\\fib(0)\end{matrix}\right)\quad\quad(n>0)$$
> 
> 对于这种形式的递推公式的矩阵构造应该能够很轻松看得出来吧，其实快速幂的运用不仅仅是在这样的递推公式上，对于某些图将其邻接矩阵表示出来，也可以构造出一个矩阵快速幂，其算法时间复杂度为 $O(k^3\log n)$，其中 $k$ 为构造出的矩阵行列值。

### 斐波那契矩阵快速幂裸题

#### 题目链接

[51Nod 1242: 斐波那契数列的第 N 项](https://www.51nod.com/Challenge/Problem.html#problemId=1242)

#### 题意

> 求斐波那契数列第 $n$ 项值，如果太大则对 $10^9+9$ 取模。

#### 题解：矩阵快速幂

#### 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int matrix_size = 2;
const int MOD = 1000000009;

int add(int a, int b) {
    a += b;
    if (a >= MOD) {
        return a - MOD;
    }
    return a;
}

struct Matrix {
    int size;
    int num[matrix_size][matrix_size];

    void operator=(const Matrix &m) {
        for (int i = 0; i < size; ++i) {
            memcpy(num[i], m.num[i], sizeof(int) * size);
        }
    }

    void Init() {
        for (int i = 0; i < size; ++i) {
            memset(num[i], 0, sizeof(int) * size);
            num[i][i] = 1;
        }
    }

    void Set() {
        size = 2;
        num[0][0] = num[0][1] = num[1][0] = 1;
        num[1][1] = 0;
    }

    void operator*=(const Matrix &m) {
        static Matrix ans;
        ans.size = size;
        for (int i = 0; i < size; ++i) {
            for (int j = 0; j < size; ++j) {
                ans.num[i][j] = 0;
                for (int k = 0; k < size; ++k) {
                    ans.num[i][j] = add(ans.num[i][j], (LL)num[i][k] * m.num[k][j] % MOD);
                }
            }
        }
        (*this) = ans;
    }

    void fast_pow(LL n) {
        static Matrix ans;
        ans.size = size;
        for (ans.Init(); n != 0; n >>= 1) {
            if ((n & 1) == 1) {
                ans *= (*this);
            }
            (*this) *= (*this);
        }
        (*this) = ans;
    }
};

LL n;
Matrix matrix;

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    cin >> n;
    matrix.Set();
    matrix.fast_pow(n - 1);
    cout << matrix.num[0][0] << endl;

    return 0;
}
```

> 可以看到，这里的 `fast_pow` 函数与上面快速幂的写法几乎完全相同，只是定义了矩阵的结构体和矩阵乘法而已，当然矩阵乘法的写法肯定是要像能够默下来那样熟练，最好快速幂的写法也能默得下来，毕竟应该算是一种基础的算法，题目才不会这样告诉你：请用矩阵快速幂解决这道动态规划问题。

## 矩阵快速幂应用

> 说了这么多，就来检查一下是否理解了矩阵快速幂吧，最好看完下面的题目之后能够自己先想出怎么运用矩阵快速幂，再来看题解。

### Okabe and El Psy Kongroo

#### 题目链接

[Codeforces 821E: Okabe and El Psy Kongroo](http://codeforces.com/contest/821/problem/E)

#### 题意

> 有一个人要出去散步，但是他只能在安全的区域内散步，将他的散步区域限定在平面直角坐标系中的第一象限，从坐标 $(0,0)$ 开始，往右走到 $(k,0)$ 的位置，问安全的走法有多少种。
> 
> 其中安全的区域有 $n$ 段，每段用 $a_i,b_i,c_i(i\in[1,n])$ 三个数表示：从 $x=a_i$ 到 $x=b_i$ 之间，只能在 $y\in[0,c_i]$ 之间散步。已知他只能从坐标 $(x,y)$ 走到 $(x+1,y-1),(x+1,y),(x+1,y+1)$ 三个坐标上。
> 
> $n\in[1,100],a,b,k\in[1,10^{18}],c\in[0,15]$ 数据保证 $a_1=0,a_n\leq k\leq b_n$，且当 $i\in[2,n]$ 时，$a_i=b_{i-1}$。由于结果可能非常大，将求得的结果对 $10^{9}+7$ 取模。

#### 题解

> 这题是不是与上面的青蛙跳台阶很像？如果熟练的话可以看得出来，这是一个可以用 dp 优化的搜索的题目，从走路的方式很容易看出，如果设走到坐标 $(x,y)$ 的走法为 $dp(x,y)$，则：
> 
> $$dp(x,y)=dp(x-1,y)+dp(x-1,y-1)+dp(x-1,y+1)$$
> 
> 其中任何一项的纵坐标超过 $c$，其值都为 $0$。
> 
> 递推公式有了，不过这是一个二维的 dp，并不像前面那个一维的容易写出矩阵（当然要构造矩阵啦，$k$ 的范围可是 $10^{18}$ 次方，横坐标一个一个推过去肯定超时），这题可以将每一个横坐标对应的所有点看作是一个状态，那么对于每一段（注意这里每一段对应的都是不同的矩阵）的状态转移方程就是：
> 
> $$\displaylines{dp(x,0)=dp(x-1,0)+dp(x-1,1)\\\\dp(x,1)=dp(x-1,0)+dp(x-1,1)+dp(x-1,2)\\\\dp(x,2)=dp(x-1,1)+dp(x-1,2)+dp(x-1,3)\\\\\cdots\cdots\\\\dp(x,c)=dp(x-1,c-1)+dp(x-1,c)}$$
> 
> 这样这个矩阵就容易推导多了：
> 
> $$\left(\begin{matrix}dp(x,0)\\\\dp(x,1)\\\\dp(x,2)\\\\\vdots\\\\dp(x,c)\end{matrix}\right)=\left(\begin{matrix}1&1&0&\cdots&0\\\\1&1&1&\cdots&0\\\\0&1&1&\cdots&0\\\\\vdots&\vdots&\vdots&\ddots&\vdots\\\\0&0&0&\cdots&1\end{matrix}\right)\left(\begin{matrix}dp(x-1,0)\\\\dp(x-1,1)\\\\dp(x-1,2)\\\\\vdots\\\\dp(x-1,c)\end{matrix}\right)$$
> 
> 最后两个细节上的问题，一个是在两段的分界线上，$c$ 值不同应当将两个 $c$ 值中最小的那个 $c$ 值以上的所有状态都设为 $0$。另一个是算法的时间复杂度，显然被分的段数越多，$c$ 的取值范围越大，算法的时间复杂度也越高，整体的时间复杂度大概为 $O(nc^3\log k)$，庆幸 $nc^3$ 才 $337500$，$\log k$ 也只在 $64$ 左右。

#### 过题代码

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
const int maxn = 16;
const int matrix_size = 16;
const int MOD = 1000000007;

int add(int a, int b) {
    a += b;
    if (a >= MOD) {
        return a - MOD;
    }
    return a;
}

struct Matrix {
    int size;
    int num[matrix_size][matrix_size];

    void operator=(const Matrix &m) {
        for (int i = 0; i < size; ++i) {
            memcpy(num[i], m.num[i], sizeof(int) * size);
        }
    }

    void Init() {
        for (int i = 0; i < size; ++i) {
            memset(num[i], 0, sizeof(int) * size);
            num[i][i] = 1;
        }
    }

    void Set(int s) {
        size = s;
        memset(num, 0, sizeof(num));
        for (int i = 0; i < s; ++i) {
            for (int j = i - 1; j <= i + 1; ++j) {
                if(j >= 0 && j < s) {
                    num[i][j] = 1;
                }
            }
        }
    }

    void operator*=(const Matrix &m) {
        static Matrix ans;
        ans.size = size;
        for (int i = 0; i < size; ++i) {
            for (int j = 0; j < size; ++j) {
                ans.num[i][j] = 0;
                for (int k = 0; k < size; ++k) {
                    ans.num[i][j] = add(ans.num[i][j], (LL)num[i][k] * m.num[k][j] % MOD);
                }
            }
        }
        (*this) = ans;
    }

    void fast_pow(LL n) {
        static Matrix ans;
        ans.size = size;
        for (ans.Init(); n != 0; n >>= 1) {
            if ((n & 1) == 1) {
                ans *= (*this);
            }
            (*this) *= (*this);
        }
        (*this) = ans;
    }
};

LL N, K, a, b, c, Ans[2][maxn], now;
Matrix matrix;

void setZero(LL *num, int n) {
    for (int i = n + 1; i < maxn; ++i) {
        num[i] = 0;
    }
}

int main() {
#ifdef ExRoc
    freopen("test.txt", "r", stdin);
#endif // ExRoc
    ios::sync_with_stdio(false);

    Ans[now][0] = 1;
    cin >> N >> K;
    while (N--) {
        cin >> a >> b >> c;
        if (a < K) {
            if (b > K) {
                b = K;
            }
            setZero(Ans[now], c);
            matrix.Set(c + 1);
            matrix.fast_pow(b - a);
            for (int i = 0; i <= c; ++i) {
                Ans[!now][i] = 0;
                for (int j = 0; j <= c; ++j) {
                    Ans[!now][i] = (Ans[!now][i] + matrix.num[i][j] * Ans[now][j] % MOD) % MOD;
                }
            }
            now = !now;
            setZero(Ans[now], c);
        }
    }
    cout << Ans[now][0] << endl;

    return 0;
}
```