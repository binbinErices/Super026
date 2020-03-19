# 剑指Offer:  二叉树的深度

### 题目描述

输入一棵二叉树的根结点，求该树的深度。

从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

#### 样例

```sh
输入：二叉树[8, 12, 2, null, null, 6, 4, null, null, null, null]如下图所示：
    8
   / \
  12  2
     / \
    6   4

输出：3
```

----------

### 算法 递归



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
    int treeDepth(TreeNode* root) {
        return dfs(root);
    }
    
    int dfs(TreeNode* root)
    {
        if (!root)
            return 0;
        int left = dfs(root->left)+1;
        int right = dfs(root->right)+1;
        return left >right ? left:right;
            
    }
};
```

---

### 算法 非递归

利用辅助队列，先将头节点入队列，当队列不空时出队列的节点记为node，
当node左节点不空时入队列，其右节点不空时入队列，如此循环即可。
求深度：构造变量cur记录当前层访问到的节点数，width记录当前层的总个数，每当访问过一层层数deep++；
此种方法同时可以求最大宽度，访问第几层的第几个节点，是一种通用方法 

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
    int treeDepth(TreeNode* root) {
        if (!root) return 0;
        queue<TreeNode*> que;
        que.push(root);int depth=0;
        while (!que.empty()) {
            int size=que.size();
            depth++;
            for (int i=0;i<size;i++) {      //一次处理一层的数据
                TreeNode *node=que.front();
                que.pop();
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return depth;
    }
};
```

