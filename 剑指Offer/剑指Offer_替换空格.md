# 剑指Offer: 替换空格

### 题目描述

请实现一个函数，把字符串中的每个空格替换成`"%20"`。

你可以假定输入字符串的长度最大是1000。
注意输出字符串的长度可能大于1000。

#### 样例

```sh
输入："We are happy."
输出："We%20are%20happy."
```

----------

### 算法1

遍历给定的字符串：

​		如果遇到空格，则在string类型的答案中添加 "%20"
​		如果遇到其他字符，则直接将它添加在答案中

#### 时空分析

时间复杂度分析: 原字符串只会被遍历常数次，所以总时间复杂度是 ***O(n)***

#### C++ 代码

```cpp
class Solution {
public:
    string replaceSpaces(string &str) {
        string res;
        for (auto ch : str)
        {
            if (ch == ' ')
                res += "%20";
            else
                res +=  ch;
        }
        
        return res;
    }
};
```

---

### 算法2

采用STL的内置函数

#### 时空分析

时间复杂度: replace函数的时间复杂度，由于每次执行该函数，会将一个字符变成三个字符，所以会涉及到数组的整体拷贝，那么每执行一次replace最坏情况下需要 ***O(n)*** 的时间，所以总时间复杂度最坏是 ***O(n2)***

#### C++代码

```cpp
class Solution {
public:
    string replaceSpaces(string &str) {
        for (string::size_type pos = 0; pos != string::npos; pos += 3)
        {
            pos = str.find(" ", pos);
            if (pos != string::npos)
                str.replace(pos, 1, "%20");
            else
                break;
       }
       return str;
    }
};
```



### 算法3  (双指针扫描) O(n)

在部分编程语言中，我们可以动态地将原数组长度扩大，此时我们就可以使用双指针算法，来降低空间的使用：

1. 首先遍历一遍原数组，求出最终答案的长度length；
2.  将原数组resize成length大小；
3. 使用两个指针，指针i指向原字符串的末尾，指针j指向length的位置；
4. 两个指针分别从后往前遍历，如果str[i] == ' '，则指针j的位置上依次填充'0', '2', '%'，这样倒着看就是"%20"；如果str[i] != ' '，则指针j的位置上填充该字符即可。

由于i之前的字符串，在变换之后，长度一定不小于原字符串，所以遍历过程中一定有i <= j，这样可以保证str[j]不会覆盖还未遍历过的str[i]，从而答案是正确的。

#### 时空分析

时间复杂度：原字符串只会被遍历常数次，所以总时间复杂度 ***O(n)***

#### C++ 代码
```cpp
class Solution {
public:
	void replaceSpace(char *str,int length) {
        if (!str || length <= 0)
            return;
        int origin = 0;
        int newlength = 0;
        int space = 0;
        char *pstr = str;
        while (*pstr != '\0')
        {
            if (*pstr == ' ')
                space++;
            origin++;
            pstr++;
        }
        
        newlength = origin + 2*space;
        if (newlength > length)
            return;
        
        while(origin >= 0 && newlength > origin)
        {
            if (str[origin] == ' ')
            {
                str[newlength--] = '0';
                str[newlength--] = '2';
                str[newlength--] = '%';   
                --origin;
            }
            else
            {
                str[newlength--] = str[origin--];
            }
        }    
    }  
};
```

#### WA原因分析

这道题目之前在牛客上运行时WA了好几回，主要是length长度没有确定好。

**错误代码示例**

```cpp
class Solution {
public:
    void replaceSpace(char *str,int length) {
        if (nullptr == str || length < 0)
            return;
        int origin = 0;
        int newlength = 0;
        int space = 0;
        while(str[origin] != '\0')
        {
            if (str[origin] == ' ')
                space++;
            origin++;
        }
         
        newlength = origin + 2*space;
        if (newlength > length)
            return;
        //认为origin长度减1才是字符串的长度
        //但是遍历长度时条件：while(str[origin] != '\0')，这时字符串长度已经没有包含'\0'
        //此时赋值循环的终止条件就出现了错误！！！
        while (origin > 0 && newlength > origin)
        {
            --origin;
            if (str[origin] == ' ')
            {
                str[--newlength] = '0';
                str[--newlength] = '2';
                str[--newlength] = '%';
            }
            else
            {
                str[--newlength] = str[origin];
            }
        }  
    }
};
```

