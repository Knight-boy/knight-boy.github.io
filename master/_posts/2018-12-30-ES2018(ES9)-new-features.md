---
title: ES2018(ES9)的新功能
date: 2018-12-30
image: /assets/img/blog/es7-es8-cover.png
description: >
  ECMA2018 New Features!
author: author2
comments: true
---

**在本文中，我将介绍通过ES2018(ES9)引入的JavaScript的新功能，以及它们的用途及如何使用它们。**

**Javascript(ECMAScript)**是一个不断发展的标准，由多个平台上的许多供应商共同实现，**ES6(ECMAScript 2015)**是一个大型的版本，花了足足六年的时间才完成。**ES9(ECMAScript 2018)**是目前最新的版本。



**技术委员会39（TC39）**由包括浏览器供应商在内的各方组成，他们推进Javascript提案沿着一条严格的发展道路前进；

- 第零阶段：**strawman**-最初想法的提交
- 第一阶段：**proposal**(提案)-由TC39一名正式成员倡导的正是提案文件，该文件包括API示例
- 第二阶段：**draft**(草案)-功能规范的初始版本，包括两个实验性实例
- 第三阶段：**candidate**(候选)-提案规范通过审查并从厂商那里手机反馈信息
- 第四阶段：**finished**(完成)-提案已准备好包含在**ECMAScript**中，一个功能只有达到此阶段时才能被视为标准。但是，在浏览器和[**Node.js**](https://nodejs.org)等运行中时可能还需要更长的时间。

## ES2016

ES2016通过添加两个小功能证明了标准化过程：

1. 数组中添加[include()][include]
2. 添加[\*\*][pow]运算符，`a**b`与`Math.pow(a,b)`相同

## ES2017

ES2017提供了更多的新功能：

- 异步函数可实现更清晰的**`Promise`**语法
- **`Object.values()`**返回一个给定对象自己的所有可枚举属性值的数组，值的顺序与使用for...in循环遍历的顺序相同（**`for...in`**枚举原型链中的属性）
- **`Object.entries()`**返回一个给定对象自身可枚举属性的键值对数组，其排列与使用**`for...in`**循环遍历对象是返回的顺序一致（**`for...in`**枚举原型链中的属性）
- **`Object.getOwnPropertyDescriptors`**返回一个对象所有自身属性的描述符（.value, .writable, **.get**, **.set**, .configurable, .enumerable）
- **`padStart()`**和**`padEnd()`**都可以填充字符串长度
- 结尾`,`(逗号)，数组定义和函数参数列表
- **`ShareArrayBuffer`**和**`Atomics`**用于从共享内存位置读取和写入

关于更多ES2017的信息请参考[What's new in ES2017][ES8]

## ES2018

**ECMAScript 2018 (ES9)**现在已经推出，以下功能已经达到第四个阶段，但各个浏览器的运行情况稍有差别。

### 异步迭代

在**async/await**的某些时刻，你可以尝试在同步循环中调用异步函数。如：

``` javascript
async function process(array) {
    for(let i of array) {
        await doSomething(i);
    }
}
```

它不会起作用，下面代码也不会：

``` javascript
async function(array) {
    array.forEach(async i => {
        await doSomething(i);
    });
}
```

循环本身保持同步，并且总是在它们的内部异步操作之前完成。



**ES2018**引入了异步迭代器(**asyncchronous iterators**)，它与常规迭代器一样，只是返回一个**Promise**。因此，**await**关键字可以和**for...in**循环一起使用，以串行方式运行异步操作。例如：

``` javascript
async function process(array) {
    for await(let i in array) {
        doSomething(i);
    }
}
```

### Promise.finally()

**Promise**链要么成功到达最后一个**.then()**，或者触发失败**.catch()**，在某些情况下，你想要无论**Promise**成功还是失败，运行相同的代码，例如清除，删除对话，关闭数据库连接等等。

**.finally()**允许你指定一个最终的逻辑而不是在最后重复**.then()**，和**.catch()**:

```javascript
function doSomething() {
    doSomething1()
    .then(doSomething2)
    .then(doSomething3)
    .catch(err => {
        console.log(err);
    })
        .finally(()=>{
        //finish here!
    });
}
```

### Rest/Spread属性

**ES2015**引入了[Rest参数][rest]和[扩展运算符][...]。三点（**...**）表示法仅用于数组操作。**Rest**参数语法将传递给函数的最后一个参数转换为数组：

``` javascript
restParam(1,2,3,4,5);
function restParam(p1,p2, ...p3) {
    //p1 = 1
    //p2 = 2
    //p3 = [3, 4, 5]
}
```

**Spread运算符**以相反的方式工作，并将数组转换为可以传递给函数的单独的参数，例如，给定任意数量的参数，**Math.max()**返回给定数字中的最大值。

``` javascript
const values = [99, 100, -1, 49, 17];
console.log(Math.max(...values));//100
```



**ES2018**支持对象解构和数组类似的**Rest/Spread**扩展功能。一基本的例子：

``` javascript
const myObj = {
    a: 1,
    b: 2,
    c: 3
};
const {a, ...x} = myObj;
//a = 1
//x = {b: 2, c: 3}
```



或者您可以使用它将值传递给函数“

```javascript
restParam({
    a: 1,
    b: 2,
    c: 3
});
function restParam({a, ...x}){
    //a = 1
    //x = {b: 2, c: 3}
}
```

与数组一样，您只能在声明结尾处使用单个**rest参数**。此外，它仅适用于每个对象的顶层而不是子对象。



扩展运算符可以在其他对象中使用，如：

```javascript
const obj1 = { a: 1, b: 2, c: 3};
const obj2 = { ...obj1, z: 5};
//obj2 = {a: 1, b: 2, c: 3, z: 5}
```

您可以使用**Spread运算符**来克隆对象**(obj2 = { ...obj1 });**

### 正则表达式命名为捕获组

**JavaScript**正则表达式可以返回匹配对象-一个包含匹配字符串的类数组。例如，要以YYYY-MM-DD格式解析日期：

``` javascript
const
	reDate = /([0-9]{4})-([0-9]{2}-([0-9]{2})/,
	match  = reDate.exec('2018-12-30'),
	year   = match[1],//2018
	month  = match[2],//12
	day    = match[3];//30
```

它很难阅读，并且更改正则表达式也可能会更改匹配对象索引。



**ES2018**允许在开始捕获括号``(``后立即使用符号**?\<name\>**，如下：

```javascript
const
	reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
	match  = reDate.exec('2018-12-30'),
	year   = match.groups.year, //2018
	month  = match.groups.month,//12
	day    = match.groups.day;  //30
```

任何为匹配的命名组都将其属性设置为**undefined**.



命名捕获组也可用于**replace()**方法中。例如将日期转换为美国MM-DD--YYYY格式：

``` javascript
const
	reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
	d = '2018-12-30',
	usDate = d.replace(reDate, '$<month>-$<day>-$<year>');
```

### 正则表达式反向断言(lookbehind)

目前**JavaScript**在正则表达式中支持**先行断言(lookahead)**。这意味着必须进行匹配但不捕获任何内容，并且断言不包含在整个匹配的字符串中，例如从价格中获取货币符号：

```javascript
const
	reLookahead = /\D(?=\d+)/,
	match = reLookahead.exec('$123.89');

console.log(match[0]);//$
```



**ES2018**引入以相同方式工作但是匹配前面的**反向断言(lookbehind)**，因此我们可以捕获价格并忽略货币字符：

```javascript
const
	reLookbehind = /(?<=\D)\d+/,
	match = reLookbehind('$123.89');

console.log(match[0]);//123.89
```

以上是一个**肯定反向断言**，非数字`\D`必须存在。同样的，还存在**否行反向断言**，表示一个值必须不存在，例如：

```javascript
const
  reLookbehindNeg = /(?<!\D)\d+/,
  match = reLookbehind.exec('$123.89');

console.log(match[0]);//null
```

### 正则表达式s*(dotAll)*标志

正则表达式点`.`匹配除回车外的任何单个字符。标志`s`改变这中行为，允许行终止符的出现，例如：

```javascript
/hello.world/.test('hello\nworld');  //false
/hello.world/s.test('hello\nworld'); //true
```



### 正则表达式*Unicode*属性转义

到目前为止，还无法在正则表达式中本机访问**Unicode**字符属性。**ES2018**添加了**Unicode**属性转义-在表单**\p{...}**和**\P{...}**-在正则表达式中使用标记`u`**(unicode)**设置，在`\p`块内，可以使用键值对的方式设置需要匹配的属性而非具体的内容。例如：

```javascript
const reGreekSymbol = /\p{Script=Greek}/u;
reGreekSymbol.test('π');//true
```

此特性可以避免使用特定的**Unicode**区间来进行内容类型判断，提升可读性和可维护性。

### 非转义序列的模板字符串

最后，**ES2018**删除了与模板字符串中转义序列相关的所有语法限制。

以前，一个`\u`开始一个**unicode**转义，`\x`开始一个十六进制转义，`\`后跟一个数字开始一个八进制转义。这使得无法创建某些字符串，如Window文件路径**c:\\uuu\\xxx\\111**。更多细节参考[模板字符串][template string]。

## Reference

[What's new in ES2018][ES9]

[What's new in ES2017][ES8]

[include]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes
[pow]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Exponentiation_%28**%29
[ES9]: https://link.juejin.im/?target=https%3A%2F%2Fwww.sitepoint.com%2Fes2018-whats-new%2F
[ES8]: https://link.juejin.im/?target=https%3A%2F%2Fwww.sitepoint.com%2Fes2017-whats-new%2F
[rest]: https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FFunctions%2FRest_parameters
[...]: https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FOperators%2FSpread_syntax
[template string]: https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2Ftemplate_strings