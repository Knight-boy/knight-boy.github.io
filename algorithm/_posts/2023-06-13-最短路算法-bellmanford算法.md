---
title: 最短路算法-bellmanford算法
date: 2023-06-13
image: /assets/img/blog/IMG_0256.JPG
description: >
  use bellmanford easily!
author: author2
comments: true
---

边的权重是负数，且有边数限制的最短路。

## bellmanford算法

使用场景：

- 存在边数限制
- 所有边的权值可能是负数

题目举例：

求从节点`1`移到节点`n`最多经过`k`条边的最短距离，如果不存在这个路径，则输出`-1`。

```c++
#include <iostream>
#include <vector>
#include <climits>

using namespace std;
constexpr int INF = 0x3f3f3f3f;

class Solution {
public:
    int bellmanford(vector<tuple<int, int, int>> &edges, int n, int k)
    {
        int m = edges.size();
		dist.resize(2 * m);
        std::generate(dist.begin(), dist.end(), []() mutable {return INF;});
        dist[1] = 0;
        
        for (int i = 0; i < k; i++) {
            backup = dist;
            for (int j = 0; j < m; j++) {
                auto [a, b, w] = edges[j];
                dist[b] = min(dist[b], backup[a] + w);
            }
        }
        
        return dist[n] > INF / 2 ? INT_MIN : dist[n];
    }
    
    vector<int> dist;
    vector<int> backup;
}

```

