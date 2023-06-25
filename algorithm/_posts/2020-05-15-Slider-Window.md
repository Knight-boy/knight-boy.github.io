---
title:"Slider Window"
date: 2020-05-15
image: /assets/img/blog/IMG_0275.JPG
description: >
  Slider window algorithm!
author: author2
comments: true
---

求最小值，利用单调增；求最大值，利用单调减；

## 求窗口里面的最大与最小值

题目描述：求下表中给定窗口k的每个窗口中的最大值与最小值，并输出

| 窗口位置            | 最大值 | 最小值 |
| ------------------- | ------ | ------ |
| [1 3 -1] -3 5 3 6 7 | 3      | -1     |
| 1 [3 -1 -3] 5 3 6 7 | 3      | -3     |
| 1 3 [-1 -3 5] 3 6 7 | 5      | -3     |
| 1 3 -1 [-3 5 3] 6 7 | 5      | -3     |
| 1 3 -1 -3 [5 3 6] 7 | 6      | 3      |
| 1 3 -1 -3 5 [3 6 7] | 7      | 3      |

```c++
class Solution {
public:
    void SlideWindow(const vector<int> &nums, int k)
    {
        constexpr int N = nums.size();
        vector<int> que(N);
        int hh = 0;
        int tt = -1;
        // 求最小值
        for (int i = 0; i < N; i++) {
            if ((hh <= tt) && (i - que[hh] + 1 > k)) {
                hh++;
            }

            while ((hh <= tt) && (nums[que[tt]] > nums[i])) {
                tt--;
            }
            que[++tt] = i;
            if (i + 1 >= k) {
                cout << nums[que[hh]] << " ";
            }
        }
        cout << endl;

        que.clear();
        // 求最大值
        for (int j = 0; j < N; j++) {
            if ((hh <= tt) && (j - que[hh] + 1 > k)) {
                hh++;
            }

            while ((hh <= tt) && (nums[que[tt]] < nums[j])) {
                tt--;
            }
            que[++tt] = j;
            if (j + 1 >= k) {
                cout << nums[que[hh]] << " ";
            }
        }
    }
};
```

