---
title: 希尔排序
date: 2020-04-25
image: /assets/img/blog/IMG_0262.JPG
description: >
  shell sort!
author: author2
comments: true
---

排序算法(四)

```c++
/*
 *希尔排序
 *时间复杂度：
 *    最坏情况：O(n^2)
 *    最好情况：O(n)
 *    平均情况：O(n^1.2)
 *空间复杂度：O(1)
 *稳定性：不稳定
 */
#include "stdafx.h"
#include <iostream>

using namespace std;
void print(int a[], int n, int i)
{
	cout<<i<<":";
	for(int j=0;j<n;j++)
	{
		cout<<a[j]<<" ";
	}
	cout<<endl;
}
/*
 *直接插入的一般形式
 */
void ShellInsertSort(int a[], int n,int dk)
{
	for(int i=dk; i<n; ++i)
	{
		if(a[i]<a[i-dk])//若第i个元素小于i-1个元素，移动有序表后插入;否则，直接插入；
		{
			int j=i-dk;
			int R = a[i];
			a[i]=a[i-dk];//首先后移一个元素

			while(R<a[j])//在有序表中查找插入的位置
			{
				a[j+dk] = a[j];
				j-=dk;//元素后移
			}
			a[j+dk] = R;//插入到正确的位置
		}
		print(a, n, i);
	}
}
/* 
 *先按增量d(n/2，n为要排序的个数进行希尔排序)
 */
void ShellSort(int a[], int n)
{
	int dk = n/2;
	while(dk>=1)
	{
		ShellInsertSort(a, n, dk);
		dk = dk/2;
	}
}
int _tmain(int argc, _TCHAR* argv[])
{
	int a[8] = {3, 1, 5, 7, 2, 4, 9, 6};
	//ShellInsertSort(a,8,1);//直接插入排序，增量为1
	ShellSort(a,8);//希尔排序
	print(a, 8, 8);
	system("pause");
	return 0;
}
```

