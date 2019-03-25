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

>```html 
><div v-bind:class="{active: isActive}"></div><!--"isActive"是一个truthy值-->
><div v-bind:class="classObject"></div><!--外部引用data-->
>```

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
<my-component class="{active: isActive}>Hello</my-component><!--当isActive值是truthy时，类名active才会有效-->
~~~

##### style的绑定

###### #对象语法

> ```html
> <div v-bind:style="{color: activeColor, fontSize:fontSize + 'px'}"></div>
> ```

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

**.lazy**: 默认情况下，`v-model` 会在每次 `input` 事件触发之后，将数据同步至 input 元素中（除了[上述](https://vue.docschina.org/v2/guide/forms.html#vmodel-ime-tip)提到的输入法组合文字时不会）。可以添加 `lazy` 修饰符，从而转为在触发 `change` 事件后同步：

**.number**:如果想要将用户的输入，自动转换为 Number 类型（译注：如果转换结果为 NaN 则返回字符串类型的输入值），可以在 `v-model` 之后添加一个 `number` 修饰符，来处理输入值。

**.trim**：如果想要将用户的输入，自动过滤掉首尾空格，可以在 `v-model` 之后添加一个 `trim` 修饰符，来处理输入值：

###### #组件上使用

HTML 内置的几种 input 类型有时并不总能满足需求。幸运的是，使用 Vue 可以创建出可复用的输入框组件，并且能够完全自定义组件的行为。

详细：[表单input绑定](https://vue.docschina.org/v2/guide/forms.html)

#### 虚拟DOM

#### 框架对比