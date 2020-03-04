# 剑指Offer: 链表中倒数第K个节点

### 题目描述

 输入一个链表，输出该链表中倒数第k个结点。 

**注意：**

- `k >= 0`;
- 如果k大于链表长度，则返回 NULL;

#### 样例

```
输入：链表：1->2->3->4->5 ，k=2

输出：4
```

----------

### 算法1 (链表遍历) O(n)

由于单链表不能索引到前驱节点，所以只能从前往后遍历。

1. 第一次变量链表，得到链表的长度length
2. 获取倒数第k个节点，也就是正数length - k + 1个节点

#### 时空分析

时间复杂度分析: 链表将会遍历两次，时间复杂度为 ***O(n)***

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
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if (NULL == pListHead)
            return NULL;
        unsigned int num = 0;
        auto *node = pListHead;
        while (node)
        {
            num++;
            node = node->next;
        }
        
        node = pListHead;
        if (k > num)
            return NULL;
        
        //执行了num - k 次
        //跳出循环时node指向第num - k +1 个节点
        for (int i = 0; i < num - k; i++)
        {
            node = node -> next;    
        } 
        return node;
    }
};
```

