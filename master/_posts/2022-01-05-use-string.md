---
title: "string"
date: 2022-01-05
image: /assets/img/blog/default.png
description: >
  use string easily!
author: author2
comments: true
---

### 周期字符串

如果一个字符串可以由某个长度为k的字符串重复多次得到，我们说该串以k为周期。例如，`abcabcabcabc`以3为周期(6，12也是其周期。输入一个长度不超过80的串，输出它的最小周期。

> 样例输入：HoHoHo
>
> 样例输出：2

```c++
// 1. 周期最小是1，最长的周期是str.size();
// 2. 周期是i, 从小到大枚举各个周期, 一旦符合条件就输出
int FindCynical(const string &str)
{
    auto len = str.size();
    for (auto i = 1; i <= len; i++) {
        if (len % i == 0) {
            bool valid = true;
            for (auto j = i; j < len; j++) {
                if (str[j] != str[j % i]) {
                    valid = false;
                    break;
                }
            }
            if (valid) {
                return i;
                break;
            }
        }
    }
    return 0;
}
```


