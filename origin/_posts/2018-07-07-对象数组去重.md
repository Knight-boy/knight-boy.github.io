---
title: "对象数组去重"
date: 2018-07-07
image: /assets/img/blog/arrayRep.jpg
description: >
  一种含有对象元素的数组去重方法！
author: author2
comments: true
---

在JavaScript中，数组是最常用也是最重要的数据类型，能够灵活掌握这种数据类型十分必要。

## **1. 使用Set**

​    **ES6** 提供了新的数据结构[**Set**](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)， 它类似数组，和**C++**中的[**set**](http://www.cplusplus.com/reference/set/set/?kw=set)容器一样，它成员的值都是唯一的，没有重复的值；**Set**本身是一个构造函数，用来生成**Set**数据结构。

```js
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;
const s = new Set();
[2,3,5,4,5,2,2].forEach(x => s.add(x));
for(let i of s) {
    console.log(i);
}
// 2 3 5 4
```

​    还有更简单的方式

```js
function dedupe(array) {
    return Array.from(new Set(array));
}
dedupe([1, 1, 2, 3]);//[1, 2, 3]
```

​    上面这种方式在于`Array.from`方法可以将Set结构转化为数组。如果你还觉得不过瘾，那么还有一种表示方式：将扩展运算符([...](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FOperators%2FSpread_syntax ))与[Set](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)结构相结合，就可以去除数组的重复成员；扩展运算符内部使用`for...of`循环。

```js
[...new Set(array)]
```

## **2. indexOf的方法**

​    这种方法新建一个存储数组，遍历参数数组，如果新数组中没找到该元素的索引，则把该元素`push`进新数组。

```javascript
function dedupe(array) {
    var newArr = [];
    for(var i=0; i<array.length; i++) {
        if(newArr.indexOf(array[i]) == -1) {
            newArr.push(array[i]);
        }
    }
    return newArr;
}
var arr = [1, 1, 2, 2, 3, 3];
console.log(dedupe(arr));//[1, 2, 3]
```

## **3. 比较数组下标**

​    我们知道，通过比较数组下标可以从数组中找到重复的数字，那么自然可以根据数组成员第一次时的索引值i进行判断，该方法类似`indexOf`的方法。

```javascript
function dedupe(array) {
    var newArr = [];
    for(var i=0; i<array.length; i++) {
        if(array.indexOf(array[i]) == i) {
            newArr.push(array[i]);
        }
    }
    return newArr;
}
var arr = [1, 1, 2, 2, 3, 3];
console.log(dedupe(arr));//[1, 2, 3]
```

## **4. 对象查找**

```javascript
function dedupe(arr) {
    var res = [];
    var o = Object.create(null);
    for(let v of arr) {
        var type = typeof v;
        if(!o[v]) {
            res.push(v);
            o[v] = [type];
        }else if(o[v].indexOf(type) == -1) {
            o[v].push(type);
             res.push(v);
        }
    }
    return res;
}
var arr = [1, 2, '2', 4, 7, 'a', 'a', 2, 3, 7, 6, 7];
console.log(dedupe(arr));//[1, 2, '2', 4, 7, 'a', 3, 6]
```

## **5. 排序相邻去重**

```javascript
function dedupe(arr) {
    arr.sort();
    var newArr = [arr[0]];
    for(var i=1; i<arr.length; i++) {
        if(arr[i] !== newArr[newArr.length-1]) {
             newArr.push(arr[i]);
        }
    }
    return newArr;
}
var arr = [1, 2, '2', 4, 7, 'a', 'a', 2, 3, 7, 6, 7];
console.log(dedupe(arr));//[1, "2", 2, 3, 4, 6, 7, "a"]
```

## **6. 数组实例的includes方法**

```javascript
function dedupe(arr) {
    var res=[];
    for(let v of arr) {
        if(!res.includes(v)) {
            res.push(v);
        }
    }
return res;
}
var arr = [1, 2, '2', 4, 7, 'a', 'a', 2, 3, 7, 6, 7];
console.log(dedupe(arr));//[1, 2, "2", 4, 7, "a", 3, 6]
```

## **7. 暴力去重**

```javascript
function dedupe(arr) {
    arr.sort();
    console.log(arr);
    for(var i=1; i<arr.length; i++) {
        if(arr[i]===arr[i-1]) {
            arr.splice(i, 1);
             i--;
        }

    }
    return arr;
}
var arr = [1, 2, '2', 4, 2, 7, 'a', 'a', 2, 3, 7, 6, 7, 7, 7];
console.log(dedupe(arr));
//[1, "2", 2, 2, 2, 3, 4, 6, 7, 7, 7, 7, 7, "a", "a"]
//[1, "2", 2, 3, 4, 6, 7, "a"]

var arr0 = [5, 1, 2, '2', 4, '2', 2, 5, 7, 'a', 'a', 2, 3, '7', 6, '7', 7, 7];
console.log(dedupe(arr));
//[1, 2, "2", "2", 2, 2, 3, 4, 5, 5, 6, 7, 7, "7", 7, "7", "a", "a"]
//[1, 2, "2", 2, 3, 4, 5, 6, 7, "7", 7, "7", "a"]
```

​    上面这种方式最好只用在数字数组或字符串数组去重，由于`sort()`排序方式的原因，再加上只比较相邻元素是否相等，当出现如`arr0`类似数组时，去重将会出现遗漏；因此最好对单一类型数组进行去重；
​    最近遇到一种比较复杂的情况：当数组中的元素包含对象元素时，对重复对象去重用一般的方法不能得到好的效果：

```javascript
const removeRepeatObj = (arr) => {
    for(let i=0,len = arr.length;i<len;i++){
        if(typeof arr[i]==='object' || Array.isArray(arr[i])){
            arr[i] = JSON.stringify(arr[i]);       
        }else{}
    }
    console.log(arr);  //test
    //基本类型的去重
    var res = [];
    var o = Object.create(null);
    for(let v of arr) {
        var type = typeof v;
        if(!o[v]) {
            res.push(v);
            o[v] = [type];
        }else if(o[v].indexOf(type) == -1) {
            o[v].push(type);
            res.push(v);
        }
    }
    //处理对象和数组（数组有点问题）
    console.log(res);//test
    for(let j=0;j<res.length;j++){
        if(typeof res[j] === 'string' && res[j].indexOf('{')!==-1){
            res[j] = JSON.parse(res[j]);
        }else if(typeof res[j] === 'string' && res[j].indexOf('[')!==-1){
            res[j] = res[j].substr(1).substring(0,res[j].length-2);;
            res[j] = res[j].split(',');
        }
    }
    return res;
}
removeRepeatObj([{a: '1'},{a: '1'},{a: '1', b: '2'},{a: '1',b: {c: '3'}},{a: '1',b: {c: '3'}},[1,2,3],[1,2,3],[4,5],['a','b'],1,2,3,3,5]);
//结果见下图
```

 

![img](/assets/img/blog/deleteRep1.png)

　　这种方法不能处理循环嵌套的数组元素，比如`[[1,2,3,[4,5,6]],{a: '1',b: {c: '3',d: {e:'4'}}}]`，对象类型的多重嵌套可以正常识别。

##  **一种更好的数组去重方法：**

```javascript
const removeRepeatObj = (arr) => {
    for(let i=0,len = arr.length;i<len;i++){
        if(typeof arr[i]==='object' && Array.isArray(arr[i])==0 && arr[i] !== null){
            arr[i] = JSON.stringify(arr[i]);       
        }else{}
    }
    console.log(arr);  //test
    //基本类型的去重
    var res = [];
    for(var i=0; i<arr.length; i++) {
        if(arr.indexOf(arr[i]) == i) {
            res.push(arr[i]);
        }
    }
    //处理对象
    console.log(res);//test
    for(let j=0;j<res.length;j++){
        if(typeof res[j] === 'string' && res[j].indexOf('{')!==-1){
            res[j] = JSON.parse(res[j]);
        }
    }
    return res;
}
removeRepeatObj([{a: '1'},{a: '1'},{a: '1', b: '2'},{a: '1',b: {c: '3',d: {e:'4'}}},{a: '1',b: {c: '3'}},[1,2,3,[5]],[1,2,3],[4,5],['a','b'],1,2,3,3,5,null,null,undefined,undefined]);
//结果如下：
```

![img](/assets/img/blog/deleteRep2.png)

## Reference：

<http://es6.ruanyifeng.com/#docs/set-map>

<http://es6.ruanyifeng.com/#docs/array>

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)