# 剑指Offer: 跳台阶

### 题目描述

 一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）

#### 样例
输入样例(共一行，包含一个整数n)：
5
输出样例(共一行，包含一个整数n)：
8

数据范围 : 1≤n≤15

----------

### 算法1 递归

基本解:
1. 阶梯为0， 方法1
2. 阶梯为1， 方法数1
3. 阶梯为2， 方法数2， 可以连续跳2次一级阶梯，或者跳1次二级

通解：
        对于n(n>2)级台阶, 要写达到n阶台阶，每次跳1 或者 2
        f(n) = f(n-1)+f(n-2)

#### 时空分析

时间复杂度分析:  ***O(n)***

#### C++ 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

int jumpN(int num)
{
    if (num == 0)
        return 0;
    else if (num == 1)
        return 1;
    else if (num == 2)
        return 2;
    else 
        return jumpN(num -1) + jumpN(num -2);
}

int main()
{
    int n ;
    cin >> n;
    cout << jumpN(n) <<endl;

    return 0;
}
```

### 算法2 递归+滚动变量

仔细观察我们会发现，递推时我们只需要记录前两项的值即可，没有必要记录所有值，所以我们可以用滚动变量递推。

#### 时空分析

时间复杂度分析:  ***O(n)*** 
空间复杂度： ***O(1)***

#### C++ 代码

```cpp
 int jumpN(int number) {
    if (number <= 0)
        return 0;
    if (number == 1)
        return 1;
    else if (number == 2)
        return 2;

    int x = 1, y = 2;
    int z = 0;
    for (int i = 3; i <= number; i++)
    {
        z = x + y;
        x = y;
        y = z;
    }
    return z;
 }

//作者：erices
//链接：https://www.acwing.com/solution/acwing/content/9442/
//来源：AcWing
//著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

