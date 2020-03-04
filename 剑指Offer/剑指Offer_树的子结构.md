# 剑指Offer: 树的子结构 

### 题目描述

输入两棵二叉树A，B，判断B是不是A的子结构。

我们规定空树不是任何树的子结构。 

#### 样例

树A：

```
     8
    / \
   8   7
  / \
 9   2
    / \
   4   7
```

树B：

```
   8
  / \
 9   2
```

返回 **true** ,因为B是A的子结构。

```
输入：1->3->5 , 2->4->5

输出：1->2->3->4->5->5
```

### 算法  递归二叉树

递归分为两部分：

1.首先需要递归A树，遍历A中所有非空节点node

2.判断A树中的以node为根节点的子树是不是包含和树B一样的节点，从根节点开始匹配

第一部分的递归：

递归遍历A树，遇到非空节点则与B树进行节点对比

第二部分的递归，我们从根节点开始遍历两棵子树：

- 如果树B中的节点为空，则表示当前分支是匹配的，返回true；
- 如果树A中的节点为空，但树B中的节点不为空，则说明不匹配，返回false；
- 如果两个节点都不为空，但数值不同，则说明不匹配，返回false；
- 否则说明当前这个节点是匹配的，然后递归判断左子树和右子树是否分别匹配即可

#### 时空分析

时间复杂度分析: 最坏情况下，我们对于树A中的每个节点都要递归判断一遍，每次判断在最坏情况下需要遍历完树B中的所有节点。故时间复杂度是 ***O(nm)*** ，其中 **n**是树A中的节点数， **m** 是树B中的节点数。 

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
    bool hasSubtree(TreeNode* pRoot1, TreeNode* pRoot2) {
        if (!pRoot1 || !pRoot2)
            return false;
        if (IsSame(pRoot1, pRoot2)) return true;
        
        return hasSubtree(pRoot1->left, pRoot2) || hasSubtree(pRoot1->right, pRoot2);
    }
    
    bool IsSame(TreeNode* pRoot1, TreeNode* pRoot2) {
        if (!pRoot2) return true;
        if (!pRoot1 || pRoot1->val != pRoot2->val) return false;
        return IsSame(pRoot1->left, pRoot2->left) && IsSame(pRoot1->right, pRoot2->right);
    }
        
};
```
