# 剑指Offer:  复杂链表的复制

### 题目描述

请实现一个函数可以复制一个复杂链表。

在复杂链表中，每个结点除了有一个指针指向下一个结点外，还有一个额外的指针指向链表中的任意结点或者null。

**注意**：

- 函数结束后原链表要与输入时保持一致。

----------

### 算法 暴力求解

双重for循环的主要目的是查找random节点的位置
分为两步实现：

1. 根据next指针新建链表
2. 双重for循环，每次查找到一个random节点，就重新遍历查找其位置

#### 时空分析

**时间复杂度: ** 时间复杂度 ***O(n^2)***

#### C++ 代码

```cpp
/**
 * Definition for singly-linked list with a random pointer.
 * struct ListNode {
 *     int val;
 *     ListNode *next, *random;
 *     ListNode(int x) : val(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *copyRandomList(ListNode *head) {
        ListNode* newList = new ListNode(-1);
        auto nextNode = newList;

        //第一步，根据next指针新建链表
        for (auto node = head; node; node = node->next) {
            ListNode* newNode = new ListNode(node->val);
            nextNode->next = newNode;
            nextNode = nextNode->next;
        }

        // 第二步，双重for循环，每次查找到一个random节点，就重新遍历查找其位置
        for (auto curNode = newList->next, node = head; curNode && node; ) {
            if (node->random) {
                auto r_curNode = newList->next, r_node = head;
                while (r_node != node->random && r_node) {
                    r_curNode = r_curNode->next;
                    r_node = r_node->next;
                }
                curNode->random = r_curNode;
            }
            curNode = curNode->next;
            node = node->next;
        }

        return newList->next;
    }
};
```

---

### 算法 使用hash表求解

分两步来实现：
1. 建立原链表和目标链表的对应关系，存储到hash表中
2. 遍历原链表，查看random节点，根据hash表中存储的对应关系，建立目标链表的random节点关系

#### 时空分析

**时间复杂度:** 时间复杂符 ***O(n)***

**空间复杂符: ** 需要申请额外空间存放哈希表映射 ***O(n)***

#### C++ 代码

```cpp
/**
 * Definition for singly-linked list with a random pointer.
 * struct ListNode {
 *     int val;
 *     ListNode *next, *random;
 *     ListNode(int x) : val(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *copyRandomList(ListNode *head) {
        unordered_map<ListNode*, ListNode*> hash;
        ListNode* newList = new ListNode(-1);
        auto nextNode = newList;

        // 第一步，建立原链表和目标链表的对应关系，存储到hash表中
        for (auto node = head; node; node = node->next) {
            ListNode* newNode = new ListNode(node->val);
            nextNode->next = newNode;
            hash.insert({node, newNode});
            nextNode = nextNode->next;
        }

        // 第二步，遍历原链表，查看random节点，根据hash表中存储的对应关系，
        // 建立目标链表的random节点关系
        for (auto node = head; node; node = node->next) {
            if (node->random) {
                auto curNode = hash[node];
                curNode->random = hash[node->random];
            }
        }

        return newList->next;
    }
};
```
----

### 算法 并行求解

主要思路是通过复制链表的方式，来解决random节点的查找问题。大体可以分为三步：
1. 链表所有节点中间都插入一个复制节点，作为并行链表
2. 遍历新链表，遇到Random节点后对应改变并行链表中的节点
3. 提取出并行链表

#### 时空分析

**时间复杂度: ** 时间复杂度 ***O(n)***

#### C++ 代码

```cpp
/**
 * Definition for singly-linked list with a random pointer.
 * struct ListNode {
 *     int val;
 *     ListNode *next, *random;
 *     ListNode(int x) : val(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *copyRandomList(ListNode *head) {
        for (auto p = head; p;)
        {
            auto np = new ListNode(p->val);
            auto next = p->next;
            p->next = np;
            np->next = next;
            p = next;
        }

        for (auto p = head; p; p = p->next->next)
        {
            if (p->random)
                p->next->random = p->random->next;
        }

        auto dummy = new ListNode(-1);
        auto cur = dummy;
        for (auto p = head; p; p = p->next)
        {
            cur->next = p->next;
            cur = cur->next;
            p->next = p->next->next;
        }

        return dummy->next;
    }
};
```
