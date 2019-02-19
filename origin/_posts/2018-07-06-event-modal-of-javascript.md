---
title: "Javascript中的事件模型"
date: 2018-07-12
image: /assets/img/blog/event.jpg
description: >
  No pain, no gains!
author: author2
comments: true
---

JavaScript基础事件模型。

### 事件与事件流

​        事件是浏览器与文档交互的瞬间，如点击按钮、填写表格等操作，它是Javascript与HTML之间沟通的桥梁。DOM是树状结构，如果同时给父节点都绑定事件时，当触发子节点的时候，这两个事件的发生顺序就牵涉到事件流的内容，它描述的是页面接受时间的顺序。事件流描述的是从页面接收事件的顺序，但比较有意思的是IE和Netscape开发团队居然提出了差不多完全相反的概念。IE的事件流是事件冒泡流，而Netscape Communicator的事件流是事件捕获流。

​       从而使得 事件流主要分为两种：事件冒泡和事件捕获。

​        IE的事件流叫做事件冒泡，即事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。Netscape团队提出的另一种事件流叫做事件捕获（event capturing)，事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。捕获的用意在于在事件到达预定目标前捕获它。

​        DOM2级事件规定的事件流包括三个阶段:事件捕获阶段、处于目标阶段、事件冒泡阶段。首先发生的是事件捕获，为截获事件提供了机会。然后是实际的目标接收到事件。最后一个阶段是冒泡阶段，可以在这个阶段对事件做出响应。

### **DOM0级事件模型**   

​        DOM0级事件模型是早期的事件模型，又称为原始事件模型，在该模型中，事件不会传播，即没有事件流的概念。事件绑定监听函数较为简单，要使用Javascript指定事件处理程序，首先必须取得一个要操作的对象的引用。

​        每个元素（包括window和document）都有自己的事件处理属性，这些属性通常全部小写，如onclick。将这种属性设置成函数就可以指定事件处理程序：

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function() {
    alert("Clicked!");
};
btn.onclick = null; //删除事件处理程序
//HTML事件处理程序
<form method="post">
    <input type="text" name="username" value="">
    <input type="button" value="Username" onclick = "alert(username.value)">
</form>
```

###  **DOM2级事件模型**

​        **在这种模型中，分为三个过程：事件捕获阶段、处于目标阶段7事件冒泡阶段；**

- 事件捕获阶段(capturing phase)。事件从document一直向下传播到目标元素, 依次检查经过的节点是否绑定了事件监听函数，如果有则执行。
- 事件处理阶段(target phase)。事件到达目标元素, 触发目标元素的监听函数。
- 事件冒泡阶段(bubbling phase)。事件从目标元素冒泡到document, 依次检查经过的节点是否绑定了事件监听函数，如果有则执行。

​        DOM2级定义了两个方法，用于处理指定和删除事件处理程序的操作，addEventListener()和removeEventListener()。所有DOM节点都包含这两个方法，并且有三个参数，要处理的事件名、作为事件处理程序的函数和一个布尔值。要在click事件添加事件处理程序，可以用：

```javascript
var btn = document.getElementById("myBtn");
btn.addEventListener("click", functioin() {
    alert(this.id);
}, false);
btn.addEventListener("click", function() {
    alert("Hello Kid");
}, false);
```

​        此时执行顺序是顺序执行："myBtn"  "Hello kid"。IE中的话执行顺序正好相反。

​        移除事件监听函数的方式如下：

```javascript
var btn = document.getElementById("myBtn");
var handler = function() {
    alert(this.id);
};
btn.addEventListener("click", handler, false);
// 
btn.removeEventListener("click", handler, false);
```

​        这里只能用函数表达式的形式作为事件处理程序，因为removeEventListener()移除的时候要求传入的参数与添加应用程序时使用的参数相同。而通过匿名函数添加的事件监听函数将无法被移除。

### **IE中的事件模型**

​        IE中使用与DOM中类似的两个方法：attachEvent()和detachEvent()。这两个方法接受相同的两个参数，事件处理程序名称和事件处理程序函数。由于IE8及更早的版本只支持事件冒泡，所以通过attachEvent()添加的事件处理程序都会被添加到冒泡阶段。如果使用attachEvent()为按钮添加一个事件处理程序可用：

```javascript
var btn = document.getElementById("myBtn");
var handler = function() {
    alert(this.id);
};
btn.attachEvent("onclick", handler);//添加事件处理程序
btn.detachEvent("onclick", handler);//删除事件处理程序
```

### **事件对象**

*DOM中的事件对象*

- type表示被触发的事件类型
- target表示事件的目标
- currentTarget表示事件处理程序当前正在处理事件的那个元素
- cancelable (Boolean) 表明是否可以取消事件的默认行为
- bubbles （Boolean）表明事件是否冒泡
- perventDefault()取消事件的默认行为。如果cancelable为true，则可以使用这个方法
- stopPropagation()取消事件的进一步捕获或冒泡。如果bubbles为true，则可以使用这个方法。

*IE中的事件对象* 

- type表示被触发的事件类型
- srcElement表示事件的目标
- cancelBubble （Boolean）默认是false，将其设为true就可以取消事件冒泡
- returnValue (Boolean) 默认是true，将其设置为false就可以取消事件的默认行为

​        有了上面的事件对象，就可以写出跨浏览器的事件对象封装成事件包裹了。

```javascript
var EventUtil = {
    addHandler: function(element, type, handler){
        if (element.addEventListener){
            element.addEventListener(type, handler, false);
        } else if (element.attachEvent){
            element.attachEvent("on" + type, handler);
        } else {
            element["on" + type] = handler;
        }
    },

    removeHandler: function(element, type, handler){
        if (element.removeEventListener){                 //DOM2
            element.removeEventListener(type, handler, false);
        } else if (element.detachEvent){                  //IE
            element.detachEvent("on" + type, handler);
        } else {
            element["on" + type] = null;                  //DOM0
        }
    },

    getEvent: function(event){
        return event ? event : window.event;
    },

    getTarget: function(event){
        return event.target || event.srcElement;
    },

    preventDefault: function(event){
        if (event.preventDefault){
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },

    stopPropagation: function(event){
        if (event.stopPropagation){
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
};
```

 