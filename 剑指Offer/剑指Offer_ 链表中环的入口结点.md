# 剑指Offer: 链表中环的入口结点

### 题目描述

给定一个链表，若其中包含环，则输出环的入口节点。

若其中不包含环，则输出`null`。

----------

### 算法 快慢指针扫描

1. 快慢指针判断是否存在环
2. 得到环的长度K
3. 链表环解开变成直的，等价于求链表倒数第K个节点。

#### 时空分析
 **时间复杂度:**  ***O(n)***

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
    ListNode *entryNodeOfLoop(ListNode *pHead) {
        auto p1 = pHead;
        auto p2 = pHead;
        
        while (p1 && p2)
        {
            p1 = p1->next;
            p2 = p2->next;
            
            if (p1)
                p1=p1->next;
            else
                return nullptr;
            
            //判断是否有环
            if (p1 == p2)
            {
                //得出环的长度
                int n = 1;
                p2 = p2->next;
                while(p2 != p1)
                {
                    p2 = p2->next;
                    n++;
                }
                
                //倒数第环长度个节点就是入口节点
                p1 = p2 = pHead;
                for (int i = 0; i < n; i++)
                {
                    p1 = p1->next;
                }
                
                while (p1 != p2)
                {
                    p1 = p1->next;
                    p2 = p2->next;
                }
                
                return p1;
            }
        }
        return nullptr;
    }
};
```