---
title: "JavaScript中的内存回收机制"
image: /assets/img/blog/recyling.jpg
description: >
  浏览器垃圾回收机制和C/C++中的垃圾回收机制有着天然的区别。垃圾能否及时回收关乎到程序运行的健壮性。
author: author2
comments: true
---

由于某些原因不再需要的内存没有被操作系统或则空闲内存池回收。编程语言中有多种管理内存的方式，这些方式从不同程度上会减少内存泄漏的几率，高级语言嵌入了一个名为垃圾收集器的软件，其工作是跟踪内存分配和使用，以便找到不再需要分配内存的时间，在这种情况下，它将自动释放它。然而，该过程是近似的，因为知道是否需要某些存储器的一般问题是不可判定的。

## 一、内存泄漏

### **1.1 循环引用导致的内存泄漏**

​       当两个对象相互引用时，会形成一个循环引用，使每个对象的引用计数为1，在纯粹的垃圾收集系统中，循环引用不是问题：如果任何其他对象都不引用所涉及的对象，则两者都是会被视为垃圾而回收。但是，在引用计数系统中，两个对象都不能被销毁，因为引用计数永远不会减到零。在使用垃圾回收和引用计数的混合系统中，由于系统无法识别循环引用而导致泄漏。在这种情况下，DOM对象和Javascript对象都不会被破坏。

```html
<script type="text/javascript">
    document.write("Circular referances between Javascript and DOM!");
    var obj;
    window.onload = function() {
        obj = document.getElementById("DivElement");
        document.getElementById("DivElement").expandoProperty = obj;
        Array(1000).join(new Array(2000).join("XXXXX"));
    }
</script>
<div id="DivElement">Div Element</div>
```

​       如上面代码所示，Javascript对象obj引用了DivElement表示的DOM对象。DOM对象反过来又通过expandoProperty对Javascript对象有一个引用。Javascript对象和DOM对象之间存在循环引用。因为DOM对象通过引用计数进行管理，所以两个对象都不会被销毁。

### **1.2 外部函数引起的循环引用**

​       下面代码中，通过调用外部函数myFunction来创建循环引用。Javascript对象和DOM对象之间的循环引用将最终导致内存泄漏。

```javascript
document.write("Circular references between Javascript and DOM!");
function myFunction(element) {
	this.elementReferences = element;
	//this code forms a circular references here
	//by DOM-->JS-->DOM
	element.expandoProperty = this;
}
function Leak() {
	//this code will leak;
	new myFunction(document.getElementById("myDiv"));
}
```

​       正如上面这两类代码示例所显示的，循环很容易创建。他们还倾向于在Javascript中最方便的编程结构：闭包。

### **1.3 闭包引起的内存泄漏**

​       Javascript的优点之一是它允许函数嵌套在其他函数之中，嵌套内部函数可以继承外部函数的参数和变量，并且对该函数是私有的。Javascript开发人员使用内部函数将小效用函数集成到其他函数中，使得内部函数（childFunction）可以访问外部parentFunction的变量。当一个内部函数获取并使用对其外部函数变量的访问时，它称为**闭包**。

​       一个简单的闭包例子

```javascript
document.write("Closure Demo!");
window.onload = function closureDemoParentFunction(paramA){
    var a = paramA;
    return function closureDemoInnerFunction(paramB){
    	alert(a + " " + paramB);
	};
};
var x=closureDemoParentFunction("outer x");
x("inner x");
```

​       在上面的代码中，closureDemoInnerFunction是父函数closureDemoParentFunction中定义的内部函数。当用外部x的参数对closureDemoParentFunction进行调用时，外部函数变量a被赋值外部x。函数返回一个指向内部函数closureDemoInnerFunction的指针，它包含在变量x中。必须注意的是，外部函数closureDemoParentFunction的局部变量a即使在外部函数返回后也会存在。这与C++等编程语言不同，在函数返回后，局部变量不再存在。在Javascript中，调用closureDemoParentFunction的时刻，创建一个具有属性a的作用域对象。此属性包含paramA的值，也称为"outer x"。同样，当closureDemoParentFunction 返回时，它将返回内部函数closureDemoInnerFunction，它包含在变量x中。

​       由于内部函数持有对外部函数的变量的引用，因此具有属性a的作用域对象不会被垃圾回收。当在x上用一个参数值（即x("inner x")进行调用时，将弹出一个显示"outer x inner x"的警报。闭包功能强大，因为它们允许内部函数在外部函数返回后保留对外部函数变量的访问权限。**遗憾的是，闭包在Javascript对象和DOM对象之间隐藏循环引用非常出色。**

​       由于IE9之前的版本对Javascript对象和COM对象使用不同的垃圾回收例程，因此闭包在这些版本中会导致一些特殊的问题。具体来说，如果闭包的作用域中保存着一个HTML元素，那么就意味着该元素将无法被销毁。

```javascript
function assignHandler() {
    var element = document.getElementById("my_btn");
    element.onclick = function() {
         alert(element.id);
     };
}
```

​       以上代码创建了一个作为element元素事件处理程序的闭包，而这个闭包又创建了一个循环引用。由于匿名函数保存了一个对`assignHandler()`的活动对象的引用，因此就会导致无法减少element的引用数。只要匿名函数存在，element的引用数至少也是1，因此它占用的内存永远也会被回收。不过，这个问题是可以被解决的：

```javascript
function assignHandler() {
    var element = document.getElementById("my_btn");
    var id = element.id;
    element.onclick = function() {
         alert(id);
     };
    element = null;
}
```

​       上面代码，是把element.id的一个副本保存在一个变量中，并且在闭包中引用该变量消除循环引用，但是，这种程度还不能解决内存泄露的问题。必须要记住：闭包会引用包含函数的整个活动对象，而这其中包含着element。即使闭包不直接引用element，包含函数的活动对象中也仍然会保存着一个引用。因此，必须要把element变量设置为null。这样就能解除对DOM对象的引用，顺利减少引用次数，确保回收其占用的内存。

### **1.4 事件处理程序引起的内存泄漏**

​      在下面的代码中，你将会发现，一个JavaScript对象（obj）包含对DOM对象（由id"元素"引用）的引用的闭包。DOM元素反过来又具有对Javascript obj的引用。在Javascript对象和DOM对象之间产生的循环引用会导致内存泄漏。

```html
<script type="text/javascript">
    document.write("Program to illustrate memory leak via closure");
    window.onload = function outerFunction() {
        var obj = document.getElementById("element");
        obj.onclick = function innerFunction() {
            alert("Hi!,I will leak");
        };
        obj.bigString = new Array(1000).join(new Array(2000).join("XXXXX"));
    };
</script>
<button id="element">Click Me</button>
```

### **1.5 避免内存泄漏的方法**

​       在Javascript中，内存泄露的另一方面是你可以避免它们。当您确定了可以导致循环引用的模式时，正如前面所列举的那样，您可以开始围绕它们进行工作。我们将使用上面三种的事件处理中内存泄漏的方式解决已知内存泄露的方法。一个简单的解决方案是使Javascript对象obj设为null，从而显式中断循环引用。

```html
<script type="text/javascript">
    document.write("Avoiding memory leak via closure by breaking the circular reference");
    window.onload=function outerFunction(){
        var obj = document.getElementById("element");
        obj.onclick=function innerFunction() {
            alert("Hi! I have avoided the leak");
            // 一些逻辑代码
        };
        obj.bigString=new Array(1000).join(new Array(2000).join("XXXXX"));
        obj = null; //显示中断循环引用
    };
</script>
<button id="element">"Click Here"</button>                    
```

​       另一种方法是通过添加一个闭包，可以避免Javascript对象和DOM对象之间的循环引用。

```html
<script type="text/javascript">
    document.write("Avoiding memory leak via closure by adding another closure");
    window.onload=function outerFunction(){
        var anotherObj=function innerFunction() {
            alert("Hi! I have avoided the leak");
            // 一些逻辑代码
        };
        (function anotherInnerFunction() {
            var obj = document.getElementById("element");
            obj.onclick = anotherObj;
        })();
</script>
<button id="element">"Click Here"</button>
```

​       第三种方法可以通过添加一个函数来避免闭包，从而防止泄漏。

```html
 <script type="text/javascript">
     document.write("Avoid leaks by avoiding closures!");
     window.onload=function()
     {
      var obj = document.getElementById("element");
      obj.onclick = doesNotLeak;
     }
     function doesNotLeak()
     {
          //Your Logic here
          alert("Hi! I have avoided the leak"); 
     }
 </script>
 <button id="element">"Click Here"</button>
```

## 二、垃圾回收

### **2.1 垃圾收集原理与意义**

​       在C和C++之类的语言中，开发人员的一项基本任务就是手动跟踪内存的使用情况，这是造成很多问题的根源。Javascript具有垃圾收集机制，也就是说，执行环境会负责管理代码执行过程中使用的内存。在编写JavaScript程序时，开发人员不再关心内存使用问题，所需内存的分配以及无用内存的回收完全实现了自动管理。这种垃圾收集机制的原理很简单：找出那些不再继续使用的变量，然后释放其占用的内存。为此，垃圾收集器会按照固定时间间隔（或代码执行中预定的收集时间），周期性地执行这一操作。

​        局部变量只在函数执行过程中存在。而在这个过程中，会为局部变量在栈（或堆）内存上分配相应的空间，以便存储它们的值。然后在函数中使用这些变量，直至函数执行结束。此时，局部变量就没有存在的必要了，因此可以释放他们的内存以供将来使用。在这种情况下，很容易判断变量是否还有存在的必要；但并非所有情况下就这么容易得出结论。垃圾收集器必须跟踪哪个变量有用哪个变量没用，对于不再有用的对象打上标记，以备将来回收其占用的内存。

### **2.2 垃圾收集策略**

​        现在各大浏览器通常采用的垃圾回收策略有两种：**标记清除**和**引用计数**。

#### **2.2.1 标记清除**

​        JavaScript最常用的垃圾收集方式就是**标记清除**(mark-and-sweep)。当变量进入环境时，就将该变量标记为"进入环境"。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入到相应的环境，就可能会用到它们。而当变量离开环境时，则将其标记为”离开环境“。

​        垃圾收集器在运行的时候会给存储在内存上的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的变量的标记。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后，垃圾收集器完成内存清除工作，销毁那些带标记的值并回收他们所占用的内存空间。

#### **2.2.2 引用计数**

​       这一种垃圾收集策略不太常用，原因在于很容易造成严重的问题：循环引用。引用计数的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将引用类型值赋给该变量时，则这个值得引用次数就是1。如果同一个值又被赋给另一个变量，则该值的引用次数加1。相反如果包含对这个值引用的变量又取到了另外一个值，则这个值的引用次数减1。当这个值的引用次数变成0时，则说明没有办法再访问这个值了，因而就可以将其占用的内存空间回收回来。这样当垃圾收集器下次再运行时，它就会释放那些引用次数为0的值所占用的内存。

​       引用计数所带来的严重问题是循环引用。循环引用指的是对象A中包含一个指向对象B的指针，而对象B中也包含一个指向对象A的指针；使得引用次数永远不会变成零，不能被垃圾收集器收集，释放其所占用的内存。

```javascript
function problem() {
    var objectA = new Object();
    var objectB = new Object();
    objectA.someOtherObject = objectB;
    objectB.anotherObject = objectA;
}
```

​       在上面的例子中，objectA和objectB通过各自的属性相互引用；也就是说，这两个对象的引用次数都是2。在采用标记清除策略的实现中，由于函数执行后，这两个对象都离开了作用域，因此这种相互引用不是个问题。但在采用引用计数策略的实现中，当函数执行完毕后，objectA和objectB还将继续存在，因为它们的引用次数永远不是0。假如这个函数被重复多次调用，就会导致大量的内存得不到回收。

​       在IE9之前版本中，有一部分对象并不是原生的Javascript对象。例如，其BOM和DOM中的对象就是使用C++以COM对象的形式实现的，而COM对象的垃圾收集机制采用的就是引用计数策略。因此，即使IE的Javascrip引擎是使用标记清除策略实现的，但Javascript访问的COM对象依然是基于引用计数策略的。

```javascript
var element = document.getElementById("some_element");
var myObj =new Object();
myObj.element = element;
element.someObject = myObj;
```

​      上面这个例子中，在一个DOM元素(element)与一个原生JavaScript对象(myObj)之间建立了循环引用。其中，变量myObj有一个名为element的属性指向element；而变量element有一个名为someObject的属性回指到myObj。由于循环引用，即使将例子中的DOM从页面中移除，内存也永远不会回收 。

​       为避免这类问题，最好在不使用它们的时候手工断开原生Javascript与DOM元素之间的连接：

```javascript
myObject.element = null;
element.someObject = null;
```

​       将变量设为null意味着切断变量与它之前引用的值之间的连接。当垃圾收集器下次运行的时候，就会删除这些值并回收它们所占用的内存。IE9+把BOM和DOM对象都转换成了真正的Javascript对象。这样就消除了常见的内存泄露现象。

### **2.3 管理内存**

####        **2.3.1 触发垃圾收集**

​        IE的垃圾收集是根据内存分配量进行的，具体就是256个变量、4096个对象（或数组）字面量和数组元素（slot）或则64KB的字符串。达到上述的任何一个临界值，垃圾收集器就会运行。这种实现方式的问题在于，如果一个脚本中包含那么多变量，那么该脚本很可能会在其生命周期中一直保存着那么多的变量。而这样一来，垃圾收集器就不得不频繁地运行，造成严重的性能问题。IE7之后，其Javascript引擎改变了其垃圾收集例程：触发垃圾收集的变量分配、字面量和数组元素的临界值被调整为动态修正。IE7的各项临界初始值和IE6相同，如果垃圾收集例程回收的内存分配量低于15%，这时候把临界条件（变量、字面量、数组元素）加倍，如果例程回收了85%的内存分配量，则将各种临界值重置回默认值。

####        **2.3.2 管理内存**

​       Javascript在进行内存管理时最主要的问题就是分配给Web浏览器的可用内存量通常要比分配给桌面应的少。这样做的目的是出于安全方面的考虑，目的是防止运行Javascript的网页耗尽全部系统内存而导致系统崩溃。内存限制问题不仅会影响给变量分配的内存，同时还会影响调用栈以及在一个线程中能够同时执行的语句数量。

​        因此，确保占有最少的内存可以让页面获得更好的性能。而优化内存占用的最佳方式，就是为执行中的代码只保留必要的数据。一旦数据不再有用，最好通过将其值设置为null来解除其引用。这一做法适用于大多数全局变量和全局对象的属性。局部变量会在它们离开执行环境时自动被解除引用。

```javascript
function createPerson(name) {
    var localPerson = new Object();
    localPerson.name = name;
    return localPerson;
}
var globalPerson = createPerson("Nicholas");
globalPerson = null;
```

​       在这个例子中，由于localPerson在createPerson()函数执行完毕后就离开了其执行环境，因此无需显示地为它解除引用。但对于全局变量globalPerson，则需要我们在不使用它的时候手工为它解除引用。不过，解除一个值得引用并不意味着自动回收该值所占用的内存。解除引用真正的作用是让值脱离执行环境，以便垃圾收集器下次运行时将其回收。

### **2.4 减少垃圾收集**

####        **2.4.1 对象object优化**       

​        为了最大限度的实现对象的重用，应该像避免使用new语句一样避免使用`{}`来新建对象。

　　`{"foo" : "bar"}`这种方式新建的带属性的对象，常常作为方法的返回值来使用，可是这将会导致过多的内存创建，因此最好的解决办法是：每一次函数调用完成之后，将需要返回的数据放入一个全局的对象中，并返回此全局对象。如果使用这种方式，就意味着每一次方法调用都会导致全局对象内容的修改，这有可能会导致错误的发生。因此，一定要对此全局对象的使用进行详细的注释和说明。

　　有一种方式能够保证对象（确保对象prototype上没有属性）的重复利用，那就是遍历此对象的所有属性，并逐个删除，最终将对象清理为一个空对象。

　　`cr.wipe(obj)`方法就是为此功能而生，代码如下：

```javascript
cr.wipe = function (obj) {
    for (var p in obj) {
         if (obj.hasOwnProperty(p))
            delete obj[p];
    }
};   
```

​        有些时候，你可以使用`cr.wipe(obj)`方法清理对象，再为obj添加新的属性，就可以达到重复利用对象的目的。虽然通过清空一个对象来获取“新对象”的做法，比简单的通过`{}`来创建对象要耗时一些，但是在实时性要求很高的代码中，这一点短暂的时间消耗，将会有效的减少垃圾堆积，并且最终避免垃圾回收暂停，这是非常值得的！

####         **2.4.2 数组优化**

​        将`[]`赋值给一个数组对象，是清空数组的捷径（例如： arr = [];），但是需要注意的是，这种方式又创建了一个新的空对象，并且将原来的数组对象变成了一小片内存垃圾！实际上，将数组长度赋值为0（arr.length = 0）也能达到清空数组的目的，并且同时能实现数组重用，减少内存垃圾的产生。

####         **2.4.3 function优化**

​        方法一般都是在初始化的时候创建，并且此后很少在运行时进行动态内存分配，这就使得导致内存垃圾产生的方法，找起来就不是那么容易了。但是从另一角度来说，这更便于我们寻找了，因为只要是动态创建方法的地方，就有可能产生内存垃圾。例如：将方法作为返回值，就是一个动态创建方法的实例。

```javascript
setTimeout(
    (function(self) {                    
      return function () {
      	self.tick();
    };
})(this), 100)
```

每过100毫秒调用一次this.tick()，看似没什么问题，但是仔细一琢磨，每一次调用都返回了一个新的方法对象，这就导致了大量的方法对象垃圾！

　　为了解决这个问题，可以将作为返回值的方法保存起来，例如：

```javascript
this.tickFunc = (
    function(self) {
      return function() {
      	self.tick();
      };
    }
)(this);
setTimeout(this.tickFunc, 100);
```



## Reference：

发布于博客园-[垃圾收集](https://www.cnblogs.com/princess-knight/p/9277830.html)、[内存泄漏](https://www.cnblogs.com/princess-knight/p/9280500.html)

<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Release_when_the_memory_is_not_needed_anymore>

<https://www.cnblogs.com/zhwl/p/4664604.html>

<http://www.ruanyifeng.com/blog/2017/04/memory-leak.html>