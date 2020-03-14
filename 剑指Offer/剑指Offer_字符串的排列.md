# 剑指Offer: 字符串的排列

### 题目描述

 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。 

#### 样例

```cpp
输入一个字符串,长度不超过9(可能有字符重复),字符只包括大小写字母。
输入：aab
输出：
    aab,
	aba,
	baa
```

----------

### 算法 动态规划 + 回溯

由于有重复元素的存在：
1. 先将所有数从小到大排序，这样相同的数会排在一起；
2. 从左到右依次枚举每个数，每次将它放在一个空位上；
3. 对于相同数，我们人为定序，就可以避免重复计算：我们在dfs时记录一个额外的状态，当出现相邻两个元素相同时，第一个元素没有被选中，则当前状态不更新，继续其他节点的状态计算。
4. 不要忘记递归前和回溯时，对状态进行更新   

#### 时空分析

时间复杂度分析：搜索树中最后一层共 ***n!*** 个节点，前面所有层加一块的节点数量相比于最后一层节点数是无穷小量，可以忽略。且最后一层节点记录方案的计算量是 ***O(n)*** ，所以总时间复杂度是 ***O(n \* n!)***

#### C++ 代码

```cpp
class Solution {
public:
    vector<string> res;//输出答案vector
    vector<bool> vis;  //记录状态vector
    string copy, ans;  //copy原字符串的拷贝，ans当前一组答案
    int n;             //原字符串长度
    vector<string> Permutation(string str) {
        if (str.empty())
            return res;
        n = str.length();
        copy = str;
        std::sort(copy.begin(), copy.end());
        ans.assign(n, 0);
        vis.assign(n, false);
        dfs(0);
        
        return res;
    }
    
    void dfs(int cur)
    {
        if (cur == n)
        {
            res.push_back(ans);
            return;
        }
        
        for (int i = 0; i < n; i++)
        {
            if (vis[i]) continue;
            if (i > 0 && copy[i] == copy[i-1] && !vis[i-1])
                continue;
            vis[i] = true;
            ans[cur] = copy[i]; 
            dfs(cur+1);
            vis[i] = false;
        }  
    }
};
```

