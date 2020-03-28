# 剑指Offer: 把数组排成最小的数 

### 题目描述

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

例如输入数组[3, 32, 321]，则打印出这3个数字能排成的最小数字321323。

#### 样例

```
输入：[3, 32, 321]

输出：321323
```

**注意**：输出数字的格式为字符串。

----------

### 算法仿函数

- 自定义排序方式，a+b 要小于 b+a的字符串输出结果，如数字3 和 数字32  **323** < **332**
- 按照自定义的排序方式输出结果

#### 时空分析
 **时间复杂度:**  ***O(n)***

#### C++ 代码

```cpp
class Solution {
public:
    struct mycompare
    {
        bool operator()(const int &a, const int &b)
        {
            if (to_string(a)+to_string(b) < to_string(b)+to_string(a))
                return true;
            return false;
        }
    };
 
    string printMinNumber(vector<int>& nums) {
        std::sort(nums.begin(), nums.end(), mycompare());
        
        string res;
        for(auto i: nums)
        {
            res+=to_string(i);
        }
        return res;
    }
};
```

