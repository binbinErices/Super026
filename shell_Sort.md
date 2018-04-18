### 目录 
[toc]
##**希尔排序**
>谢尔排序(**Shellsort**)的名称源于它的发明者 **Donald Shell**，他通过比较相距一定间隔的元素来工作。
>该方法的**基本思想**是：先将整个待排元素序列切割成若干个子序列（由相隔某个“**增量**”的元素组成的）分别进行直接插入排序，然后依次缩减增量再进行排序，待整个序列中的元素基本有序（增量足够小）时，再对全体元素进行一次直接插入排序。

由于直接插入排序在元素基本有序的情况下（接近最好情况），效率是非常高的，因此希尔排序在时间效率上有较大提高。

---
	先看一个例子：用Shell_Sort对{50,26,38,80,70,90,8,30,40,20}进行排序：
![这里写图片描述](http://img.blog.csdn.net/20171022000508723?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

---
![这里写图片描述](http://img.blog.csdn.net/20171022002603246?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

---
![这里写图片描述](http://img.blog.csdn.net/20171022000537488?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
由此便可以对一个序列进行排序。

---
##**coding实现**

```
void shell_sort(int *p, int len)
{
	int gap, i, j;
	for (gap = len / 2; gap > 0; gap /= 2)
	{
		for (i = gap; i < len; i+=gap)
		{
			int tmp = p[i];
			for (j = i; j - gap >= 0; j -= gap)
			{
				if (tmp<p[j - gap])
					p[j] = p[j - gap];
				else
					break;
			}
			p[j] = tmp;
		}
	}
}
```
>上面代码中的 gap 增量叫做shell增量，增量的使用对于算法性能有影响，使用谢尔增量时，谢尔排序的最坏情形运行时间为 O(n^2)，Hibbard 提出一个稍微不同的增量序列1,3,7,...2^k-1..
>使用 Hibbard 增量的谢尔排序的最坏情形运行时间为O(n^1.5)

##**实例测试：**

```
/*
* 问题描述：Shell排序
* C/C++ 语言
*
* @author Erice_s
* binbin_Erices@163.com
* @date 2017/10/21
*
*/
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void shell_sort(int *p, int len)
{
	int gap, i, j;
	for (gap = len / 2; gap > 0; gap /= 2)
	{
		for (i = gap; i < len; i+=gap)
		{
			int tmp = p[i];
			for (j = i; j - gap >= 0; j -= gap)
			{
				if (tmp<p[j - gap])
					p[j] = p[j - gap];
				else
					break;
			}
			p[j] = tmp;
		}
	}
}

void print(int *a, int len)
{
	for (int i = 0; i < len; i++)
	{
		printf("a[%d]=%d\n", i, a[i]);
		if ((i + 1) % 3 == 0 || i == len - 1)
			printf("\n");
	}
}

int main(void)
{
	int Array[] = { 89, 45, 58, 90, 29, 34, 17 };
	int len = sizeof(Array) / sizeof(*Array);

	
	printf("sort before:\n");
	print(Array, len);

	shell_sort(Array, len);


	printf("sort after:\n");
	print(Array, len);

	system("pause");
	return 0;
}
```
---
**测试结果：**
![这里写图片描述](http://img.blog.csdn.net/20171022001151541?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

