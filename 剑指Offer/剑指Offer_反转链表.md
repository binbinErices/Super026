# 剑指Offer: 反转链表

### 题目描述

定义一个函数，输入一个链表的头结点，反转该链表并输出反转后链表的头结点。

#### 样例

```
输入:1->2->3->4->5->NULL

输出:5->4->3->2->1->NULL
```

----------

### 算法1
##### (链表操作，迭代) O(n)

翻转即将所有节点的next指针指向前驱节点。
由于是单链表，我们只有一个next域，在迭代时不能直接找到前驱节点，所以我们需要一个额外的指针保存前驱节点。同时在改变当前节点的next指针前，不要忘记保存它的后继节点。

#### 时空分析

空间复杂度分析：遍历时只有3个额外变量，所以额外的空间复杂度是 ***O(1)***
时间复杂度分析：需要遍历一次链表，时间复杂度是***O(n)***

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
    ListNode* ReverseList(ListNode* head) {
        if (!head || !head->next)
            return head;
        ListNode *pCur = head;
        ListNode *pPre = nullptr;
        ListNode *pNext;
        
        while (pCur)
        {
            pNext = pCur ->next;
            pCur->next = pPre;
            pPre = pCur;
            pCur = pNext;
        }
        
        return pPre; 
    }
};
```

----------

### 算法2
##### (递归)  O(n)

1. Question：需要把一个链表翻转，返回头节点
2. Divide: 每一个节点完成翻转，整个链表就可以完成翻转。采用递归的方式处理，处理到原链表的尾指针就是新链表的头指针。
3. Conquer: 合并小问题的解

#### 时空分析

时间复杂度：链表中的每一个节点都需要递归操作一遍，时间复杂度 ***O(n)***

空间复杂度：总共递归 n 层，系统栈的空间复杂度是 ***O(n)***，所以总共需要额外 ***O(n)*** 的空间

#### 参考文献

[b站动态展示](https://www.bilibili.com/video/av83907207?t=1)


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
    ListNode* ReverseList(ListNode* head) {
        // 1.求解基本问题
        if (nullptr == head || nullptr == head->next)
            return head;
        //2.将大问题转化小问题
        ListNode* res = ReverseList(head->next);
        //3. 将小问题的解转化为大问题的解
        head->next->next = head;
        head ->next = nullptr;
        return res;
    }
};
```

