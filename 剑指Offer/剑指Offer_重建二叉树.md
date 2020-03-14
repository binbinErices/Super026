# 剑指Offer: 重建二叉树

### 题目描述

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。 

**注意：**

- 二叉树中每个节点的值都互不相同；
- 输入的前序遍历和中序遍历一定合法；

#### 样例

```cpp
给定：
前序遍历是：[3, 9, 20, 15, 7]
中序遍历是：[9, 3, 15, 20, 7]

返回：[3, 9, 20, null, null, 15, 7, null, null, null, null]
返回的二叉树如下所示：
    3
   / \
  9  20
    /  \
   15   7
```

----------

### 算法 递归

递归建立整棵二叉树：先递归创建左右子树，然后创建根节点，并让指针指向两棵子树。

- 先利用前序遍历找根节点：前序遍历的第一个数，就是根节点的值
- 在中序遍历中找到根节点的位置 k，则 k 左边是左子树的中序遍历，右边是右子树的中序遍历
- 假设左子树的中序遍历的长度是 L，则在前序遍历中，根节点后面的 L 个数，是左子树的前序遍历，剩下的数是右子树的前序遍历

有了左右子树的前序遍历和中序遍历，我们可以先递归创建出左右子树，然后再创建根节点

#### 时空分析

**时间复杂度: ** 我们在初始化时，用 <font color=red>哈希表(**unordered_map<int,int>**)</font> 记录每个值在中序遍历中的位置，这样我们在递归到每个节点时，在中序遍历中查找根节点位置的操作，只需要 ***O(1)*** 的时间。此时，创建每个节点需要的时间是 ***O(1)***，所以总时间复杂度是 ***O(n)***

**空间复杂度: ** 建立哈希表 ***O(n)***

#### C++ 代码

```cpp
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    unordered_map<int,int> pos;
    vector<int> pre,in;
    TreeNode* reConstructBinaryTree(vector<int> preorder,vector<int> inorder) {
        pre = preorder;
        in  = inorder;
        int n = preorder.size();
        for (int i = 0; i < n; i ++ )
            pos[inorder[i]] = i;
        return dfs(0, n - 1, 0, n - 1);
    }

    TreeNode* dfs(int pl, int pr, int il, int ir)
    {
        if (pl > pr) return nullptr;
        int k = pos[pre[pl]] - il;
        TreeNode* root = new TreeNode(pre[pl]);
        root->left = dfs(pl + 1, pl + k, il, il + k - 1);
        root->right = dfs(pl + k + 1, pr, il + k + 1, ir);
        return root;
    }
};
```

----