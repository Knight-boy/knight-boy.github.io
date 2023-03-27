---
title: 冒泡排序
date: 2020-04-25
image: /assets/img/blog/IMG_0267.JPG
description: >
  bubble sort!
author: author2
comments: true
---

排序算法(一)

```c++
#include "stdafx.h"
#include <iostream>

using namespace std;
void bubbleSort(int a[], int n)
{
	for(int i=0;i<n-1;++i)
	{
		for(int j=0;j<n-i-1;++j)
		{
			if(a[j]>a[j+1])
			{
				int temp = a[j];
				a[j]=a[j+1];
				a[j+1]=temp;
			}
		}
	}
}
// 改进1
void bubbleSort1(int r[], int n)
{
	int i=n-1;// 初始时，最后位置保持不变
	while(i>0)
	{
		int pos = 0;
		for(int j=0;j<i;j++)
		{
			if(r[j]>r[j+1])
			{
				pos = j;
				int temp=r[j];
				r[j]=r[j+1];
				r[j+1]=temp;
			}
		}
		i=pos;
	}
}

// 改进2
void bubbleSort2(int s[], int n)
{
	int low = 0;
	int high = n-1;
	int tmp, j;
	while(low<high)
	{
		for(j=low;j<high;++j)// 正向冒泡，找到最大者（将最大者放在最后面）
		{
			if(s[j]>s[j+1])
			{
				tmp=s[j];
				s[j]=s[j+1];
				s[j+1]=tmp;
			}
		}
		--high;
		for(j=high;j>low;--j)// 反向冒泡，找到最小者（将最小者放在最前面）
		{
			if(s[j]<s[j-1])
			{
				tmp=s[j];
				s[j]=s[j-1];
				s[j-1]=tmp;
			}
		}
		++low;
	}
}
int _tmain(int argc, _TCHAR* argv[])
{
	const int length = 9;
	int str[length] = {49,38,65,97,76,13,17,27,49};
	//bubbleSort(str,length);
	//bubbleSort1(str,length);
	bubbleSort2(str,length);
	for(int i=0;i<length;++i)
	    cout<<str[i]<<" ";
	system("pause");
	return 0;
}
```

