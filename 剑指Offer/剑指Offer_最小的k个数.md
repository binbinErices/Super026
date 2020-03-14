# 剑指Offer: 最小的k个数

### 题目描述

  输入n个整数，找出其中最小的k个数 

**注意：**

- 数据保证k一定小于等于输入数组的长度;
- 输出数组内元素请按从小到大顺序排序;

#### 样例

```cpp
输入：[1,2,3,4,5,6,7,8] , k=4
输出：[1,2,3,4]
```

----------

### 算法1 排序取k个最小值

std::sort排序后取最小的k个数。

#### 时空分析

时间复杂度分析：sort的时间复杂度***O(nlogn)*** , 遍历K次 ***O(k)***, 总的时间复杂度 ***O(nlogn)***

#### C++ 代码

```cpp
class Solution {
public:
    vector<int> getLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if (k > input.size() || k <=0  || input.empty())
            return res;
        std::sort(input.begin(), input.end());
        
        for(int i = 0; i < k; i++)
        {
            res.push_back(input[i]);
        }
        
        return res;
    }
};
```

----

**自写排序算法： **

```cpp
class Solution {
public:
    vector<int> getLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if (k > input.size() || k <=0  || input.empty())
            return res;
        //std::sort(input.begin(), input.end());
        quick_sort(input, 0, input.size()-1);
        
        for(int i = 0; i < k; i++)
        {
            res.push_back(input[i]);
        }
        
        return res;
    }
    
    void quick_sort(vector<int>& a, int l, int r)
    {
        if (l >= r)
            return;
        int x = a[l+r>>1], i = l - 1, j = r + 1;
        while(i < j)
        {
            do i++; while(a[i] < x);
            do j--; while(a[j] > x);
            if (i < j)
                swap(a[i], a[j]);
        }
        quick_sort(a, l, j);
        quick_sort(a, j+1, r);
    }
};
```

### 算法2 快速选择

运用快速排序的思想，每次快速选择会将一个数放置到正确的位置（即满足左边的数都比它小，右边的数都比它大），因此我们可以对原数组做k次快速选择。

#### 时空分析

时间复杂度分析：与Quick sort不同的是，Quick select只考虑所寻找的目标所在的那一部分子数组，而非像Quick sort一样分别再对两边进行分割。正是因为如此，Quick select将平均时间复杂度从 ***O(nlogn)*** 降到了 ***O(n)***
最坏时间复杂度： ***О(n²)***
平均时间复杂度： ***О(n)***
最坏空间复杂度： total ***О(n)***  , auxiliary  ***O(1)***  

#### C++代码

```cpp
class Solution {
public:
    vector<int> getLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        for(int i = 1;i <= k;i++)
            res.push_back(quick_select(input,0,input.size()-1,i));
        return res;
    }

    int quick_select(vector<int>& q,int l,int r,int k)
    {
        if(l >= r) return q[l];
        int i = l-1,j = r+1,x = q[l+r >> 1];
        while(i < j)
        {
            do i++;while(q[i] < x);
            do j--;while(q[j] > x);
            if(i < j) swap(q[i],q[j]);
        }
        if(k == j-l+1) return q[j];
        else if(k < j-l+1) return quick_select(q,l,j,k);
        else return quick_select(q,j+1,r,k-(j-l+1));
    }
};
```



### 算法3 堆排序
维护一个大小为k的大根堆，将数组元素都push进堆，当堆中的数大于k时弹出堆顶元素。注意弹出堆顶的顺序是从大到小的k个数，要进行逆序操作

#### 时空分析

时间复杂度分析：建堆的时间复杂度是 ***O(logk)*** ，要进行n次建堆的操作。总的时间复杂度为 ***O(nlogk)***

#### C++ 代码

```cpp
class Solution {
public:
    vector<int> getLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        priority_queue<int> heap;
        for(auto x : input)
        {
            heap.push(x);
            if(heap.size() > k) heap.pop(); 
        }
        while(heap.size())
        {
            res.push_back(heap.top());
            heap.pop();
        }
        reverse(res.begin(),res.end());
        return res;
    }
};
```

