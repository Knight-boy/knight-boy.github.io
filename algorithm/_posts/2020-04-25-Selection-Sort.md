---
title: 选择排序
date: 2020-04-25
image: /assets/img/blog/IMG_0263.JPG
description: >
  selection sort!
author: author2
comments: true
---

排序算法(五)

```c++
/*
 *简单选择排序(在要排序的一组数中，选出最大或最小的一个数与第一个位置的数交换；
 然后在剩下的数当中再找最大或最小的与第二个位置交换，直至最后两个元素比较后为止)
 *时间复杂度：
 *    最坏情况O(n^2);
 *    最好情况O(n^2);
 *    平均情况O(n^2);
 *稳定性：不稳定
 */
#include "stdafx.h"
#include <iostream>

using namespace std;
void print(int a[], int n, int i)
{
	cout<<"第"<<i+1<<"趟: ";
	for(int j=0;j<n;j++)
	{
		cout<<a[j]<<" ";
	}
	cout<<endl;
}

/*
 *查找数组的最小值
 *@return int 数组的键值
 */
int SelectionMinKey(int a[], int n, int i)
{
	int k=i;
	for(int j = i+1;j<n;j++)
	{
		if(a[k]>a[j]) k=j;
	}
	return k;
}
/*
 *简单选择排序
 */
void SelectionSort(int a[], int n)
{
	int key, tmp;
	for(int i=0;i<n;i++)
	{
		key = SelectionMinKey(a, n, i);
		if(key!=i)
		{
			tmp = a[i];
			a[i] = a[key];
			a[key] = tmp;
		}
		print(a, n, i);
	}
}
/*
 *二元选择排序
 */
void SelectSort(int r[], int n)
{
	int i=0,j=0, min, max, tmp;
	for(i=0;j<n/2;i++)
	{
		min = i;max=i;
		for(j=i;j<n-i;j++)
		{
			if(r[j]<r[min])
			{
				min=j;continue;
			}
			if(r[j]>r[max])
			{
				max=j;
			}
		}
		int mintmp=0;
		int maxtmp=0;
		mintmp = r[min];
		r[min]=r[n-i-1]; 
		r[n-i-1]=mintmp;
	
    maxtmp = r[max];
	  r[max]=r[i];
	  r[i]= maxtmp;
	}
	for(i=0;i<n;i++)
		cout<<r[i]<<" ";
	cout<<endl;
}
int _tmain(int argc, _TCHAR* argv[])
{
	int a[8]={3,8,7,5,6,4,9,2};
	cout<<"初始值: ";
	for(int j=0;j<8;j++)
	{
		cout<<a[j]<<" ";
	}
	cout<<endl<<endl;
	//SelectionSort(a, 8);//简单选择排序
	SelectSort(a,8);//二元选择排序
	system("pause");
	return 0;
}
```

