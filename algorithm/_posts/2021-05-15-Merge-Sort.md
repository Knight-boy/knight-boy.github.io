---
title: "归并排序"
date: 2021-05-15
image: /assets/img/blog/default.png
description: >
  merge sort!
author: author2
comments: true
---

排序算法(七)

递归思想的运用，基本思路包括以下几步：

1. 区间[L, R]二分成 [L, mid]和[mid + 1, R]
2. 递归排序[L, mid]和[mid + 1, R]
3. 归并，将左右两侧的有序序列合并成一个有序序列

例题：

给定一个长度为n的整数序列，使用归并排序将这个数列从小到大进行排列，并将排序好的数列按顺序输出

> **输入样例**：
>
> 5
>
> 3 1 2 4 5
>
> **输出样例**:
>
> 1 2 3 4 5



```cpp
constexpr int N = 100000;
vector<int> q(N);
vector<int> temp(N);
void MergeSort(int l, int r)
{
  if (l >= r) {
    return;
  }

  int mid = l + 1 >> 1;
  MergeSort(l, mid);
  MergeSort(mid + 1, r)
  
  int k = 0; 
  int i = l;
  int j = mid + 1;
  while (i <= mid && j <= r) {
    if (q[i] <= q[j]) {
      temp[k++] = q[i++];
    } else {
      temp[k++] = q[j++];
    }
  }

  while (i <= mid) {
    temp[k++] = q[i++];
  }
  while (j <= r) {
    temp[k++] = q[j++];
  }
  
  for (int i = l, j = 0; i <= r;) {
    q[i++] = temp[j++];
  }
}

int main()
{
  int n = 0;
  int num = 0;
  cin >> n;
  for (size_t i = 0; i < n; i++) {
    cin >> num;
    q[i] = num;
  }
  MergeSort(0, n - 1);
  for (size_t j = 0; j < n; j++) {
    cout << q[j] << " ";
  }
  return 0;
}
```

