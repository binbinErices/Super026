Leetcode只出现一次的数字
```
#include<iostream>
#include<vector>

using namespace std;

//只有一个元素出现一次，其他都出现两次
//线性时间复杂度，不申请额外空间
int singleNumber1(int num[],int length)
{
	int des=0;
	//cout << "num:" << sizeof(num) / sizeof(num[0]) << endl;//不是传副本，是地址
	for (int i = 0; i < length; i++)
	{
		des ^= num[i];
	}
	return des;
}


//只有一个元素出现一次，其他都出现三次
//线性时间复杂度，不申请额外空间
int singleNumber2(vector<int> &nums)
{
	vector<int> dight(32,0);
	
	//打印一下dight数组
	cout << "dight(32,0) print begin： ";
	for (auto v : dight)
	{
		cout << v << " ";
	}
	cout << "dight(32,0) print end..." << endl;

	for (int i = 0; i < static_cast<int>(nums.size()); i++)
	{
		for (int j = 0; j < 32; j++)
		{
			dight[j] += (nums[i] >> j) & 1;
		}
	}

	int num = 0;
	for (int i = 0; i < 32; i++)
	{
		if (dight[i] % 3 == 1)
		{
			num += 1 << i;
		}
	}
	return num;
}


//对于给定整数数组，恰好有2个元素出现一次，其他都出现两次，找出只出现一次的两个元素
//线性时间复杂度，能否使用常树空间复杂度实现
int* singleNumber3(vector<int> &nums)
{
	int z = 0;
	for (auto v : nums)
	{
		z ^= v;
	}
	int result[2] = { 0 };
	z &= (~(z - 1));

	for (auto v : nums)
	{
		if ((z&v) == 0)
			result[0] ^= v;
		else 
		{
			result[1] ^= v;
		}
	}
	
	return result;
}

int main()
{
	//case1
	int a[] = { 4, 1, 2, 1, 2, 4, 6 };
	int len = sizeof(a) / sizeof(*a);
	cout <<"singleNumber1: " << singleNumber1(a,len) << endl;

	//case2
	vector<int> vec1 = { 2, 1, 2, 2, 1, 16, 1,45,16,	16};
	cout << "singleNumber2: " << singleNumber2(vec1) << endl;

	//case3
	vector<int> vec2 = {  2, 2, 1, 16, 1, 87, 16, 99};
	int *arry = singleNumber3(vec2);
	cout << "singleNumber3: " << *arry << " singleNumber3: "<<*(arry+1)<<endl;

	system("pause");
	return 0;
} 
```
> 第一种情况的思路：
	直接for循环全部异或一次，相同的数字被抵消，就只剩下出现一次的数字。

---
>第二种情况的思路：
	new一个数组，用来存储每一位2进制的和，如果所有数字都出现三次，那么每一位都必定能被3整除，所以只要找到不能被3整除的位然后转成十进制即可。

---
>第三种情况的思路：
	简单来描述为数组为2n+x+y,其中2n是值出现两次的数字，x，y是出现一次的数字。将数组拆成2a+x,2b+y转化成情况1。
	其中a+b=n.对于两个不同的数x，y，异或的结果x^y=z一定不为0，那么我们根据z即可把x,y分隔开，同时我们也可以根据Z把
	重复出现2次的n个数也分到两组里，并且一定是成对分开。
	>>算法描述：
		1. 求Z=X^Y;
		2. 然后根据Z把所有的数分成两组numsA[],numsB[]