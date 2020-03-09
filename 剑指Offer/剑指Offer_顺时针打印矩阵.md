# 剑指Offer: 顺时针打印矩阵

### 题目描述

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

#### 样例

```cpp
输入：
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]

输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

----------

### 算法 模拟

我们顺时针定义四个方向：上右下左。
从左上角开始遍历，先往右走，走到不能走为止，然后更改到下个方向，再走到不能走为止，依次类推，遍历 n^2个格子后停止。

#### 时空分析

时间复杂度分析:  矩阵中每个格子遍历一次，所以总时间复杂度是  ***O(n^2)***

空间复杂度： ***O(n^2)***, 申请额外空间用来标记每个格子的状态

#### C++ 代码

```cpp
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        vector<int> res;
        if (matrix.empty() || matrix[0].empty())
            return res;
        int m = matrix.size();
        int n = matrix[0].size();
        
        vector<vector<bool> > record(m, vector<bool>(n, false));
        int dx[] = {-1, 0, 1, 0};
        int dy[] = {0, 1, 0, -1};
        int x = 0, y = 0, d = 1; //d为1，开始向右走，右下左上
        
        for(int i = 0; i < n*m; i++)
        {
            res.push_back(matrix[x][y]);
            record[x][y] = true;
            
            int a = x + dx[d], b = y + dy[d];
            if (a < 0 || a >= m || b < 0 || b >= n || record[a][b])
            {
                d = (d+1)%4;
                a = x + dx[d];
                b = y + dy[d];
            }
            x = a;
            y = b;
        }
        return res;
    }
};
```

