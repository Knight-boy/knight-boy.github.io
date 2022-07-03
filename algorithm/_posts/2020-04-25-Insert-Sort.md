---
title: 插入排序
date: 2020-04-25
image: /assets/img/blog/default.png
description: >
  insert sort!
author: author2
comments: true
---

排序算法(六)

```c++
/*
 *直接插入排序
 *时间复杂度：
 *    最好情况：O(n)，
 *    最坏情况：O(n^2)，
 *    平均情况：O(n^2)；
 *空间复杂度：O(1)
 *稳定性：稳定
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

void InsertSort(int a[], int n)
{
	for(int i=1;i<n;i++)
	{
		if(a[i]<a[i-1])//若第i个元素小于i-1个元素，移动有序表后插入；否则，直接插入。
		{
			int j=i-1;
			int R = a[i];//复制为哨兵,即存储待排序元素
			a[i]=a[i-1];//先后移一个元素
			while(R<a[j])//在有序表查找插入位置
			{
				a[j+1]=a[j];
				j--;//将元素后移
			}
			a[j+1]=R;//插入到正确的位置
		}
		print(a, n, i);//打印每趟排序结果
	}
}

int _tmain(int argc, _TCHAR* argv[])
{
	int a[8]={49,38,65,97,76,13,27,49};
	InsertSort(a,8);
	print(a,8,8);
	system("pause");
	return 0;
}
```

