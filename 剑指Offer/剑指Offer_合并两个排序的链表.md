# 剑指Offer:  合并两个排序的链表 

### 题目描述

 输入两个递增排序的链表，合并这两个链表并使新链表中的结点仍然是按照递增排序的。 

#### 样例

```
输入：1->3->5 , 2->4->5

输出：1->2->3->4->5->5
```

### 算法1  二路归并
1. 新建头部的辅助结点dummy，设置cur指针指向dummy
2. 若当前l1指针指向的结点的值val比l2指针指向的结点的值val小，则令cur的next指针指向l1，且l1后移；否则指向l2，且l2后移
3. cur指针位置后移
4. 循环以上步骤直到l1或l2为空
5. 将剩余的l1或l2接到cur指针后边
   

#### 时空分析

时间复杂度分析:  ***O(n)***

空间复杂度分析:  ***O(1)***

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
    ListNode* merge(ListNode* l1, ListNode* l2) {
        ListNode *dummy = new ListNode(0);
        ListNode *cur = dummy;
        while (l1 != NULL && l2 != NULL)
        {
            if (l1->val <= l2->val)
            {
                cur->next = l1;
                l1 = l1->next;
            }
            else
            {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        
        
        cur->next =(l1== NULL ? l2:l1);
        
        ListNode *res = dummy->next;
        delete dummy;
        
        return res;
    }  
};
```

### 算法2  递归
#### 时空分析

时间复杂度分析:  两个链表各遍历一次，所以时间复杂度为***O(n)***

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
    ListNode* merge(ListNode* l1, ListNode* l2) {
        if (l1 == nullptr) return l2;
        if (l2 == nullptr) return l1;
        ListNode* res = dfs(l1, l2);
        return res;
    }
    ListNode* dfs(ListNode* l1, ListNode* l2) {
        if (l1 == nullptr) return l2;
        if (l2 == nullptr) return l1;
        ListNode* temp = nullptr;
        if (l1->val < l2->val) {
            temp = l1;
            temp->next = dfs(l1->next, l2);
        }
        else {
            temp = l2;
            temp->next = dfs(l1, l2->next);
        }
        return temp;
    }
};
```