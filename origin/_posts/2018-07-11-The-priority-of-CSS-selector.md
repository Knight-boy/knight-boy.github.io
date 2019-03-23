---
title: "CSS选择器的优先级"
date: 2018-07-11
image: /assets/img/blog/伪类.png
description: >
  No pain, no gains!
author: author2
comments: true
---

### 选择器的权重值

- !important的权重值最高，用 [1,0,0,0]表示；
- ID的权重值是 [0,1,0,0];
- class类的权重值是[0,0,1,0];
- 伪类的权值是[0,0,1,0];
- 属性选择器的权值是[0,0,1,0];
- HTML标签的权重是[0,0,0,1];
- 伪对象选择的权值是[0,0,0,1];
- 通配符的权值最小[0,0,0,0];

**注意：**权值的大小跟选择器类型和数量有关；样式的优先级跟样式的定义顺序有关。
{:. message}


因此在同一级别的优先级顺序是：important>内联样式>类>标签|伪类|属性选择器>伪对象>通配符>继承，同时，同一级别的优先级来说，数量多的优先级要高。

### 选择器的类型

![基本选择器](/assets/img/blog/基础的选择器.png)

![组合选择器](/assets/img/blog/组合选择器.png)

![伪类](/assets/img/blog/伪类.png)

![伪元素](/assets/img/blog/伪元素.png)

![属性选择器](/assets/img/blog/属性选择器.png)

有人把选择器的效率做了一个排序[^1]：

1. id选择器
2. 类选择器
3. 标签选择器
4. 相邻选择器(div+p)
5. 子选择器(ul<li)
6. 后代选择器(li a)
7. 通配符选择器(`*`)
8. 属性选择器(E[att=val])
9. 伪类选择器(a:hover,li:nth-child)





[^1]: https://developer.mozilla.org/zh-CN/docs/Learn/CSS