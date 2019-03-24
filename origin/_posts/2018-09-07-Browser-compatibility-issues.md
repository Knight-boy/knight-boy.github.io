---
title: "浏览器兼容性问题"
date: 2018-09-07
image: /assets/img/blog/default.png
description: >
  No pain, no gains!
author: author2
comments: true
---

你不得不知的六大浏览器：[Chrome](https://en.wikipedia.org/wiki/Google_Chrome)、[Firefox](https://en.wikipedia.org/wiki/Firefox)、[IE](https://en.wikipedia.org/wiki/Internet_Explorer)、[Opera](https://en.wikipedia.org/wiki/Opera_(web_browser))、[Safari](https://en.wikipedia.org/wiki/Safari_(web_browser))、[Microsoft Edge](https://en.wikipedia.org/wiki/Microsoft_Edge)。

作为现今使用最广泛的桌面浏览器，拿下整个浏览器行业98%的市场占有率[^1]。然而，由于历史的原因，各家的浏览器或多或少存在奇怪的的差异性。因此，在web开发过程中，能够处理以上所有浏览器的兼容性成为了不小的挑战，对开发人员自身的技术实力的要求也越来越高。

在上面所有浏览器中，最傲娇的浏览器要数**IE**了，自其诞生以来就颇具争议，由于众所周知的原因，能够在浏览器大战中存活下来本身就已经是个奇迹了，IE不同的版本差别很大，总是处在补漏中，直到[**IE11**](https://en.wikipedia.org/wiki/Internet_Explorer_11)之后才逐渐稳定下来。

本文在这里尽可能详细地总结了浏览器兼容性问题以及对应的解决方案。

##### margin加倍的问题

被设置为浮动的`div`设置的margin在IE中会加倍。这是IE6中存在的Bug。

> 解决方案是在div里面加上`display:inline;`

```html
<div id="isfloat"></div>
<style>
#isfloat{
    float: left;
    margin: 5px;/*IE6会把其作为10px*/
	display: inline;/*加上之后将其作为5px*/
}
</style>
```

`display:inline;`元素会被显示为内联元素。

提示：尽管现阶段使用IE6的客户端已经很少见了，但了解过去存在的问题和了解现在存在的问题同样重要。
{: .message}

##### IE中的浮动会产生双倍距离

```css
#box {
    float:left;
    width: 100px;
    margin: 0 0 0 100px;
    display: inline;
}
```

> ``` css
> #box{
>     display: block;
>        display: inline;
>        display: inline-block;
>        display: table;
> }
> ```
>
> `block`: 总是在新行开始，即把元素设置为块级元素，使得元素宽度、高度、行高、边距都可控制；
>
> `inline`:总是在同一行显示，即将元素设置内联元素，不可设置宽高，边距等；
>
> `inline-block`:使元素即具有内联性质又具有块级元素的特性；

提示：当把`display`设置为`none`时，会使元素脱离文档流，元素将会不可见，此时不会占据页面的位置空间，当页面状态改变时会发生[重排(Redraw)](https://www.knightboy.cn/origin/2018-07-12-Repaint-and-redraw/)
{: .message}

##### IE宽度和高度的问题

IE不能识别`min-`这个定义，但实际上它把`width`和`height`当作有`min`的情况来用。如果只使用宽度和高度，对于正常浏览器这两个值不会变，如果只用`min-width`和`min-height`，在IE中宽高相当于没有设置。

> 解决方法如下：
>
> ```css
> #box{width:80px;height:35px;}//正常显示
> html>body #box {
>     width: auto;
>     height: auto;
>     min-width: 80px;
>     min-height: 35px;
> }
> ```

##### 页面的最小宽度的问题

`min-width`比较方便，用于指定元素不小于某个宽度，能保证排版的正确性，但在低版本IE中不能使用，IE7+[^2]可用；

```css
#container{
    min-width: 600px;
    width: expression(document.body.clientwidth<600?"600px":"auto");/*已经废弃，在<IE8中存在*/
}
```

注意：不建议使用CSS Expression,只在IE5,IE6中使用，IE8+不再支持CSS表达式，会影响网站的性能。
{: .message}

##### Div的垂直居中

```css
vertical-align: middle;
line-height: 200px;
```

> `line-height:200px`将行距增加到`div`的高度，插入文字就能够实现垂直居中；但要控制内容no-wrap;

##### Div浮动IE文本产生3px的问题

当左边对象浮动，右边采用外补丁的左边距来定位，右边对象内的文本会离左边有3px的间距；

```css
#box {float: left;width: 800px;}
#left {float: left;width: 50%;}
#right {width: 50%;}
*html #left {margin-right: -3px;}
```

```html
<div id="box">
    <div id="left"></div>
    <div id="right"></div>
</div>
```

##### 高度不适应的问题

当内层对象的高度发生变化时，外层高度不能自动调节，特别是当内层使用了`margin`或`padding`时。

```html
<body>
    <div id="box">
        <p>这是一些文本。。。</p>
    </div>
</body>
```

```css
#box {
    background-color: #dedede;
    margin-top: 100px;
}
#box p {
    margin-top: 40px;
    margin-bottom: 40px;
    text-align: center;
}
```

> 解决方法是在`<p>`元素前后各加一个`<div></div>`,并对`div`设置`border`属性。



[^1]: <https://en.wikipedia.org/wiki/Web_browser>
[^2]: <https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height#Browser_compatibility>