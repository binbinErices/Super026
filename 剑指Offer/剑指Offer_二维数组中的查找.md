# 剑指Offer: 二维数组中的查找

### 题目描述

在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。

请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

#### 样例

```sh
输入数组：

[
  [1,2,8,9]，
  [2,4,9,12]，
  [4,7,10,13]，
  [6,8,11,15]
]

如果输入查找数值为7，则返回true，

如果输入查找数值为5，则返回false。
```

----------

假设矩阵一共有 n 行，m列

### 算法1 (暴力搜索)  ***O(nlogm)***

刚开始尝试做时，把二维数组拆成一维数组，在一维数组中利用二分进行查找。

#### 时空分析

时间复杂度分析：时间复杂度是 ***O(nlogm)***

空间复杂度分析：***O(1)***

#### C++ 代码
```cpp
class Solution {
public:
    bool searchArray(vector<vector<int>> array, int target) {
        if (array.size() == 0)
            return false;
        bool flag = false;
        int l,r,mid;
        for(unsigned int i = 0; i < array.size(); i++)
        {
            l = 0;
            r = array.at(i).size() -1;
            while (l < r)
            {
                mid = l + r >> 1;
                if (array.at(i).at(mid) < target)
                    l = mid + 1;
                else
                    r = mid;
            }
            
            if (array.at(i).at(l) == target)
            {
                flag = true;
                break;
            }
        }
        
        return flag;
    }
};
```

----------

### 算法2 (单调性扫描) O(n+m)

矩阵matrix\[m][n] ，x为右上角定点处的数值：

```sh
x左边的数都小于等于x，x下边的数都大于等于x
x左边的数都小于等于x，x下边的数都大于等于x
```


因此我们可以从整个矩阵的右上角开始枚举，假设当前枚举的数是 x：

如果 x 等于target，则说明我们找到了目标值，返回true；
如果 x 小于target，则 xx 左边的数一定都小于target，我们可以直接排除当前一整行的数；
如果 x 大于target，则 xx 下边的数一定都大于target，我们可以直接排序当前一整列的数；
排除一整行就是让枚举的点的横坐标加一，排除一整列就是让纵坐标减一。
当我们排除完整个矩阵后仍没有找到目标值时，就说明目标值不存在，返回false。

#### 时空分析

每一步会排除一行或者一列，矩阵一共有 n 行，m 列，所以最多会进行 n+m 步。所以时间复杂度是 ***O(n+m)*** 。


#### C++ 代码
```cpp
class Solution {
public:
    bool searchArray(vector<vector<int>> array, int target) {
        if (array.size() == 0)
            return false;
        int n = array.size();
        int m = array[0].size();
        for (int i = 0; i < n; i++)
        {
            if (target > array[i][m-1])
            	continue;
            else
            {
            	for (int j = m - 1; j >= 0; j--)
                {
                    if (target == array[i][j])
                    {
                        return true;
                    }
                }
            }   
        }
        
        return false;
    }
};
```

