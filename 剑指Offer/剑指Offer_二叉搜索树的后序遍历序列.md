# 剑指Offer:  二叉搜索树的后序遍历序列 

### 题目描述

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。
如果是则返回true，否则返回false。
假设输入的数组的任意两个数字都互不相同。 

**注意：**

- 空树也是完全二叉树

#### 样例

```cpp
输入：[4, 8, 6, 12, 16, 14, 10]
输出：true
```

----------

### 算法 递归

根据二叉搜索树的特点: 左子树关键字的值严格小于根节点数值，右子树的关键字数值都大于根节点数值

- 先利用后续遍历找根节点：后续遍历的最后一个数，就是根节点的值
- 利用根节点的数值找出左子树的部分，后右子树的部分
- 递归求解左子树和右子树

#### 时空分析

**时间复杂度: ** dfs中有个while循环，最坏情况下会循环 ***O(n)*** 次，一共会执行 ***O(n)*** 次dfs，所以时间复杂度是 ***O(n^2)*** 。如果同时给出中序遍历和后续遍历，那可以用哈希表优化成 ***O(n)***

#### C++ 代码

```cpp
class Solution {
public:
    vector<int> seq;
    bool verifySequenceOfBST(vector<int> sequence) {
        seq = sequence;
        return dfs(0, seq.size()-1);
    }
    
    bool dfs(int l, int r)
    {
        if (l >= r) return true;
        int root = seq[r];
        int k = l;
        while(seq[k] < root) k++;
        for(int i = k; i < r; i++)
        {
            if (seq[i] < root)
                return false;
        }
        return dfs(l, k-1) && dfs(k, r-1);
    } 
};
```

----