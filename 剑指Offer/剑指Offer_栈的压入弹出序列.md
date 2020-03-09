# 剑指Offer: 栈的压入弹出序列

### 题目描述

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。

假设压入栈的所有数字均不相等。

例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。

**注意**：若两个序列长度不等则视为并不是一个栈的压入、弹出序列。若两个序列都为空，则视为是一个栈的压入、弹出序列。

#### 样例

```cpp
输入：[1,2,3,4,5]
     [4,5,3,2,1]

输出：true
```

----------

### 算法 栈的模拟

输入序列：1 2 3 4 5

输出序列：4 5 3 2 1

栈的操作：

- 将下一个数压入栈中
- 将当前栈顶元素弹出

判断当前栈顶元素是否和下一个要输出的数是一样的

- 一样， 必然将当前栈顶元素弹出
- 不一样， 将输入序列的下一个元素压入栈中

#### 时空分析

时间复杂度分析:  压栈 ***n*** 次， 弹出 ***n*** 次， 时间复杂度 ***O(n)***

#### C++ 代码

```cpp
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if (pushV.size() != popV.size())
            return false;
        if (pushV.empty() && popV.empty())
            return true;
        stack<int> stk;
        int index = 0;
        for(auto x : pushV)
        {
            stk.push(x);
            while (stk.size() && stk.top() == popV[index])
            {
                stk.pop();
                index++;
            }
        }
        return stk.empty();
    }
};
```

