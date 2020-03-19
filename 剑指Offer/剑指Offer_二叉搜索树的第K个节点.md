# 剑指Offer:  二叉搜索树的第K个节点

### 题目描述

给定一棵二叉搜索树，请找出其中的第k小的结点。

#### 样例

```sh
例如， （5，3，7，2，4，6，8）  中，按结点数值大小顺序第三小结点的值为4 
```

----------

### 算法 递归

- 由于题目给定的是二叉搜索树，右子树小于根节点，左子树大于根节点。
- 中序遍历就是从小到大的有序排列

#### 时空分析

**时间复杂度: ** 树的节点中序遍历一遍，时间复杂度 ***O(n)***

**空间复杂度:** 开辟了数组来存放中序遍历结果，空间复杂度 ***O(n)***

#### C++ 代码

```cpp
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};
*/
class Solution {
public:
    //中序遍历的结果就是有序序列，第K个元素就是vec[K-1]存储的节点指针；
    TreeNode* KthNode(TreeNode* pRoot, unsigned int k)
    {
        if(pRoot == NULL || k <= 0) 
            return NULL;
        vector<TreeNode*> vec;
        Inorder(pRoot,vec);
        if(k>vec.size())
            return NULL;
        return vec[k-1];
    }
    //中序遍历，将节点依次压入vector中
    void Inorder(TreeNode* pRoot,vector<TreeNode*>& vec)
    {
        if(pRoot==NULL) return;
        Inorder(pRoot->left,vec);
        vec.push_back(pRoot);
        Inorder(pRoot->right,vec);
    } 
};
```
