# 剑指Offer: 删除链表中重复的节点  

### 题目描述

在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留。

#### 样例1

```
输入：1->2->3->3->4->4->5

输出：1->2->5
```

#### 样例2

```
输入：1->1->1->2->3

输出：2->3
```

----------

### 算法1 哈希表 

1. 先把每个链表中的数值放入哈希表中
2. 创建一个虚拟头结点
3. 遍历链表，当哈希表中的个数大于1时删除节点，串成新链表
4. 返回新链表的头节点

#### 时空分析
 **时间复杂度:**  ***O(n)***

**空间复杂度:**  ***O(n)***    

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
    ListNode* deleteDuplication(ListNode* head) {
        if (!head) return nullptr;
        auto cur = head;
        unordered_map<int,int> map;
        while (cur)
        {
            ++map[cur->val];
            cur = cur -> next;
        }

        cur = head;
        ListNode*dummy = new ListNode(0);
        ListNode* newHead = dummy;
        ListNode *tmp = nullptr;
        while(cur)
        {
            if (map[cur->val] > 1)
            {
                tmp = cur;
                cur = cur->next;
                delete tmp;
            }
            else
            {
                dummy->next = cur;
                dummy = dummy->next;
                cur = cur->next;
            }
        }
        //[0, 1, 1, 1, 6, 6, 6, 6, 7, 7, 7, 8, 8, 10, 11, 11, 13, 14, 15, 16, 17, 19, 21, 23, 24, 26, 27, 28, 29, 29]
        dummy->next = nullptr;//需要将新链表的尾指针指向nullptr, 它可能之前指向一个重复数字结尾的链表
        tmp = newHead;
        newHead = newHead -> next;
        delete tmp;

        return newHead;
    }
};

作者：erices
链接：https://www.acwing.com/solution/acwing/content/10567/
来源：AcWing
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

---

### 算法2 线性扫描

从前往后扫描整个链表，每次扫描元素相同的一段，如果这段中的元素个数多于1个，则将整段元素直接删除。

#### 时空分析

**时间复杂度:** 整个链表只扫描一遍，所以时间复杂度是  ***O(n）***

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
    ListNode* deleteDuplication(ListNode* head) {
        auto dummy = new ListNode(-1);
        dummy->next = head;

        auto p = dummy;
        while (p->next) {
            auto q = p->next;
            while (q && p->next->val == q->val) q = q->next;

            if (p->next->next == q) p = p->next;
            else p->next = q;
        }

        return dummy->next;
    }
};
```
