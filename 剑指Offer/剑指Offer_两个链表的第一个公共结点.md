# 剑指Offer:  两个链表的第一个公共结点

### 题目描述

- 输入两个链表，找出它们的第一个公共结点。

  当不存在公共节点时，返回空节点。

#### 样例

  ```sh
  给出两个链表如下所示：
  A：        a1 → a2
                     ↘
                       c1 → c2 → c3
                     ↗            
  B:     b1 → b2 → b3
  
  输出第一个公共节点c1
  ```

----------

### 算法 常规思路

先计算出两个链表的长度，可以让比较长的先走两个链表长度之差的步数，两个再一起走。


#### 时空分析

**时间复杂度: ** 时间复杂度 ***O(n+m)***

#### C++ 代码

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *FindFirstCommonNode(ListNode *headA, ListNode *headB) {
        if (!headA || !headB)
            return nullptr;
        
        auto headA_ = headA;
        auto headB_ = headB;
        int lenA = 0, lenB = 0;
        while (headA_->next)
        {
            lenA++;
            headA_ = headA_->next;
        }
        
        while (headB_->next)
        {
            lenB++;
            headB_ = headB_->next;
        }   
        
        int delta = lenA - lenB;
        if (delta < 0)
        {
            delta = -delta;
            while(delta--)
            {
                headB = headB->next;
            }
        }
        else
        {
            while(delta--)
            {
                headA = headA->next;
            }
        }
        
        while (headA)
        {
            if (headA->val == headB->val) return headA;
            headA = headA->next;
            headB = headB->next;
        }
        
        return nullptr;
    }
};
```

---

### 算法 利用数学关系

不同部分为 **a** 和 **b** ，公共部分为 **c** ；**a + c + b** = **b + c + a** ;让两个一起走，**a** 走到头就转向 **b** ， **b** 走到头转向**a** ，则在公共部分<font color = red> **相遇**</font>

**这种方法适用于确定存在公共节点，若不存在公共节点就会死循环出不来**

#### 时空分析

**时间复杂度:** 时间复杂符 ***O(n)***

#### C++ 代码

```cpp
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* headA, ListNode* headB) {
        auto p = headA, q = headB;
        while(p != q) {
            if(p) p = p->next;
            else p = headB;
            if (q) q = q->next;
            else q = headA;
        }
        return p;
    }
};
```



###  算法 哈希表
用一个哈希表记下访问过的指针

#### 时空分析

**时间复杂度: ** 时间复杂度 ***O(n+m)***

#### C++ 代码

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *findFirstCommonNode(ListNode *headA, ListNode *headB) {
        map<ListNode*,int> record;
        int cnt = 0;
        ListNode* h = headA; 
        while(h!=NULL){
            record[h] = 1;
            h = h->next;
        }
        h = headB;
        while(h!=NULL){
            if (record[h]) return h;
            h = h->next;
        }
        return NULL;
    }
};
```

