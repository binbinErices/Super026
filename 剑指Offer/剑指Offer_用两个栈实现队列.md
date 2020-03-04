# 剑指Offer: 用两个栈实现队列

### 题目描述

 请用栈实现一个队列，支持如下四种操作：

- push(x) – 将元素x插到队尾；
- pop() – 将队首的元素弹出，并返回该元素；
- peek() – 返回队首元素；
- empty() – 返回队列是否为空
- **注意：**
  - 你只能使用栈的标准操作：`push to top`，`peek/pop from top`, `size` 和 `is empty`；
  - 如果你选择的编程语言没有栈的标准库，你可以使用list或者deque等模拟栈的操作；
  - 输入数据保证合法，例如，在队列为空时，不会进行`pop`或者`peek`等操作；

#### 样例

```cpp
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);
queue.peek();  // returns 1
queue.pop();   // returns 1
queue.empty(); // returns false
```

----------

### 算法1 使用栈实现队列

<font color = red size = 5>使用两个栈来做实现队列，一个主栈，用来存储数据；一个辅助栈，用来当缓存</font>

push(x)，我们直接将x插入主栈中即可。
pop()，    此时我们需要将主栈元素全部弹出压入辅助栈中，辅助栈的栈顶元素就是我们需要弹出的元素，将栈顶元素弹出，再把辅助栈元素全部压入主栈。
peek()，  可以用和pop()操作类似的方式，得到最先压入栈的元素。
empty()，判断主栈是否为空

#### 时空分析

时间复杂度分析:  

push()：***O(1)***
pop():   每次需要将主栈元素全部弹出，再压入，所以需要 ***O(n)*** 的时间
peek()：类似于pop()，需要 ***O(n)*** 的时间
empty()：***O(1)***

#### C++ 代码

```cpp
class MyQueue {
public:
    /** Initialize your data structure here. */
    stack<int> stack1;
    stack<int> stack2;
    MyQueue() {
        
    }
    
    void copy(stack<int> &res, stack<int> &des)
    {
        while (!res.empty())
        {
            des.push(res.top());
            res.pop();
        }
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        stack1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        copy(stack1, stack2);
        int ret = stack2.top();
        stack2.pop();
        copy(stack2, stack1);
        return ret;
    }
    
    /** Get the front element. */
    int peek() {
        int top;
        copy(stack1, stack2);
        int ret = stack2.top();
        copy(stack2, stack1);
        return ret;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return stack1.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * bool param_4 = obj.empty();
 */
```

