---
title: "CSS容器的包含块"
date: 2018-05-05
image: /assets/img/blog/CSSbox.png
description: >
  Frame model! 
author: author2
comments: true
---

一个元素的尺寸和位置经常受到包含块的影响，大多数情况下，包含块就是这个元素的祖先块元素的内容区，但并不总是这样。

​        当一个客户端代理如浏览器展示一个文档时，对于每一个元素，他都产生了一个盒模型。每一个盒子都被化成了四个区域：

![盒子区域](/assets/img/blog/CSSbox.png)

​        元素的尺寸及位置，常常会受到它的包含块的影响。对于一些属性，例如`width`,`height`,`padding`,`margin`,绝对定位元素的偏移值(如`position`被设置成`absolute`或`fixed`,当对其赋予百分比值时,这些值的计算值就是通过元素的包含块计算得来的)

​        确定一个元素的包含块的过程完全依赖于这个元素的`position`属性:

1. 如果`position`属性为`static`或`relative`,包含块就是由它的最近的祖先块元素(如`inline-block`,`block`或`list-item`元素)或格式化上下文(如 table container,flex container,grid container or the block container itself)的内容边缘组成的.

   1. 如果`position`属性为`absolute`,包含块就是由它的最近的`position`值不为`static`(即是`fixed`, `absolute`,` relative` 或`sticky`)的祖先元素的内边距的边缘组成.

2. 如果`position`属性是`fixed`,包含块就是由当前的可视窗口viewport组成.

3. 如果`position`属性是`absolute`或`fixed`,包含块也可以是由满足以下条件的最近父级元素的内边距的边缘组成:

   1. `transform`或`perspective`的属性值不是`none`;

      > If the property has a value different than `none`, a [stacking context](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context) (层叠上下文)will be created. In that case, the object will act as a containing block for any `position: fixed` elements that it contains.
      >
      > —Form [transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)

   2. `transform`或`perspective`的属性值是可变的(`will-change`);

   3. `filter`的属性值不是`none`或者是可变的(`will-change`)（仅仅在Firefox中有效）

**注意：**根元素`<html>`所在的包含块是一个被称作**初始包含块**的矩形。它是页面区域(分页媒体)或视口的区域(连续媒体)
{: .message}

​        如上所述，如果某些属性被赋予百分比的话，它的计算值是由这个元素的包含块决定的。这些属性包括盒模型属性和偏移属性。

1. 要计算`height，top`及`bottom`中的百分比值是通过包含块的**`height`**值。如果包含块的`height`值会根据它的内容变化，而且包含块的`position`属性的值被赋予`relative`或`static`，那么这种情况百分比值为`0`。
2. 要计算`width`，`left`，`right`，`padding`，`margin`这些属性由包含块的**`width`**属性的值来计算它的百分比。

**Example:**

对于html:

```html
<div id="demo">
    <section>
        <p>This is a paragraph!</p>
    </section>
</div>
```

- `<p>`标签是静态定位时，它的包含块是离它最近的块祖先元素`section`。

  ```css
  
  section {
    display: block;
    width: 400px;
    height: 160px;
    background: lightgray;
  }
  p {
    width: 50%;
    height: 25%;
    margin: 5%;
    padding: 5%;
    background: blue;
  }
  ```

<!DOCTYPE html>
<html>
  <head>
  <meta charset="utf-8">
  </head>
  <style>
    #demo1 {
      background: beige;
    }
    div section{
      display: block;
      width: 400px;
      height: 160px;
      background: lightgray;
    }
    section p{
      width: 50%;
      height: 50%;
      margin: 5%;
      padding: 5%;
      background: blue;
    } 
  </style>
  <body>
    <div id="demo1">
      <section id="sec1">
        <p id ="demop1">This is a paragraph!</p>
      </section>
    </div>
  </body>
</html>