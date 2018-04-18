# 分治法之归并排序
##目录
[TOC]

##摘要
本小节中主要讨论**`分治法`**在**`归并排序`**的应用：

- **分治法的步骤**
- **主方法**
- **归并排序的算法**
- **归并排序的实例**

-------------------

## 分治法的步骤

  
 -     `Divide the problem into one or more subproblem `
 - 分解：将原问题划分为一些子问题，子问题的形式和原问题的相同，但是规模减小 
 -    `Conquer the subproblems by solving them recursively.`
 - 解决：利用递归求解出子问题的解，如果子问题的规模足够小，则停止递归，直接求解
 -    `Combine the solutions to the subproblems into the solution for the original problem`
 - 合并：将子问题的解合并成原问题的解
 - 


## 主方法（The master method）

>**Master theorem**  是判断递归式，求解估算算法复杂度比较常用的方法，它的主要内容如下：
	>>  Master theorem (reprise)
	>T(n) = aT(n/b) + f(n)
CASE 1: f(n) = O(n log b a – ε ), constant ε > 0
⇒ T(n) = Θ(n log b a ) .
CASE 2: f(n) = Θ(n log b a lg k n), constant k ≥ 0
⇒ T(n) = Θ(n log b a lg k+1 n) .
CASE 3: f(n) = Ω(n log b a + ε  ), constant ε > 0,
and regularity condition
⇒ T(n) = Θ(f(n)) .

###主方法的实例
***eg1*** :  T(n)=4T(n/2)+n
>由于f(n)=n=O(n^2),属于CASE1，==>T(n)=Θ(n^2)

------
***eg2*** :  T(n)=2T(n/2)+n
>由于f(n)=n=Θ(n^2),属于CASE2，==>T(n)=Θ(nlgn)

---
***eg3*** :  T(n)=4T(n/2)+n^3
>由于f(n)=n^3=Ω(n^2),属于CASE3，==>T(n)=Θ(n^3)



## 归并排序

**merge算法伪代码1**　：

![算法导论中的伪代码：使用哨兵](http://img.blog.csdn.net/20171014185025102?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**Merge算法伪代码2**　：

``` c/c++
Merge(B[0...p-1],C[0...q-1],A[0,p+q-1])
//将两个有序数组合并成一个有序数组
//输入：两个有序数组B[0...p-1],C[0...q-1]
//输出：A[0,p+q-1]中已经有序存放了B和C的元素
 i<-0;j<-0;k<-0;
 while i<p && j<q do
    if B[i]<=C[j]
       A[k] <- B[i];i <- i+1
    else
       A[k] <- C[j];j <- j+1
    k <- k+1
 if i=p
    copyC[j...q-1] to A[k..p+q-1]
 else
    copyB[i...p-1] to A[k..p+q-1]
```
---
**MergeSort算法伪代码1**　：

![这里写图片描述](http://img.blog.csdn.net/20171014190821473?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**MergeSort算法伪代码2**　：

``` c/c++
MergeSort(A[0,n-1])
//递归调用mergesort对数组A进行排序
//输入：一个可排序数组A[0...n-1]
//输出：非降序排列的数组A[0,n-1]
if n>1
  copy a[0...n/2] to B[0...n/2]
  copy a[n/2+1...n-1] to C[0...n/2]
MergeSort(B[0...n/2])
MergeSort(C[0...n/2])
Merge(B,C,A)
```
---
**MergeSort 的例子**　：

![这里写图片描述](http://img.blog.csdn.net/20171014191724775?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


###归并排序的实例1
***eg  :***   应用**归并算法**将序列EXAMPLE按照字母表顺序排序

``` c/c++
/*
* 归并排序：C 语言
*
* @author Erice_s
* @date 2017/10/13
*
* 参数说明：
*    a -- 一个可排序的数组
*    first -- 第一个有序数组下标最小的数
*    last -- 第二个有序数组下标最大的数
*    mid -- 第一个有序数组结束，第二个有序数组开始的下标
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void merge(char *a, int first, int mid, int last)
{ 
	int i = first;
	int j = mid + 1;
	int m = 0;

	char *ptmp = (char*)malloc((last - first + 1)*sizeof(char));
	while (i <= mid&&j <= last)
	{
		if ((int)a[i] < (int)a[j])
			ptmp[m++] = a[i++];
		else
			ptmp[m++] = a[j++];
	}
	while (i <= mid)
		ptmp[m++] = a[i++];
	while (j<=last)
		ptmp[m++] = a[j++];
	
	for (i = 0; i < last - first + 1; i++)
		a[first+i] = ptmp[i];
	
	free(ptmp);
}

void mergeSort(char *a, int first, int last)
{
	if (a==NULL||first >= last)
		return;
	int mid = (first & last) +((first^last) >> 1);
	//int mid = (first + last)/2;//may cause overflow
	mergeSort(a, first, mid);
	mergeSort(a, mid+1, last);
	merge(a,first,mid,last);
}
void print(char *p,int len)
{
	for (int i = 0; i < len; i++)
	{
		printf("%c", p[i]);
	}
	printf("\n");
}

int main(void)
{
	char p[] = { 'E','X','A','M','P','L','E' };
	int len = sizeof(p)/sizeof(*p);

	printf("mergeSort before: ");
	print(p, len);

	mergeSort(p, 0, len-1);

	printf("mergeSort  after: ");
	print(p, len);

	system("pause");

	return 0;
}
```
**运行结果 ：**

![归并排序结果](http://img.blog.csdn.net/20171014192955985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

---
###归并排序的实例2
***eg 2 ：***有N个整数，A[1]...A[N],需要找到这样的（i，j）的数对d的**数量**；满足要求：
	>  1 <= i < j<= N  && A[i]>A[j]
	> 数据范围：1<=N<=65537   , 0< A[i] <10^9
	> 算法时间复杂度为O(nlgn)

---
###思路分析:
  采用归并排序的思想，先把问题的规模变小，问题可以简化为三个子问题，解决A[]左边存在多少逆序对，A[]右边存在多少逆序对，横跨mid的有多少逆序对。
  
  ![逆序对问题](http://img.blog.csdn.net/20171014194748146?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

---
``` c/c++
/*
* 归并排序：C 语言
*
* @author Erice_s
* @date 2017/10/13
*
* 参数说明：
*    a -- 一个可排序的数组
*    l -- 左边界
*    m -- 中值
*    r -- 右边界
*/

#define _CRT_SECURE_NO_WARNINGS
#include  <iostream>
#include  <cstdio>

using  namespace  std;

#define  MAXN  65537
int  a[MAXN];
int  la[MAXN], ra[MAXN];
long  long  cnt; // 逆序对数目

void  merge (int l,  int m, int r) //l  左边界，m 中值 ， r 右边界
{
   int  p1 = 0, p2 = 0, j, k;
	for ( int i = l ; i <= m; i++)
		la[p1++] = a[i]; // 初始化 la 数组
	for (int  i = m+ 1; i <= r; i++)
		ra[p2++] = a[i]; // 初始化 ra 数组
		la[p1] = ra[p2] = 0x3f3f3f3f;
	j = k = 0;
	for (int  i = l; i <= r; i++)
	{
		if (la[j] <= ra[k])
			a[i] = la[j++];
		else
		{
			a[i] = ra[k++];
			cnt += p1 -j;
		}
	}
}

void  mergeSort( int l, int  r)
{
	if (l >= r)
		    return;
	int  m = (l & r) +((l ^ r)>> 1);
	mergeSort(l, m);
	mergeSort(m + 1, r);
	merge(l, m, r);
}

int  main()
{
	int  n;
	while (~scanf("%d", &n))
	{
		cnt = 0;
		for (int i = 0; i < n; i++)
			scanf("%d", &a[i]);
		mergeSort(0, n -1);
		printf("测试数组的元素个数： %d\n", n);
		printf("逆序对个数为：%lld \n", cnt);
	}
	return 0;
}
```
---
**运行结果**
![逆序对](http://img.blog.csdn.net/20171014201138012?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXJpY2Vfcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
  
### aha
 >好久没写过博客了，今天就写到这里了，欢迎小伙伴叨扰!  merci...