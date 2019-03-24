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

#### margin加倍的问题

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

#### IE中的浮动会产生双倍距离

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
>        display: block;
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

#### IE宽度和高度的问题

IE不能识别`min-`这个定义，但实际上它把`width`和`height`当作有`min`的情况来用。如果只使用宽度和高度，对于正常浏览器这两个值不会变，如果只用`min-width`和`min-height`，在IE中宽高相当于没有设置。

> 解决方法如下：
>
> ```css
> #box{width:80px;height:35px;}/*正常显示*/
> html>body #box {
>  width: auto;
>  height: auto;
>  min-width: 80px;
>  min-height: 35px;
> }
> ```

#### 页面的最小宽度的问题

`min-width`比较方便，用于指定元素不小于某个宽度，能保证排版的正确性，但在低版本IE中不能使用，IE7+[^2]可用；

```css
#container{
    min-width: 600px;
    width: expression(document.body.clientwidth<600?"600px":"auto");/*已经废弃，在<IE8中存在*/
}
```

注意：不建议使用CSS Expression,只在IE5,IE6中使用，IE8+不再支持CSS表达式，会影响网站的性能。
{: .message}

#### Div的垂直居中

```css
vertical-align: middle;
line-height: 200px;
```

> `line-height:200px`将行距增加到`div`的高度，插入文字就能够实现垂直居中；但要控制内容no-wrap;

#### Div浮动IE文本产生3px的问题

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

#### 高度不适应的问题

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

#### IE图片显示有空隙

解决办法包括改变html的排版，设置`img`为`display:block;`或者设置`vertical-align:top|bottom|middle|text-bottom;`来解决.

#### 文本对齐和文本输入框的问题

```css
input {
    width: 300px;
    height: 30px;
    border: 1px solid red;
    vertical-align: middle;/*添加该语句*/
}
```

#### li中显示省略号

```css
li {
    width: 200px;
    white-space:nowrap;
    text-overflow: ellipsis;
    -o-text-overflow: ellipsis;
    overflow: hidden;
}
```

#### 定义1px高度的容器

在IE6下,因为默认行高造成此类问题,解决方法包括: 设置`overflow:hidden;`,`zoom:0.08;`,`line-height:1px;`

#### div居中的问题

`div`设置`margin: 0 auto;`时已经居中,IE还需要在父级元素中设置`text-align:center;`

#### 使div在浏览器中垂直居中

实现垂直居中有很多种操作方法,详见codepen:

<iframe height="265" style="width: 100%;" scrolling="no" title="Vertical Center" src="//codepen.io/knight-boy/embed/rZPXRG/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/knight-boy/pen/rZPXRG/'>Vertical Center</a> by Knight
  (<a href='https://codepen.io/knight-boy'>@knight-boy</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

#### 链接访问样式的设置

注意排列顺序: L-V-H-A,简便记法: 又爱(LoVe)又恨(HAte)

```css
a:link{}
a:visited {}
a:hover{}
a:active{}
```

`:hover`必须位于`:link`和`:visited`之后才能生效.

#### `<ul>`的`padding`和`margin`

**ul**在Firefox中默认有`padding`值,而在IE中只有`margin`有默认值,所以先定义: `ul{margin:0;padding:0;}`

#### `<form>`标签

在IE中,会存在`margin`的默认值,在Firefox中,`margin`默认是0;为统一,可以类比`<ul>`标签.

Add:

```css
cursor:pointer;/*Firefox和IE7+*/
cursor: hand;/*IE6*/
```

#### 盒模型不一致

在IE和Firefox中Box模型不一致导致`margin`相差2px;解决办法如下:

```css
div {
    margin: 30px !important;
    margin: 28px;
}
```

在IE6中不支持!important属性,但其他浏览器支持,如此在IE6中解释成:

```css
div {
    margin: 30px;
    margin: 28px;/*最后一个覆盖前面的同属性值*/
}
```

#### float的div闭合,清除浮动

```html
<div id="floatA"></div>
<div id="floatB"></div>
<div id="NOfloat"></div>
```

对于第三个块元素,不希望继续进行浮动设置,此时在IE中没有问题,但在Firefox中因为float标签需要闭合.采用在`floatA`和`Nofloat`之间添加`<div id="clearfix"></div>`,这个`div`必须和两个具有`float`特性的元素处于同级的状态,之间不能存在嵌套关系,否则会产生异常.并采取如下样式定义:

```css
.clearfix{clear: both;}
```

#### Firefox文本无法撑开容器高度

标准浏览器中固定高度值的容器不会像IE6那样被撑开容器,可以采用设置`min-height:200px;`的方式.

```css
{
    height:auto !important;
    height: 200px;/*为了兼顾不能识别min-height的IE6*/
    min-height: 200px;
}
```

#### IE滚动条颜色

解决方法:将body修改为html

```css
html {
    scrollbar-face-color:#f6f6f6;
    scrollbar-highlight-color:#fff;
    scrollbar-shadow-color:#eeeeee;
    scrollbar-3dlight-color:#eeeeee;
    scrollbar-arrow-color:#000;
    scrollbar-track-color:#fff;
    scrollbar-darkshadow-color: #fff;
}
```

#### border中的dotted和dashed

**dotted**: 点状轮廓; **dashed**: 虚线轮廓

dotted在IE6下不兼容,显示为dashed效果.而dashed在各个浏览器中显示一致.

#### border:none;与border:0的区别

- 性能差异:

  `border: 0`把border设为"0"像素虽然在页面上看不到,但按border默认值理解,浏览器依然会对**border-width/border-color**进行渲染,即已经占用了内存值.

  `border:none;`把border设为"none",浏览器解析"none"时将不作出渲染动作,即不消耗内存值.

- 兼容性差异

  兼容性差异只针对**IE6**、**IE7**与标签*button*、*input*而言，在win7的XP主题下均会出现此现象。

  当`border：none`时对IE6和IE7无效，边框依然存在；但`border:0`时对IE6,IE7无边框；在Firefox中`border:none;`与`border:0`的显示一致；

#### 不同浏览器**margin**和**padding**差异较大

```html
<body>
    <div id="container">
        <p>container</p>
        <div id="element">element</div>
    </div>
</body>
```

```css
*{
    margin: 0;
    padding: 0;
}/*使Firefox和IE显示一致*/
#container {
    margin: 0 auto;
    border: 1px solid #000;
    background: #777;
    width: 400px;
    height: 160px;
    text-align: center;/*IE实现父级元素内的子元素居中*/
}
#element {
    background: #95cfef;
    border: 1px solid #36f;
    width: 300px;
    height: 100px;
    margin: 0 auto;/*在Firefox中用以使子元素居中*/
    padding: 5px 5px;
}
```

#### 在IE6中无法设置小高度*

```css
#element {
    background: #dedede;
    border: 1px solid #36F;
    width: 300px;
    height: 2px;
    margin: 30px 0 0 30px;
    overflow: hidden;/**/
}
```

#### 在IE6中list项目间出现空行*

```css
ul {
    width: 200px;
    margin: 0;
    padding: 0;
    list-style: none;
}
li a {
    background: #dedede;
    display: block;
    margin-bottom: 1px;
    _height: 1px;/*消除IE6在列表项上下添加的额外空间；同时使IE6的超链接响应区域扩大为块；*/
}
```

#### IE6中使用float属性会产生两倍距离*

```css
#container {
    border: 1px solid #000;
    background: #777;
    width: 400px;
    height: 150px;
    margin: 30px 0 0 30px;
}
#element {
    background: #dedede;
    border: 1px solid #36f;
    width: 400px;
    height: 150px;
    margin: 30px 0 0 30px;
    float: left;
    display: inline;/*解决手段*/
}
```

#### 溢出边界问题(BFC)

```css
#container {
    border: 1px solid #000;
    background: #777;
    width: 400px;
    height: 150px;
    margin: 30px 0 0 30px;
    overflow: hidden;/*解决方案*/
}
#element {
    border: 1px solid #000;
    background: #777;
    width: 300px;
    height: 200px;
    margin: 0 auto;
}
```

#### 最低高度设置min-height*

```css
#left,#right {
    padding: 5px;
    border: 1px solid #000;
    float: left;
    width: 70px;
    margin-right: 5px;
}
#right {
    min-height: 75px;
    _height: 75px;/*IE6 hack*/
}
```

### CSS Hack

#### <=IE6的版本识别

语法：*html 选择器{...}

```css
*html .content {}
```

#### 只有IE7识别

语法：*+html 选择器 {...}

```css
*+html .content {}
```

###### IE6及以下的版本识别

语法：_属性：值；

###### IE7及以下版本识别

语法：+属性：值；

#### 条件注释语法

###### 判断等于某个IE版本的语法

语法：`<!--[if ie [版本号]> 要判断的内容 <![endif]-->`

```html
<!--[if ie 6]
<link rel="stylesheet" type="text/css" href="index.css">
<![endif]-->
```

###### 判断IE浏览器的范围：gte(great than equal)、gt(great than)、lte(less than equal)、lt(less than)

语法：`<!--[if gte is [版本号]> 要判断的内容<![endif]-->`

###### 判断非IE浏览器

语法：`<!--[if !ie]><!-->要判断的内容<!--><![endif]-->`

###### 判断是IE浏览器

语法：`<!--[if ie]>要判断的内容<![endif]-->`

###### !important

可以根据IE(IE7能够识别`!important`)和非IE浏览器设置不同的样式，所以为了兼容IE6与火狐之间的差异，一般会用`!important`.

#### 区分IE与非IE浏览器

```css
#tip {
    background: blue;/*非IE*/
    background: red\9;/*IE6，IE7,IE8*/
}
```

#### 区分IE6、IE7、IE8、Firefox

使用符号： `\9`、`*`、`_`

```css
#tip {
    background: blue;/*Firefox*/
    background: red\9;/*IE8*/
    *background: black;/*IE7*/
    _background: yellow;/*IE6*/
}
```

说明：因为IE系列浏览器可读(`\9`)，IE6和IE7可读(`*`)，另外IE6可识别(`_`)
{: .message}

#### 区分IE6、IE7、Firefox

1. 使用符号：`*`、`_`

```css
#tip {
    background: blue;/*Firefox*/
    *background: black;/*IE7*/
    _background: orange;/*IE6*/
}
```

IE6与IE7可读(`*`)，IE6又可识别(`_`)，但是IE7却无法读取(`_`)，Firefox无法识别`_`和`*`；

2. 使用符号：`*`、`!important`

```css
#tip {
    background: blue;/*Firefox*/
    *background: black !important;/*IE7*/
    *background: orange;/*IE6*/
}
```

**说明：**

IE7可以辨识(`*`)和(`!important`)，但IE6只可以识别`*`却无法识别`!important`，Firefox可以识别`!important`但不能识别`*`。

#### 区分IE7和Firefox

识别符号： `*`、`!important`

```css
#tip {
    background: blue;/*Firefox*/
    *background: black !important;/*IE7*/
}
```

说明：Firefox可以识别(`!important`)，无法识别`*`，IE7两者都可以识别；
{: .message}

#### 区分IE6和IE7

```css
#tip {
    *background: blue;/*IE7*/
    _background: black;/*IE6*/
}
/*or*/
#tip {
    background: blue !important;/*IE7*/
    background: black;/*IE6*/
}
```

只有IE6可以识别`_`,只有IE浏览器可以识别`*`;IE7可以识别`!important`，IE6不行。CSS读取是自上而下。

[^1]: <https://en.wikipedia.org/wiki/Web_browser>
[^2]: <https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height#Browser_compatibility>