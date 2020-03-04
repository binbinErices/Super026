# 剑指Offer: 斐波那契数列

### 题目描述

输入一个整数 n ，求斐波那契数列的第 n 项。

假定从0开始，第0项为0。(n<=39)

#### 样例

```c
输入整数 n=5 

返回 5
```

----------

 

 **斐波那契数列**是一个整数序列*F 点数n* = 定义如下递推关系 

F(0) = 0

F(1) = 1

F(n) = F(n-1) + F(n-2)

...

F(40) = 102334155

F(39) = 63245986

输入数据范围n<=39, 为了避免整数溢出问题，我们求 F(n)%p 的值， p=10^9+7. 比F(39)大即可



### 算法1 递归

递归计算的节点个数是 O(2^n)的级别的，存在大量重复计算。

#### 时空分析

时间复杂度分析: 时间复杂度是 ***O(2^n)*** ，一秒内大约能算到第三四十项

#### C++ 代码

```cpp
const int MOD = 1000000007;
int f(int n)
{
    if (n == 1) 
        return 1;
    else if (n == 0)
        return 0;
    return (f(n - 1) + f(n - 2)) % MOD;
}
```

---

### 算法2 记忆化搜索

开一个大数组记录中间结果，如果一个状态被计算过，则直接查表，否则再递归计算。

#### 时空分析

开一个大数组记录中间结果，如果一个状态被计算过，则直接查表，否则再递归计算。
总共有 n 个状态，计算每个状态的复杂度是 ***O(1)*** ，所以时间复杂度是 ***O(n)*** 。
一秒内算 n=10^7 毫无压力，但由于是递归计算，递归层数太多会爆栈，大约只能算到 n=10^5 级别

#### C++代码

```cpp
const int N = 100000, MOD = 1000000007;
int a[N];
int f2(int n)
{
    if (a[n]) return a[n];
    if (n == 1) return 1;
    else if (n == 0) return 0;
    a[n] = f2(n - 1) + f2(n - 2);
    a[n] %= MOD;
    return a[n];
}
```

---

### 算法3 递推

开一个大数组，记录每个数的值。用循环递推计算。
总共计算 n 个状态，所以时间复杂度是 ***O(n)*** 。 
但需要开一个长度是 n 的数组，内存将成为瓶颈，当 n=10^8时，需要的内存是 4∗10^8/1024/1024≈381MB 
分子中乘4是因为C++中 int 类型占4字节

#### 时空分析

总共有 n 个状态，计算每个状态的复杂度是 ***O(1)*** ，所以时间复杂度是 ***O(n)*** 。


#### C++代码

```cpp
const int N = 100000000, MOD = 1000000007;
int f3(int n)
{
    a[0] = 0;
    a[1] = 1;
    for (int i = 2; i <= n; i ++ )
    {
        a[i] = a[i - 1] + a[i - 2];
        a[i] %= MOD;
    }
    return a[n];
}
```

### 算法4 递归+滚动变量
仔细观察我们会发现，递推时我们只需要记录前两项的值即可，没有必要记录所有值，所以我们可以用滚动变量递推。

#### 时空分析

时间复杂度还是 ***O(n)*** ，但空间复杂度变成了 ***O(1)***


#### C++代码

```cpp
const int MOD = 1000000007;
int f4(int n)
{
    int x, y, z;
    x = 0;
    y = 1;
    for (int i = 2; i <= n; i ++ )
    {
        z = (x + y) % MOD;
        x = y;
        y = z;
    }
    return z;
}
```

### 算法5 矩阵运算 + 快速幂

用算法4我们1秒内最多可以算到  n=10^8 级别，那当 n 更大时该怎么办呢？
可以先利用矩阵运算的性质将通项公式变成幂次形式，然后用平方倍增（快速幂）的方法求解第 n 项。

首先我们定义向量
![matrix.png](./matrix.png)

#### 时空分析

时间复杂度分析：快速幂的时间复杂度是 ***O(logn)*** ，所以算法5的时间复杂度也是 ***O(logn)*** 。

#### C++代码

```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <ctime>

using namespace std;

const int MOD = 1000000007;

void mul(int a[][2], int b[][2], int c[][2])
{
    int temp[][2] = {{0, 0}, {0, 0}};
    for (int i = 0; i < 2; i ++ )
        for (int j = 0; j < 2; j ++ )
            for (int k = 0; k < 2; k ++ )
            {
                long long x = temp[i][j] + (long long)a[i][k] * b[k][j];
                temp[i][j] = x % MOD;
            }
    for (int i = 0; i < 2; i ++ )
        for (int j = 0; j < 2; j ++ )
            c[i][j] = temp[i][j];
}


int f_final(long long n)
{
    int x[2] = {1, 0};

    int a[2][2] = {{1, 1}, {1, 0}};

    int res[][2] = {{1, 0}, {0, 1}};
    int t[][2] = {{1, 1}, {1, 0}};
    long long k = n - 1;
    while (k)
    {
        if (k&1) mul(res, t, res);
        mul(t, t, t);
        k >>= 1;
    }

    int c[2] = {0, 0};
    for (int i = 0; i < 2; i ++ )
        for (int j = 0; j < 2; j ++ )
        {
            long long r = c[i] + (long long)x[j] * res[j][i];
            c[i] = r % MOD;
        }

    return c[0];
}


int main()
{
    long long n ;

    cin >> n;
    cout << f_final(n) << endl;

    return 0;
}

//作者：yxc
//链接：https://www.acwing.com/blog/content/25/
//来源：AcWing
//著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



<font color = red>**最后一个算法的时间复杂度是 O(logn) ，n 在 long long 范围内都可以在1s内算出来。**</font>

[Reference]( https://www.acwing.com/blog/content/25/ )