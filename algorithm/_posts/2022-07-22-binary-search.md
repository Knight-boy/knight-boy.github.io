---
title: "binary-search"
date: 2022-07-22
image: /assets/img/blog/IMG_0326.JPG
description: >
  search algorithm!
author: author2
comments: true
---

二分搜索类问题的求解通用思路

**例题一**

已知输入格式：

第一行包含整数n和q，表示数组长度和询问个数。

第二行包含n个整数(范围1~10000范围内)，表示完整数组。

接下来有q行，每行包含一个整数k，表示一个查询元素 。

按如下格式输出：

共q行，每行包含两个整数，表示所求元素的起始位置和终止位置。

如果数组中不存在该元素，则返回`-1, -1`。

输入样式：

> 6 3
>
> 1 2 2 3 3 4
>
> 3
>
> 4
>
> 5

输出样例：

> 3  4
>
> 5  5
>
> -1 -1



**二分搜索**

![binary_search](/assets/img/blog/binary_search.png){:align="center"}

**代码实现**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n = 0;
    int q = 0;
    cin >> n >> q;
    vector<int> v(n);
    int in = 0;
    for (size_t i = 0; i < n; i++) {
        cin >> in;
        v[i] = in;
    }
    int k = 0;
    while (q--) {
        // 核心代码
        int l = 0;
        int r = n - 1;
        while (l < r) {
            int mid = l + r >> 1;
            if (v[mid] < k) {
                l  = mid + 1;
            } else {
                r = mid;
            }
        }

        if (v[l] != k) {
            cout << "-1 -1" << endl;
            continue;
        }

        int l1 = 0;
        int r1 = n - 1;
        while (l1 < r1) {
            int mid = l1 + r1 + 1 >> 1;
            if (v[mid] <= k) {
                l1 = mid;
            } else {
                r1 = mid - 1;
            }
        }
        // 核心代码
        cout << l << " " << l1 << endl;
    }
    return 0;
}
```

