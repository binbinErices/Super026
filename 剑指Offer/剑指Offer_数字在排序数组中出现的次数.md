# 剑指Offer:   数字在排序数组中出现的次数 

### 题目描述

统计一个数字在排序数组中出现的次数。

例如输入排序数组[1, 2, 3, 3, 3, 3, 4, 5]和数字3，由于3在这个数组中出现了4次，因此输出4。

#### 样例

```sh
输入：[1, 2, 3, 3, 3, 3, 4, 5] ,  3

输出：4
```

----------

### 算法1 STL容器


先把数组元素放入multiset中  然后统计k出现的个数

**时间复杂度: ** 建立set花费 ***O(n)*** , 查找出现的次数时间复杂符 ***O(logn)***  

#### C++ 代码

```cpp
class Solution {
public:
    int getNumberOfK(vector<int>& nums , int k) {
        multiset<int> _set;
        for(auto i:nums)
        {
            _set.insert(i);
        }
        
        return _set.count(k);
    }
    
    
};
```

---

### 算法2 二分查找

1. 先判断边界条件， 判断k存在数组中
2. 先找出大于等于k 的最小位置
3. 先找出小于等于k 的最大位置

#### 时空分析

**时间复杂度:**  ***O(logn)***

#### C++ 代码

```cpp
class Solution {
public:
    vector<int> vec;
    int getNumberOfK(vector<int>& nums , int k) {
        if (nums.empty()) return 0;
        if (k < nums[0] || k > nums[nums.size()-1]) return 0;
        vec = nums;
        
        return getMax(k) - getMin(k) + 1;
        
    }
    
    int getMin(int k)
    {
        int l = 0;
        int r = vec.size() - 1;
        int mid = 0;
        
        while(l < r)
        {
            mid = l + r >> 1;
            if (vec[mid] >= k) r = mid;
            else l = mid + 1;
        }
        
        return l;
    }
    
     int getMax(int k)
    {
        int l = 0;
        int r = vec.size() - 1;
        int mid = 0;
        
        while(l < r)
        {
            mid = l + r + 1 >> 1;
            if (vec[mid] <= k) l = mid;
            else r = mid - 1;
        }
        
        return l;
    }
};
```



###  算法3 哈希表
用一个哈希表记下数组中的元素

#### 时空分析

**时间复杂度: ** 时间复杂度 ***O(n)***

#### C++ 代码

```cpp
class Solution {
public:
    int getNumberOfK(vector<int>& nums , int k) {
        unordered_map<int, int> cnt;
        for(auto i: nums) ++cnt[i] ;
        return cnt[k];
    }  
};
```

