# 剑指Offer:  二叉树中和为某一值的路径

### 题目描述

输入一棵二叉树和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。

从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。。 



```cpp
给出二叉树如下所示，并给出num=22。
      5
     / \
    4   6
   /   / \
  12  13  6
 /  \    / \
9    1  5   1

输出：[[5,4,12,1],[5,6,6,5]]
```

----------

### 算法 递归+回溯

- 遍历一遍树的节点，沿节点向下依次递增，回溯的时候，恢复上一次的状态

#### 时空分析

**时间复杂度: ** 树的节点遍历一遍，时间复杂度 ***O(n)***

#### C++ 代码

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> res;//存放结果数组的数组
    vector<int> path;       //路径数组
    int expect;			    //期待的和
    int csum;               //当前和
    vector<vector<int> > findPath(TreeNode* root,int expectNumber) {
        if (!root)
            return res;
        expect = expectNumber;
        csum = 0;
        dfs(root);

        return res;
    }

    void dfs(TreeNode* root)
    {
        if (!root)
            return;

        csum += root->val;
        path.push_back(root->val);
        if (!root->left && !root->right && csum == expect)
            res.push_back(path);

        dfs(root->left);
        dfs(root->right);

        csum -= root->val;
        path.pop_back();
    }
};
```

----

<font color = red size = 5>使用减法可以省略存储当前和和目前和的成员变量，代码如下:</font>

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<vector<int> > findPath(TreeNode* root,int expectNumber) {

        dfs(root, expectNumber);
        return res;
    }

    void dfs(TreeNode* root, int sum)
    {
        if (!root)
            return;

        sum -= root->val;
        path.push_back(root->val);
        if (!root->left && !root->right && !sum)
            res.push_back(path);

        dfs(root->left, sum);
        dfs(root->right, sum);

        sum += root->val;
        path.pop_back();
    }
};
```

