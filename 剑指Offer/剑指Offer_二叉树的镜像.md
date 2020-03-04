# 剑指Offer: 二叉树的镜像

### 题目描述

 操作给定的二叉树，将其变换为源二叉树的镜像。 

#### 样例

```sh
输入树：
      8
     / \
    6  10
   / \ / \
  5  7 9 11

 [8,6,10,5,7,9,11,null,null,null,null,null,null,null,null] 
输出树：
      8
     / \
    10  6
   / \ / \
  11 9 7  5

 [8,10,6,11,9,7,5,null,null,null,null,null,null,null,null]
```



### 算法  递归二叉树

我们发现镜像后的树就是将原树的所有节点的左右儿子互换，所以我们在递归遍历原树的所有节点，将每个节点的左右儿子互换即可。

#### 时空分析

时间复杂度分析:  原树仅被遍历一次，所以时间复杂度是 ***O(n)***

#### C++代码

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
    void mirror(TreeNode* root) {
        if (!root)
            return;
        mirror(root->left);
        mirror(root->right);
        swap(root->left, root->right);
    }
};
```

