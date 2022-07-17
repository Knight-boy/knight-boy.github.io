---
title: 快速排序
date: 2020-04-25
image: /assets/img/blog/default.png
description: >
  bubble sort!
author: author2
comments: true
---

排序算法(三)

```c++
#include "stdafx.h"
#include <exception>
#include "stdlib.h"
#include <iostream>

using namespace std;

/*
 *产生范围[start, end]之间的随机数
 */
int RandomInRange(int start, int end)
{
	return rand()%(end-start+1)+start;
}

void Swap(int* data1, int * data2)
{
  int temp;
	temp = *data1;
	*data1 = *data2;
	*data2 = temp;
}

/**
 * 实现快速排序算法的关键在于先在数组中选择一个数字，
 * 接下来把数组中的数字部分分为两部分，
 * 比选择的数字小的数字移动到数组的左边，
 * 比选择数字大的数字移动到数组的右边，
 * 这样就把该数组分成两段较小的数组，
 * 对每段数组递归处理后就可以完成排序过程
 **/
int Partition(int data[], int length, int start, int end)
{
	if (data==nullptr || length <=0 || start < 0 || end >= length)
		throw new exception("Invalid Parameters.");
	int index = RandomInRange(start, end);
	Swap(&data[index], &data[end]);

	int small = start - 1;
	for (index = start;index<end;++index) {
		if (data[index]<data[end]) {
		    ++small;
		    if (small !=index) {
			    Swap(&data[index],&data[small]);
        }
    }
	}
	++small;
	Swap(&data[small], &data[end]);
	return small;
}

void QuickSort(int data[], int length, int start, int end)
{
	if(start == end)
		return;
	int index = Partition(data, length, start, end);
	if(index>start)
		QuickSort(data, length, start, index-1);
	if(index<end)
		QuickSort(data,length, index+1, end);
}

int _tmain(int argc, _TCHAR* argv[])
{
	const int length = 14;
	int data[length] = {1,4,32,65,6,6,787,87,78,9,0,45,66,77};
	QuickSort(data, 14, 0, 13);
	for(int i =0;i<length;i++)
	{
	    cout<<data[i]<<" ";
	}
	cout<<endl;
	system("pause");
	return 0;
}

```

