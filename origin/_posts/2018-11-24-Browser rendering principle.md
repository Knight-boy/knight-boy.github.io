---
title:  "浏览器原理"
image: /assets/img/blog/browserthro.jpg
description: >
  从地址栏到页面呈现过程，从浏览器多进程到JS单线程，从JS单线程最后到JS运行机制
author: author2
comments: true
---

​	来源于[浏览器的工作原理：网络浏览器幕后揭秘](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)

## 1 浏览器的高级架构

​	浏览器的主要组件是：

1. **用户界面(The user interface)**：这包括地址栏，后退/前进按钮，书签菜单等。浏览器的每个部分都显示除了您看到所请求页面的窗口。
2. **浏览器引擎(The browser engine)**: 在UI和渲染引擎之间进行编组操作。
3. **渲染引擎(The rendering engine)**：负责显示请求的内容。例如，如果请求的内容是HTML，则呈现引擎解析HTML和CSS，并在屏幕上显示解析的内容。
4. **网络(Networking)**：用于网络调用，例如HTTP请求，在独立于平台的界面后面使用不同平台的不同实现。
5. **UI后端(UI backend)**：用于绘制组合框和窗口等基本小部件。此后端公开了一个非平台特定的通用接口。它下面使用操作系统用户界面方法。
6. **JavaScript解释器(Javascript interpreter)**。用于解析和执行JavaScript代码。
7. **数据存储(Data storage)**。这是一个持久层。浏览器可能需要在本地保存各种数据，例如cookie。浏览器还支持存储机制，例如localStorage，IndexedDB，WebSQL和FileSystem。

![浏览器组件](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/layers.png)

<center><b>图0 </b>浏览器组件</center>

## 2 渲染引擎

​	不同的浏览器使用不同的渲染引擎：Internet Explorer使用Trident，Firefox使用Gecko，Safari使用WebKit。Chrome和Opera（来自15版）使用Blink，一个WebKit的分支。

​	WebKit是一个开源渲染引擎，起初是Linux平台的引擎，并由Apple修改以支持Mac和Windows。有关详细信息，请参阅[webkit.org](http://webkit.org/)。	

## 3 主流程(The main flow)

​	渲染引擎首先通过网络获得所请求文档的内容，通常以8K分块的方式完成。渲染引擎在获取内容后的基本流程如下：

​	解析html用以构建DOM树，然后构建Render树(呈现树)，接着布局Render树，最后绘制Render树。流程如**图1**所示。

![渲染流程](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/flow.png)

<center><b>图1 </b>渲染引擎基本流程</center>

- 首先渲染引擎开始解析html文件，并将html标签转化为内容树中的DOM节点。接着，它解析外部CSS文件及Style标签中的样式信息。这些样式信息以及html中的可见性指令将被用来构建另一棵树-Render树。

- Render树由一些包含有颜色和大小等属性的矩形区域块组成，它们将被按照正确的顺序显示到屏幕上。

- Render树构建好了之后，将会执行Render树布局，它将确定每个节点在屏幕上的确切坐标。再下一步就是绘制，即遍历Render树，并使用UI后端层绘制每个节点。

- 值得注意的是，这个过程是逐步完成的，为了更好的用户体验，渲染引擎将会尽可能早的将内容呈现到屏幕上，并不会等到所有的html都解析完成之后再去构建和布局Render树。它是边解析边显示，解析一部分内容就显示一部分内容，同时，可能还会通过网络下载其余内容。

![Webkit引擎解析模式](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/webkitflow.png)

<center><b>图2 </b>Webkit引擎渲染主流程</center>

![Gecko渲染主流程](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/image008.jpg)

<center><b>图3 </b>Gecko引擎渲染主流程</center>

​	从**图2**和**图3**中可以看出，尽管Webkit和Gecko使用的术语稍有不同，他们的主要流程基本相同。Gecko称可见的格式化元素组成的树为frame树，每个元素都是一个frame，Webkit则使用Render树这个名词来命名由渲染对象组成的树。Webkit中元素的定位称为布局，而Gecko中称为回流。Webkit称利用DOM节点及样式信息去构建Render树的过程为attachment，Geoko在html和DOM树之间附加了一层，这层称为内容接收器，相当制造DOM元素的工厂。

## 4 浏览器的进程

### 4.1 多进程的浏览器

* Browser进程：浏览器的主进程(负责协调、主控)，仅有一个。作用包括：
   - 负责浏览器界面显示，与用户交互。如前进，后退，标签，主页等；
   - 负责各个页面的管理，创建和销毁等其他过程；
   - 将Renderer进程得到的内存中的Bitmap，绘制到用户界面上；
   - 网络资源的管理里，下载等；
* 第三方插件进程：每种类型的插件对应一个进程，仅当使用该插件时才创建，如通过浏览器的扩展程序，使用PDF查看器插件就能显示PDF文档。
* GPU进程：最多一个，用于3D绘制等。
* 浏览器渲染进程(浏览器内核，Render进程，内部是多线程)：默认每个Tab页面一个进程，互不影响，主要作用是页面渲染，脚本执行，事件处理等(浏览器也会做些优化，把多个空白Tab页合并成一个进程)。

> 优势：
>
> + 避免单个page crash影响到整个浏览器的运行
> + 避免第三方插件crash影响到真个浏览器
> + 多进程充分利用多核优势
> + 方便使用沙盒模型隔离插件等过程，提高浏览器的生存能力 *

**注意：** 这样做会带来内存的不小消耗，但从长远来看，在浏览器中用空间换时间的策略还是值得的。
{: .message}


### 4.2 浏览器内核

+ **GUI渲染线程**
  - 负责渲染浏览器界面，解析HTML，CSS，构建DOM树和呈现树(Render tree)，布局呈现树和绘制呈现树。
  - 当界面需要重绘(Repaint)或由于某种操作发生回流(Reflow)时，该线程就会执行。
  - **注意**：**GUI渲染线程与JS引擎线程是互斥的**，当JS引擎执行时GUI线程就会被挂起(Pending)，GUI更新会被保存在一个队列中等到JS空闲时立即被执行。
+ **JS引擎线程**
  + JS引擎线程也称JS内核，负责处理Javascript脚本程序(V8引擎)。
  + JS引擎线程负责解析Javascript脚本，运行代码。
  + JS引擎一直等待任务队列中任务的回调过程，并加以处理，一个Tab页中有且仅有一个JS线程在运行JS程序。
  + **注意**：**GUI渲染线程与JS引擎线程是互斥的**，所以如果JS执行时间太长，就会导致页面渲染不连贯，导致页面渲染加载阻塞。(比如在本站打开文章“优化网站性能的35条规则“时偶尔会出现此类情况”)。
+ **事件触发线程**
  + 归属于浏览器而不是JS引擎，用来控制事件循环。
  + 当JS引擎执行代码块setTimeout时(也可以来自浏览器内核的其它线程，如鼠标点击、AJax异步请求等)，会将对应任务添加到事件线程中。
  + 当对应的事件符合触发条件时，该线程会把事件添加到任务队列尾，等待着JS引擎空闲。
  + **注意**：由于JS单线程的关系，所以在处理任务队列中的任务时必须在JS引擎空闲时按照队列中先后顺序依此执行。
+ **定时触发线程**
  + 即是`setTimeout`和`setInterval`所在的线程。
  + 浏览器定时计数器并不是由Javascript引擎计数的(因为Javascript引擎是单线程的，处于阻塞状态会影响计时器的准确性)。
  + 因此通过单独的线程来计时并触发定时(计时完毕后，添加到事件队列中，等待JS引擎空闲时执行)。
  + **注意**：W3C在HTML标准中规定，规定要求setTimeout中低于4ms的时间间隔算作4ms。
+ **异步http请求线程**
  + 在`XMLHttpRequest`在连接后是通过浏览器新开的一个线程请求
  + 将检测到状态变更时，如果设置有回调函数，异步线程就产生**状态变更事件**，将这个回调再放入事件队列中。再由JavaScript引擎执行。
![浏览器内核](https://user-gold-cdn.xitu.io/2018/1/21/1611938b2d39a5b2?imageslim)

## 5 Reference

[浏览器的工作原理：新式网络浏览器幕后揭秘](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)

[从浏览器多进程到JS单线程，JS运行机制](https://juejin.im/post/5a6547d0f265da3e283a1df7)

[如何由一道题完善自己的前端知识体系](http://www.dailichun.com/2018/03/12/whenyouenteraurl.html)