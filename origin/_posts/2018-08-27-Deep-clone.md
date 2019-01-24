---
title: 深拷贝与浅拷贝
date: 2018-08-27
image: /assets/img/blog/Data.png
description: >
  A few methods of achieving deep clone!
author: author2
comments: true
---

五种基本变量类型`Null`, `Undefined`, `Boolean`, `Number`, `String`,变量都是按值存放的，存放在栈内存中的简单数据段，可以直接访问。

### **JavaScript的变量类型**

对于引用类型，是存放在栈中的对象，变量保存的是一个指针，这个指针指向另一个位置。当需要访问引用类型（如对象，数组等）的值时，首先从栈中获取该对象的地址指针，然后再从堆内存中取得所需的数据。**JavaScript**存储对象都是存地址的，所以浅拷贝会导致`obj1`和`obj2`只想同一块内存地址。改变了其中一方的内容，都是在原来的内存上做修改会导致拷贝对象和原对象都发生改变，而深拷贝是开辟一块新的内存地址，将原对象的各个属性逐个复制进去。对拷贝对象和原对象各自的操作各不影响。

**eg1**:

```javascript
//数组拷贝
var arr1 = [1,2,3];
var arr2 = arr1;
arr1[0] = "e";
console.log(arr1);//"e,2,3"
console.log(arr2);//"e,2,3"
```

### **浅拷贝的实现**

#### 	1. 引用复制

```javascript
function shallowClone(copyObj) {
  var obj = {};
  for(var i in copyObj) {
    obj[i] = copyObj[i];
  }
}
var x = {
  a:1,
  b:{d:{e:5}},
  c:[1,2,3]
};
var y = shallowClone(x);
console.log(y.b.d===x.b.d);//true;
```

#### 　　2. Object.assign()

　　Object.assign方法用于对象的合并，并将源对象(source)的所有可枚举属性，复制到目标对象(target)。当目标对象和源对象有同名属性时，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

```javascript
const target = {a: 1，b: 1};
const source1 = {b: 2}；
const source2 = {c: 3};
Object.assign(target, source1, source2);
console.log(target) //{a: 1, b: 2, c: 3}
```

Object.assign()拷贝属性是有限制的，只拷贝原对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（enumerable: false)

```javascript
Object.assign({b: 'c'},
    Object.defineProperty({}, 'invisible', {
        enumerable: false,
        value: 'hello'
    })
);
//{b: 'c'}
```

`Object.assign`方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。

```javascript
const obj1 = {a: {b: 5}};
const obj2 = Object.assign({}, x);
obj1.a.b=2;
console.log(obj2.a.b) //2
```

上面代码中，源对象`obj1`的`a`属性的值是一个对象，`Object.assign`拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。

```javascript
//采用这种方法可以克隆它继承的值
function clone(origin){
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```

 

### **深拷贝的实现**

####  　　**1.Array的slice和concat方法**

**`Array`**的`slice`和`concat`方法不修改原数组，只会返回一个浅复制了原数组中的元素的一个新数组。之所以把它放在深拷贝里是因为它表现得像个深拷贝实际上是浅拷贝。原数组的元素会按照下述规则拷贝：

-  **如果该元素是个对象引用，slice会拷贝这个对象引用到新的数组中。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变。**
- **对于字符串、数字、布尔值来说，slice会拷贝这些值到新的数组中，在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。**

```javascript
var arr = [1, 2, 3];
var arr_shallow = arr;
var arr_concat = arr.concat();
var arr_slice = arr.slice(0);
console.log(arr===arr_shallow);//true;
console.log(arr==arr_slice);//false;
console.log(arr===arr_concat);//false;
```

可以看到，`concat`和`slice`返回不同的数组实例，这与直接引用复制是不同的。而从另一个例子可以看出**Array**的`concat`和`slice`并不是真正的深拷贝，数组中的对象元素`(Object, Array)`只是复制了引用。如下：

```javascript
var arr = [1, [1,2,3], {name: "knight"}];
var arr_concat = arr.concat();
var arr_slice = arr.slice(0);
arr_concat[1][0] = 5;//改变arr_concat中数组元素的值
console.log(arr[1]);//[5,2,3];
console.log(arr_slice[1]);//[5,2,3];
arr_slice[2].name = "knightboy";//改变arr_slice中对象元素的值
console.log(arr[2].name);//knightboy
console.log(arr_concat[2].name);//knightboy
```

#####  　  **递归实现对象的深拷贝**

```javascript
function deepCopy(obj) {
  var target = Array.isArray(obj) ? [] : {};
  for(var key in obj){
    if(typeof(obj[key]) == 'object' || Array.isArray(obj[key]))
      target[key] = deepCopy(obj[key]);
    else
      target[key] = obj[key];
  }
  return target;
}
```

#### 　　**2. Json对象的parse和stringify**

**JSON**对象`parse`方法可以将**JSON**字符串反序列化成**JS**对象，`stringify`方法可以将**JS**对象序列化成**JSON**字符串，借助这两个方法，可以实现对象的深拷贝。

```javascript
//eg1
var source = {name: "source", child: {name: "child"}};
var target = JSON.parse(JSON.stringify(source));
target.name = "target";//改变target的name属性
console.log(source.name); //source
console.log(target.name);//target
target.child.name = "target child";//改变target的child
console.log(source.child.name);//child
console.log(target.child.name);//target child

//eg2
var source = {name: function(){console.log(1);}, child:{name: "child"}};
var target = JSON.parse(JSON.stringify(source));
console.log(target.name);//undefined;

//eg3
var source = {name: function() {console.log(1);}, child:new RegExp("e")};
var target = JSON.parse(JSON.stringify(source));
console.log(target.name);//undefined
console.log(target.child);//Object {}
```

这种方法使用较为简单，可以满足基本的深拷贝需求，而且能够处理**JSON**格式能表示的所有数据类型，但是对于**正则表达式**类型、**函数类型**等无法进行深拷贝(而且会直接丢失相应的值)。还有一点不好的地方是它会抛弃对象的**constructor**。也就是深拷贝之后，不管这个对象原来的构造函数是什么，在深拷贝之后都会变成`Object`。同时如果对象中存在循环引用的情况也无法正确处理。

#### 　　**3. jQuery.extend()方法源码实现**

```javascript
/** 
 *$.extend([deep], clone, copy)
 *[options]用来缓存arguments[i]
 *[name]用来接收将要被扩展对象的key
 *[src]表示改变之前，target对象上每个key所对应的value，即target[name]
 *[copy]表示传入对象上每个key所对应的value, 即options[name]
 *[copyIsArray]判定copy是否为一个数组
 *[clone]深拷贝中用来临时存储对象或数组的src
 */
jQuery.extend = jQuery.fn.extend = function( ) {
  var options, name, src, copy, copyIsArray, clone,
      target = arguments[0] || {};
  i=1,
    length = arguments.length,
    deep = false;
  //Handle a deep copy situation
  if(typeof target === "boolean") {
    deep = target;
    //Skip the boolean and target
    target = arguments[i] || {};
    i++;
  }
  //Handle case when target is a string or something(possible in deep copy)
  if(typeof target!="object" && !isFunction(target)) {
    target = {};
  }
  //Extend jQuery itself if only one argument is passed
  if(i === length) {
    target = this;
    i--;
  }
  for(; i< length; i++) {

    //Only deal with non-null/undefined values
    if((options = arguments[i])!=null) {

      //Extend the base object
      for(name in options) {
        src = target[name];
        copy = options[name];

        //Prevent never-ending loop such as var i = {}; i.a = i; $.extend(true, {}, i);
        if(target === copy) {
          continue;
        }

        //Recurse if we're merging plain objects or arrays
        if(deep && copy && (jQuery.isPlainObject(copy) || (copyIsArray = Array.isArray(copy)))) {
          if(copyIsArray) {
            copyIsArray = false;
            clone = src && Array.isArray(src) ? src : [];
            else {
              clone = src && jQuery.isPlainObject(src) ? src : {};
            }
            //Never move original objects, clone them
            target[name] = jQuery.extend(deep, clone, copy);
            //Don't bring in undefined values
          } else if(copy !== undefined) {
            target[name] = copy;//if find name, cover it,or make it;
          }
        }
      }
    }
    //Return the modified object
    return target;
  };
```

这个方法到目前还是不能处理原对象内部的循环引用，

```javascript
var a = {name: "knight"},
    b = {name: "knightboy"};
a.child = b;
b.parent = a;
$.extend(true, {}, a);
//Uncaught RangeError: Maximum call stack size exceeded
```

　　**4. 自定义实现深拷贝**

```javascript
// 偏函数
//判断类型 boolean
var $ = (function() {
  var $={};
  var types = "Array Object String Date RegExp Function Boolean Number Null Undefined".split(" ");
  function type() {
    return Object.prototype.toString.call(this).slice(8, -1);
  }
  for(var i=types.length;i--;) {
    $['is' + types[i]] = (function(self) {
      return function(obj) {
        return type.call(obj) === self;
      };
    })(types[i]);
  }
  return $;
})();

function copy(obj, key, deep) {
  var index = 0;
  if (obj === null || typeof obj !== "object") {
    return obj;
  }
  var name, value, target = $.isArray(obj) ? [] : {};
  for (name in obj) {
    value = obj[name];
    if (value === obj) {
      continue;
    }
    if(index===0)
      value.copykey = 1;
    if (deep && ($.isArray(value) || $.isObject(value))) {
      if (!value.copykey && key!==name) {
        target[name] = copy(value, name, deep);
        delete target[name].copykey;
        delete value.copykey;
      } else {
        target[name] = value;
        delete target[name].copykey;
        delete value.copykey;
      }
    } else {
      target[name] = value;
    }
    index=1;
  }
  return target;
}
//嵌套
const obj1 = {
  x: {
    m: 1
  },
  y: undefined,
  z: function add(z1, z2) {
    return z1 + z2;
  },
  a: Symbol("foo")
};

const obj2 = copy(obj1,{},true);
obj2.x.m = 2;

//原型链
function SupType(){
  this.SupProperty = "knight";
}
function SubType(){
  this.SubProperty = 19;
}
SupType.prototype.getSupValue = function(){
  return this.SupProperty;
};
SubType.prototype = new SupType();
var instance = new SubType();
let origin = instance.getSupValue();

//循环引用test1
var a = {name: "knight"},
    b = {name: "knightboy"};
a.child = b;
b.parent = a;
//循环引用test2
function P(obj){
}
P.prototype.test = function(){console.log(1)};
var p =new RegExp('/[.]/');
var obj3,obj4;
//var obj5=new P();

obj3={a:1,b:2,c:3,d:{a:1,b:2,c:3,d:{a:1,b:2,c:3,d:{a:1,b:2,c:3,d:Date.now(),e:p}}}};
obj4={a:1,b:2,c:3,d:Date.now(),e:p};
obj3.a = obj4;
obj4.a = obj3;
obj3.f = obj4;
obj4.f = obj3;
var res =  copy(obj3,{},true);
console.log("源对象(copy):\n",obj3);
console.log("目标对象(clone):\n",res);
res.b=5;
console.log(res.b=obj3.b);
console.log("二次环引用测试():\n",obj4);
```

 

```javascript
//数据类型判断还有一种更加简洁的方法：
var Type = {};
for(var i=0,type;type=['String','Array','Number','Object','Boolean','Undefined','Null','Symbol','Date','RegExp','Function'][i++];){
  (function(type){
    Type['is'+ type] = function(obj){
      return Object.prototype.toString.call(obj) === '[object ' + type + ']';
    }
  })(type);
}
Type.isArray([]);//true
```

 