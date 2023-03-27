---
title: "sort writting"
date: 2021-12-07
image: /assets/img/blog/IMG_0257.JPG
description: >
  five sort algorithm writting!
author: author2
comments: true
---

五种排序方法

```c++
std::array<int, 10> s = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3}; 

// 用默认的 operator< 排序
std::sort(s.begin(), s.end());

// 用标准库比较函数对象排序
std::sort(s.begin(), s.end(), std::greater<int>());

// 用自定义函数对象排序
struct customLess{
  bool operator()(int a, int b) const
  {   
    return a < b;
  }   
};
std::sort(s.begin(), s.end(), customLess());

// 用 lambda 表达式排序
std::sort(s.begin(), s.end(), [](int a, int b) {
  return b < a;   
});

// 用 bind 方法排序
std::sort(std::begin(s), std::end(s), std::bind(std::less<int>(), std::placeholders::_1, std::placeholders::_2));

```

