# 剑指Offer: 和为S的两个数

### 题目描述

 输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的  

#### 样例

```cpp
对应每个测试案例，输出两个数，小的先输出
```

----------

### 算法1 遍历

由于数组是递增排列的，两个数值相隔越大， 乘积越小，所以只需要找出一次满足条件的一个偶对即可。

#### 时空分析

时间复杂度分析：遍历一遍数组 ***O(n)*** ， 每次find一遍 ***O(logn)*** ， 总的时间复杂度 ***O(nlogn)***

#### C++ 代码

```cpp
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        vector<int> res;
        if (array.empty())
            return res;
        int n = array.size();
        for(auto item: array)
        {
            int value = sum - item;
            auto iter = find(array.begin(), array.end(), value);
            if (iter != array.end())
            {
                if (item > value)
                {
                    res.push_back(value);
                    res.push_back(item);
                    break;
                }
                else
                {
                    res.push_back(item);
                    res.push_back(value);
                    break;
                }              
            }
        }
        return res;
    }
};
```



### 算法2 遍历

由于数组是递增排列的，两个数值相隔越大， 乘积越小，所以只需要找出一次满足条件的一个偶对即可。

#### 时空分析

时间复杂度分析：遍历一遍数组 ***O(n)*** 

#### C++代码

```cpp
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> a,int sum) {
        vector<int> res;
        if (a.empty())
            return res;
 
        int n = a.size();
        int i = 0, j = n - 1;
        while(i < j){
            if(a[i] + a[j] == sum){
                res.push_back(a[i]);
                res.push_back(a[j]);
                break;
            }
            while(i < j && a[i] + a[j] > sum) --j;
            while(i < j && a[i] + a[j] < sum) ++i;
        }
        return res;
    }
};
```

