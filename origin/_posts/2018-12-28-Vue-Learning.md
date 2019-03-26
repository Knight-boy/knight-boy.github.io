---
title:  "Vue Learning"
image: /assets/img/blog/vue.png
description: >
  A great powerful Frame!
author: author2
comments: true
---

[Vue](https://cn.vuejs.org/index.html)、[Vue Loader](https://vue-loader.vuejs.org/)、[Vue Router](https://router.vuejs.org/)、[Vuex](https://vuex.vuejs.org/)

#### 数据绑定

##### class的绑定

###### #对象语法

```html
<div v-bind:class="{active: isActive}"></div><!--"isActive"是一个truthy值-->
<div v-bind:class="classObject"></div><!--外部引用data-->
```

```js
data: {
    classObject: {
        active: true;
        'text-danger':false;
    }
}
```

上面的内联和外部引入的渲染效果相同。除此之外，还可以将**class**和**style**与某个**computed**属性绑定在一起，此时，`computed`属性所对应的`getter`函数要返回一个对象。

~~~html
<div v-bind:class="classObject"></div>
~~~

~~~js
data: {
    isActive: true;
    error: null;
},
computed: {
    classObject: {
        return {
            active: this.isActive && !this.error,
            'text-danger': this.error && this.error.type === 'fatal'
        }
    }
}
~~~

###### #数组语法

向`v-bind:class`传入一个数组，来与calss列表对应：

~~~html
<div v-bind:class="[activeClass, errorClass]"></div>
~~~

~~~js
data: {
    activeClass: 'active',
    errorClass: 'text-danger'
}
~~~

此外，也可以通过三元表达式进行`class`的切换。

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div><!--当isActive为truthy时才添加class名"activeClass"-->
```

为了避免出现多个条件语句的繁琐，可以在数组语法中使用对象语法：

~~~html
<div v-bind:class="[{active: isActive}, errorClass]"></div>
~~~

###### #组件中使用

如果你这样声明组件：

~~~js
Vue.component('my-component', {
    template: '<p class="foo bar">Hello</p>'
})
~~~

然后在调用组件时，再添加一些类名：

~~~html
<my-component class="baz boo">Hello</my-component>
~~~

最终渲染的效果：

~~~html
<p class="foo bar baz boo">Hello</p>
~~~

同样的，`class`绑定也是如此：

~~~html
<my-component class="{active: isActive}">Hello</my-component><!--当isActive值是truthy时，类名active才会有效-->
~~~

##### style的绑定

###### #对象语法

 ```html
<div v-bind:style="{color: activeColor, fontSize:fontSize + 'px'}"></div>
 ```

```js
data: {
    activeColor: 'red',
    fontSize: 30
}
```

通常直接与data中的style对象绑定：

~~~html
<div v-bind:style="styleObject"></div>
~~~

~~~js
data: {
    styleObject: {
        color: "red",
        fontSize: '13px'
	}
}
~~~

和class对象绑定类似，通常也会和Computed属性结合使用，此时`computed`属性所对应的`getter`函数返回一个对象。

###### #数组语法

`v-bind:style`的数组语法，可以在同一元素上，使用多个style对象

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

###### #自动添加前缀

再`v-bind:style`中使用具有[浏览器厂商前缀](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix)的CSS属性如`transform`，Vue会自动检测并向style添加合适的前缀。

###### #多个值*

~~~html
<div v-bind:style="{dispaly: ['-webkit-box','-ms-flexbox','flex']}"></div>
~~~

最终只显示浏览器支持的属性。

##### 表单input绑定

可以使用**v-model**指令在表单`input`、`textarea`、`select`元素上创建**双向数据绑定**。`v-model`指令可以根据`input`的type类型，自动地以正确的方式更新元素，本质上的是通过监听用户的`input`事件来更新数据。

> `v-model`会忽略所有表单元素中`value`、`checked`或`selected`属性上的初始设置的值，而总是将Vue实例中的data作为真实数据来源。因此，必须在Javascript端的组件`data`选项中声明这些初始值，而不是在HTML端。

###### #与value属性绑定

对于*radio*、*checkbox*和*select*的`option`选项，通常可以将`v-model`与值是静态字符串的value属性关联在一起(或者在`checkbox`中，绑定到布尔值):

~~~html
<!--当选中时，`picked` 的值是字符串 "a"（译者注：如果没有 value 属性，选中时值是 null-->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` 的值是 true 或 false -->
<input type="checkbox" v-model="toggle">

<!-- 当选中第一个选项时，`selected` 的值是字符串 "abc"（译者注：如果没有 value 属性，选中时 selected 值是 option 元素内的文本）-->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
~~~

有时需要把value值与 Vue 实例上的一个动态属性绑定在一起。这时我们可以通过 `v-bind` 来实现。`v-bind` 还允许我们将 input 元素的值绑定到非字符串值。

###### #修饰符

**.lazy**: 默认情况下，`v-model` 会在每次 `input` 事件触发之后，将数据同步至 input 元素中。可以添加 `lazy` 修饰符，从而转为在触发 `change` 事件后同步：

**.number**:如果想要将用户的输入，自动转换为 Number 类型（译注：如果转换结果为 NaN 则返回字符串类型的输入值），可以在 `v-model` 之后添加一个 `number` 修饰符，来处理输入值。

**.trim**：如果想要将用户的输入，自动过滤掉首尾空格，可以在 `v-model` 之后添加一个 `trim` 修饰符，来处理输入值：

###### #组件上使用

HTML 内置的几种 input 类型有时并不总能满足需求。幸运的是，使用 Vue 可以创建出可复用的输入框组件，并且能够完全自定义组件的行为。

详细：[表单input绑定](https://vue.docschina.org/v2/guide/forms.html)

#### 虚拟DOM

##### Virtual DOM算法

DOM是比较慢的，当把简单的`div`元素的属性都打印出来，你将会大吃一鲸。而这仅仅是冰山一角，真正的DOM更为庞大，而且操作DOM时你要小心翼翼，轻微的操作就可能导致页面**重排**，将会严重影响页面性能。

相对于DOM对象，原生的**Javascript**对象处理起来更快，而且简单。DOM树可以很容易地用Javascript对象表示出来：

~~~javascript
var element = {
    tagName: 'ul',
    props: {
        id: 'list'
    },
    children: [
        {tagName: 'li',props:{class: 'item'},children: ["Item 1"]},
        {tagName: 'li',props:{class: 'item'},children: ["Item 2"]},
        {tagName: 'li',props:{class: 'item'},children: ["Item 3"]},
    ]
}
~~~

对应的HTML写法：

~~~html
<ul id="list">
    <li class="item">Item1</li>
    <li class="item">Item2</li>
    <li class="item">Item3</li>
</ul>
~~~

既然原来 **DOM** 树的信息都可以用**JavaScript**对象来表示，反过来，你就可以根据这个用 **JavaScript** 对象表示的树结构来构建一棵真正的**DOM**树。

可以用新渲染的对象树去和旧的树进行对比，记录这两棵树差异。记录下来的不同就是我们需要对页面真正的 **DOM** 操作，然后把它们应用在真正的 **DOM** 树上，页面就变更了。这样就可以做到：视图的结构确实是整个全新渲染了，但是最后操作DOM的时候确实只变更有不同的地方。

这就是所谓的 **Virtual DOM 算法**。包括几个步骤：

1. 用 **JavaScript** 对象结构表示 **DOM** 树的结构；然后用这个树构建一个真正的 **DOM**树，插到文档当中；
2. 当状态变更的时候，重新构造一棵新的对象树。然后用新的树和旧的树进行比较，记录两棵树差异；
3. 把2所记录的差异应用到步骤1所构建的真正的**DOM**树上，视图就更新了；

**Virtual DOM** 本质上就是在 **JS** 和 **DOM** 之间做了一个缓存。可以类比 CPU 和硬盘，既然硬盘这么慢，我们就在它们之间加个缓存：既然 DOM 这么慢，我们就在它们 JS 和 DOM 之间加个缓存。CPU（JS）只操作内存（Virtual DOM），最后的时候再把变更写入硬盘（DOM）。

#### 算法实现

##### #步骤一：用JS对象模拟DOM树

用JavaScript构建DOM节点

*element.js*[^1]

~~~javascript
function Element(tagName, props, children) {
    this.tagName = tagName;
    this.props = props;
    this.children = children;
}
module.exports = function(tagName, props, children) {
    return new Element(tagName, props, children);
}
~~~

上面的DOM结构可以简单的表示：

~~~js
var el = require('./element')
var ul = el('ul',{id: 'list'},[
    el('li',{class: 'item'}, ['Item 1']),
    el('li',{class: 'item'}, ['Item 2']),
    el('li',{class: 'item'}, ['Item 3'])
])
~~~

现在的`ul`只是一个Javascript对象表示的DOM结构，页面上并没有这个结构但可以构建真正的`ul`:

~~~js
Element.prototype.render = function(){
    var el = document.createElement(this.tagName)//
    var props = this.props;
    for(var propName in props) {
        var propValue = props[propName];
        el.setAttribute(propName, propValue);
    }
    var children = this.children || [];
    children.forEach(function(child) {
        var childEl = (child instanceof Element)
        	? child.render()
        	: document.createTextNode(child)
    	el.appendChild(childEl);
    })
    return el;
}
~~~

`render`方法会根据`tagName`构建一个真正的DOM节点，然后设置这个节点的属性，最后利用递归构建出所有的子节点。所以只需要：

~~~js
var ulRoot = ul.render();
document.body.appendChild(ulRoot);
~~~

上面的`ulRoot`是真正的DOM节点，把它插入到`body`中，就形成了真正的`<ul>`的DOM结构。

~~~html
<ul id="list">
    <li class="item">Item 1</li>
    <li class="item">Item 2</li>
    <li class="item">Item 3</li>
</ul>
~~~

##### 步骤二：比较两棵虚拟DOM树的差异

 正如所预料的那样，两棵树的完全的diff算法是一个时间复杂度为O(n<sup>3</sup>)的问题。但在前端中，很少会跨层移动DOM元素，所以虚拟DOM只会对同一个层级的元素进行对比：

![同层移动](/assets/img/blog/samelayer.png)

上面的`div`只会和同一层级的`div`进行对比，这种算法复杂度可以达到O(n)。

###### #深度优先遍历(DFS)，记录差异

在实际的代码中，会对新旧两棵树进行一个深度优先的遍历，这样每个节点都会有一个唯一的标记：

![DFS](/assets/img/blog/dfs.png)

在进行深度优先遍历时，每遍历到一个节点就把该节点和新的树进行对比。如果有差异就记录到一个对象里面。

~~~js
/*diff函数，对比两棵树*/
function diff(oldTree, newTree) {
    var index = 0;/*当前节点的标识*/
    var patches = {};/*用来记录节点差异的对象*/
    dfsWalk(oldTree, newTree,index,patches);
    return patches;
}
/*DFS*/
function dfsWalk(oldNode, newNode, index, patches) {
    /*对比oldNode和newNode的不同，记录下来*/
    patches[index] = [...];
    diffChildren(oldNode.children,newNode.children,index,patches);                  
}
/*遍历子节点*/
function diffChildren(oldChildren,newChildren,index,patches){
    var leftNode = null;
    var currentNodeIndex=index;
    oldChildren.forEach(function(child,i){
        var newChild = newChildren[i];
        currentNodeIndex = (leftNode && leftNode.count)/*计算节点的标识*/
        	? currentNodeIndex + leftNode.count + 1
        	: currentNodeIndex + 1;
        dfsWalk(child, newChild,currentNodeIndex,patches);/*深度遍历子节点*/
        leftNode = child;
    })
}
~~~

当图中的`div`和新的`div`有差异，当前索引是0，则：

~~~javascript
patches[0] = [{difference},{difference},...];/*数组存储新旧节点的差异*/
~~~

同理`p`是`patches[1]`，`ul`是`patches[3]`。

###### #差异类型

对DOM的操作可能会：

1. 替换掉原来的节点，例如把上面的`div`换成了`section`
2. 移动、删除、新增节点，例如把上面的`div`子节点，把`p`和`ul`顺序互换
3. 修改了节点的属性
4. 对于文本节点，文本的内容可能会改变。例如修改上面文本节点2内容为`Virtual DOM 2`

所以定义了几种差异类型：

~~~js
var REPLACE = 0
var REORDER = 1
var PROPS = 2
var TEXT = 3
~~~

对于节点替换，很简单。判断新旧节点的`tagName`是不是一样的，如果不一样就要替换掉，如`div`换成`section`，记录如下:

~~~js
patches[0] = [{
    type: REPLACE,
    node: newNode/*el('section',props.children)*/
}]
~~~

如果给`div`新增了属性`id`为`container`，记录如下：

~~~js
patches[0] = [{
    type: REPLACE,
    node: newNode/*el('section',props,children)*/
},{
    type: PROPS,
    props: {
        id: "container"
    }
}]
~~~

如果是文本节点，如上面的文本节点2，记录如下：

~~~js
patches[2] =[{
    type: TEXT
    content: "Virtual DOM2"
}]
~~~

当需要对节点进行重新排序时，如`p ul div`的顺序换成了`div p ul`，这时需要对节点进行移动。

###### #列表对比算法

假设用英文字母唯一地标识一个节点：

旧的节点顺序：

~~~
a b c d e f g h i
~~~

新的节点顺序：

~~~
a b c h d f g i j
~~~

现在知道了新旧的顺序，求最小的插入、删除操作（移动可以看成是删除和插入操作的结合）。这个问题抽象出来其实是字符串的最小编辑距离问题（[Edition Distance](https://en.wikipedia.org/wiki/Edit_distance)），最常见的解决算法是 [Levenshtein Distance](https://en.wikipedia.org/wiki/Levenshtein_distance)，通过动态规划求解，时间复杂度为 O(M * N)。但是我们并不需要真的达到最小的操作，我们只需要优化一些比较常见的移动情况，牺牲一定DOM操作，让算法时间复杂度达到线性的（O(max(M, N))。具体算法细节比较多，这里不累述，有兴趣可以参考[代码](https://github.com/livoras/list-diff/blob/master/lib/diff.js)。

我们能够获取到某个父节点的子节点的操作，就可以记录下来：

~~~js
patches[0] = [{
    type: REORDER,
    moves: [{remove or insert}, {remove or insert},...]
}]
~~~

但是要注意的是，因为`tagName`是可重复的，不能用这个来进行对比。所以需要给子节点加上唯一标识`key`，列表对比的时候，使用`key`进行对比，这样才能复用老的 DOM 树上的节点。

这样，我们就可以通过深度优先遍历两棵树，每层的节点进行对比，记录下每个节点的差异了。完整 diff 算法代码可见 [diff.js](https://github.com/livoras/simple-virtual-dom/blob/master/lib/diff.js)。

##### 步骤三：把差异应用到真正的DOM树上

因为步骤一所构建的 JavaScript 对象树和`render`出来真正的DOM树的信息、结构是一样的。所以我们可以对那棵DOM树也进行深度优先的遍历，遍历的时候从步骤二生成的`patches`对象中找出当前遍历的节点差异，然后进行 DOM 操作。

~~~js
function patch (node, patches) {
  var walker = {index: 0}
  dfsWalk(node, walker, patches)
}

function dfsWalk (node, walker, patches) {
  var currentPatches = patches[walker.index] // 从patches拿出当前节点的差异

  var len = node.childNodes
    ? node.childNodes.length
    : 0
  for (var i = 0; i < len; i++) { // 深度遍历子节点
    var child = node.childNodes[i]
    walker.index++
    dfsWalk(child, walker, patches)
  }

  if (currentPatches) {
    applyPatches(node, currentPatches) // 对当前节点进行DOM操作
  }
}
~~~

applyPatches，根据不同类型的差异对当前节点进行DOM操作：

~~~js
function applyPatches (node, currentPatches) {
  currentPatches.forEach(function (currentPatch) {
    switch (currentPatch.type) {
      case REPLACE:
        node.parentNode.replaceChild(currentPatch.node.render(), node)
        break
      case REORDER:
        reorderChildren(node, currentPatch.moves)
        break
      case PROPS:
        setProps(node, currentPatch.props)
        break
      case TEXT:
        node.textContent = currentPatch.content
        break
      default:
        throw new Error('Unknown patch type ' + currentPatch.type)
    }
  })
}
~~~

完整代码请参考[patch.js](https://github.com/livoras/simple-virtual-dom/blob/master/lib/patch.js)。

Virtual DOM 算法主要是实现上面步骤的三个函数：[element](https://github.com/livoras/simple-virtual-dom/blob/master/lib/element.js)，[diff](https://github.com/livoras/simple-virtual-dom/blob/master/lib/diff.js)，[patch](https://github.com/livoras/simple-virtual-dom/blob/master/lib/patch.js)。然后就可以实际的进行使用：

~~~js
// 1. 构建虚拟DOM
var tree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: blue'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li')])
])

// 2. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)

// 3. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: red'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li'), el('li')])
])

// 4. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)

// 5. 在真正的DOM元素上应用变更
patch(root, patches)
~~~

当然这是非常粗糙的实践，实际中还需要处理事件监听等；

本节转自： [虚拟DOM](https://github.com/livoras/blog/issues/13)

[^1]: <https://github.com/livoras/simple-virtual-dom/blob/master/lib/element.js>