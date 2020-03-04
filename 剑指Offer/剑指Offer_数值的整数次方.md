# 剑指Offer: 数值的整数次方

### 题目描述

实现函数*double Power(double base, int exponent)*，求*base*的 *exponent*次方。

不得使用库函数，同时不需要考虑大数问题。

**注意：**

- 不会出现底数和指数同为0的情况

#### 样例1

```
输入：10 ，2

输出：100
```

#### 样例2

```
输入：10 ，-2  

输出：0.01
```

----------

### 算法 快速幂算法

#### 时空分析

时间复杂度分析:  ***O(logn)***

空间复杂度分析:  ***O(1)***

#### C++ 代码

```cpp
class Solution {
public:
    double Power(double base, int exponent) {
        
        double res = 1.0;
        if (exponent < 0 && base != 0)
        {
            base     = 1 / base;
            exponent = - exponent;
        }
        
        while (exponent)
        {
            if (exponent & 1)
                res *= base;
            base = base * base;
            exponent >>= 1;
        }
        
        return res;
    }
};
```

