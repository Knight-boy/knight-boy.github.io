---
title: "浏览器中的跨域"
image: /assets/img/blog/crossdomain.jpg
description: >
  跨域问题一直是前端领域的重要内容，能够很好的解决跨域问题在前端开发实践中非常重要。
author: author2
comments: true
---
浏览器的同源策略能够很好的抵御非法访问，但有时开发者需要跨域访问一些资源，因而产生了许多能够实现跨域访问的一些策略。

### 一、同源与同源策略

​	我们知道，同源指的是协议、域名、端口号全部相同。同源策略(Same Origin Policy)是一种约定，它是浏览器最核心也是最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能都可能会受到影响。Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略是处于对用户安全的考量的，如果缺少了同源的限制，那又怎么能够确定别人的网站始终对你是友好的呢。针对非同源的情况制定了一些限制条件：

1. 无法读取不同源的`Cookie`、`LocalStorage`、`indexDB`。
2. 无法获得不同源的DOM。
3. 不能向不同源的服务器发送Ajax请求。

　　在浏览器中，`<script>`、`<img>`、`<iframe>`、`<link>`等标签都可以跨域加载资源，而且不受同源策略的限制。事实上，在大多数情境下，我们经常是需要借用非同源来提供数据的，所以这就要用到跨域方面的技术了。

### 二、JSONP

​	JSONP是指JSON Padding，JSONP是一种非官方跨域数据交换协议，由于`script`的src属性可以跨域请求，所以JSONP利用的就是浏览器的这个原理，需要通信时，动态插入一个javascript标签。请求的地址一般带有一个`callback`参数，假设需要请求的地址为<http://localhost:3000?callback=show>，服务器返回的代码一般是show()的JSON数据，而show函数恰恰是前端需要用的这个数据的函数。JSONP非常简单易用，自动补全API利用的就是JSONP。

​        一个简单的例子：

``` javascript
var script = doxument.createElement("script");
script.setAttribute("type", "text/javascript");
script.src="http://example.com/ip?callback=handleResponse";
document.body.appendChild(script);

function handleResponse(data) {
    console.log('Your public IP address is: '+data.ip);
}
```

​       JSONP解决跨域的本质：`<script>`标签可以请求不同域名下的资源，即`<script>`请求不受浏览器同源策略的影响。上例中的script会向<http://example.com/>服务器发送请求，这个请求的url后面带了个callback参数，是用来告诉服务器回调方法的方法名的。因为服务器收到请求后，会把相应的数据写进handleResponse的参数，也就是服务器会返回如下的脚本：

```javascript
handleResponse({
    "ip" : "8.8.8.8"
});
```

​       这样浏览器通过`<script>`下载的资源就是上面的脚本了，`<script>`下载完就会立即执行，也就是说<http://example.com/ip?callback=handleResponse>这个请求返回后就会立即执行上面的脚本代码，而这个脚本代码就是调用回调方法和拿到Json数据了。

​	我们再来看一个例子：

``` javascript
//请求代码
function jsonp(callback) {
    var script = document.createElement("script");
        url = `https://localhost:3000?callback=${callback}`;
    script.setAttribute("src", url);
    document.querySelector("head").appendChild(script);
}
function show(data) {
    concole.log(`学生姓名为: ${data.name}，年龄为: ${data.age}，性别为: ${data.sex}`);
}
jsonp("show");
```

``` javascript
//响应代码
const student = {
    name: "Knight",
    age: 19,
    sex: "male"
};
var callback = url.parse(req.url, true).query.callback;
res.writeHead(200,{
    "Content-Type": "application/json;charset=utf-8"
});
res.end(`${callback}(${JSON.stringify(student)})`);
```

**JSONP**有一个很大问题，就是只能进行GET请求。
{: .notice}

### 三、跨域源资源共享(CORS)

　　CORS是W3C制定的跨站资源分享标准，可以让AJAX实现跨域访问，定义了在必须访问跨域资源时浏览器与服务器该如何沟通。CORS背后的基本思想，就是使用自定义的HTTP头部让浏览器和服务器进行沟通，从而决定请求或响应应该成功还是失败。

​	比如一个简单的使用GET或POST的请求，它没有自定义的头部，而主体内容是text/plain。在发送该请求时，需要给它附加一个额外的Origin头部，其中包含请求页面的源信息（协议、域名、端口号），以便服务器根据该头部信息来决定是否给予响应。

```php
Origin: http://www.example.com
```

　　如果服务器认为这个请求可以接受，就在Access-Control-Allow-Origin头部中发回相同的源信息（如果是公共资源，可以发“*”）。例如：

```php
Access-Control-Allow-Origin: http://www.example.com
```

　　如果没有这个头部信息或信息不匹配，浏览器就会驳回请求。正常情况下，浏览器会处理请求。此时，请求和响应都不包含Cookie信息。

　　**简单请求**的跨域：

　　请求方式为GET或则POST；

　　假若请求是POST的话，Content-Type必须为下列之一：

> - application/x-www-form-urlencoded
> - mutipart/form-data
> - text/plain

　　不含有自定义头；

 对于简单的跨域只进行一次http请求：

```javascript
function ajaxPost(url, obj, header) {
    return new Promise((resolve, reject) => {
         var xhr=new XMLHttpRequest(),
              str = '' ;
              keys = Object.keys(obj);
         for(var i=0,len=keys.length;i<len;i++) {
             str +=`${keys[i]}=${obj[keys[i]]}&`;
         }
         str = str.substring(0, str.length - 1);
         xhr.open('post', url);
         xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
         if(header instanceof Object) {
             for(var k in header)
                  xhr.setRequestHeader(k, header[k]);
         }
         xhr.send(str);
         xhr.onreadystatechange = function() {
              if(xhr.readyState == 4) {
                  if(xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                      resolve(xhr.responseText);
                  } else {
                      reject();
                  }
              }
         }
         
    });
}
ajaxPost("https://localhost:3000?page=cors", {
        name: "Knight",
        age: 19,
        sex: "male"
}).then((text) => {console.log(text);},
                ()=>{console.log("请求失败");});
//后端处理
var postData = "";
req.on("data", (data) => {
    postData += data;
});
req.on("end", () => {
    postData = querystring.parse(postData);
    res.writeHead(200, {
        "Access-Control-Allow-Origin": "*",
        "Content-Type": "application/json;charset=utf-8"
    });
    if(postData.name === student.name 
       && Number(postData.age) === student.age 
       && postData.sex === student.sex) {
        res.end(`yeah! ${postData.name} is a good guy!`);
    } else {
        res.end("No! a bad guy!");
    }
});
```

对于非简单请求来说，需要两次http请求，其中在请求之前有一次预检请求。

```javascript
function ajaxPost(url, obj, header) {
    return new Promise((resolve, reject) => {
         var xhr=new XMLHttpRequest(),
              str = '' ;
              keys = Object.keys(obj);
         for(var i=0,len=keys.length;i<len;i++) {
             str +=`${keys[i]}=${obj[keys[i]]}&`;
         }
         str = str.substring(0, str.length - 1);
         xhr.open('post', url);
         xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
         if(header instanceof Object) {
             for(var k in header)
                  xhr.setRequestHeader(k, header[k]);
         }
         xhr.send(str);
         xhr.onreadystatechange = function() {
              if(xhr.readyState == 4) {
                  if(xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                      resolve(xhr.responseText);
                  } else {
                      reject();
                  }
              }
         }
         
    });
}
ajaxPost("https://localhost:3000?page=cors", {
        name: "Knight",
        age: 19,
        sex: "male"
}, {"X-author": "Knight"}).then((text) => {console.log(text);},
                ()=>{console.log("Request Error!");});
//后端处理
var postData = "";
if(req.method == "OPTIONS") {
    res.writeHead(200, {
        "Access-Control-Max-Age": 3000,
        "Access-Control-Allow-Headers": "X-author",
        "Access-Control-Allow-Origin": "*",
        "Content-Type": "application/json;charset=utf-8"
    });
    res.end();
    return void 0;
}
req.on("data", (data) => {
    postData += data;
});
req.on("end", () => {
    postData = querystring.parse(postData);
    res.writeHead(200, {
        "Access-Control-Allow-Origin": "*",
        "Content-Type": "application/json;charset=utf-8"
    });
    if(postData.name === student.name 
       && Number(postData.age) === student.age 
       &&  postData.sex === student.sex) {
        res.end(`yeah! ${postData.name} is a good guy!`);
    } else {
        res.end("No! a bad guy!");
    }
});
```

　　上面代码中，两个响应头: `Access-Control-Allow-Headers`，用来指明在实际的请求中，可以使用那些自定义的http请求头；`Access-Control-Max-Age`，用来指定此次预请求的结果的有效期，在有效期内则不会发出预请求，类似于缓存。

### **四、document.domain实现跨域**

　　可以将子域和主域的`document.domain`设为同一个主域来实现跨域。但前提条件是，这两个域名必须属于同一个基础域名，所用的协议，端口都要一致，否则无法通过`document.domain()`来进行跨域。

​	**example 1:**

​	如果想要在你的http://www.knightboy.cn/a.html页面里使用`<iframe>`调用另一个http://knightboy.cn/b.html页面。这时候你想在a页面里面获取b页面里的DOM，然后进行操作。然后你会发现你不能获得b的DOM。document.getEle

mentById("myIFrame").contentWindow.document或window.parent.document.body因为两个窗口不同源而报错。

​	这时候你只需要在a页面里和b页面里把document.domian设置成相同的值就可以在两个页面里操作Dom了。

​	**example 2:**

​	如果你在http://www.knightboy.cn/a.html页面里写入了document.cookie = "key=hello world"；你在http://knightboy.cn/b.html页面是拿不到这个cookie的。

​	原因在于，Cookie是服务器写入浏览器的一小段信息，只有同源的网页才能共享。但是，两个网页一级域名相同，二级域名不同，浏览器允许通过设置`document.domain`来共享Cookie。另外，服务器也可以在设置Cookie的时候，指定Cookie的所属域名为一级域名。这样的话，二级域名和三级域名不用做任何设置便可以读取这个Cookie。

​	有一点需要注意的是：document.domain虽然可以读写，但只能设置成自身或者是高一级的父域且主域必须相同。所以**只能解决一级域名相同二级域名不同的跨域问题。**还有就是**document.domain只适用于Cookie和iframe窗口，LocalStorage和IndexDB无法通过这种方法跨域。**

### **五、window.name跨域**

​	window对象有一个name属性，该属性有个特征：即在一个窗口(window)的生命周期内，窗口载入的所有页面都是共享一个window.name的，每个页面对window.name都有读写的权限，window.name是持久存在一个窗口载入过的所有页面中的，并不会因新页面的载入而进行重置。注意，window.name的值只能是字符串的形式，且这个字符串的大小最大能容许2M左右甚至更大的容量，因浏览器而异，但一般是够用的。

​	**example 1:**

　　现在在一个浏览器的一个标签页里打开http://www.knightboy.cn/a.html页面，你通过location.href = http://baidu.com/b.html，在同一个浏览器标签页里打开了不同域名下的页面。这时候这两个页面你可以使用window.name来传递参数。因为window.name指的是浏览器窗口的名字，只要浏览器窗口相同，那么无论在哪个页面里访问都是一样的。

​	**example 2:**

　　你的http://www.knightboy.cn/a.html页面里使用`<iframe>`调用另一个http://baidu.com/b.html页面。这时候你想在a页面里获取b页面里的DOM，然后进行操作。结果会发现不能获得b中的DOM。同样会因为不同源而报错，和上面提到的不同之处就是两个页面的一级域名也不相同。这时候`document.domain`就解决不了了。

​	浏览器窗口有window.name属性。这个属性的最大特点就是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。比如当在b页面里设定`window.name="hello"`，你再返回到a页面，在a页面访问window.name，可以得到hello。这种方法的优点是，window.name容量很大，可以放置非常长的字符串；缺点是必须监听子窗口window.name属性的变化，影响网页性能。

```html
<!--a.html-->
<DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>跨域</title>
    <script>
        function getData() {//iframe载入后执行该函数
            var iframe = document.getElementById("proxy");
            iframe.onload=function() {//a.html与iframe属于同源了,可以互相访问
                var data = iframe.contentWindow.name;
                //获取iframe里的window.name，也就是b.html页面给它设置的数据
                alert(data);
        }
         iframe.src="data.html";//这里的data.html为随便的一个页面，
            					//目的是，使得a.html能访问到iframe里的内容，
            					//也可设置成about:blank
    </script>    
<head>
<body>
    <iframe id="proxy" src="http://baidu.com/b.html" 
            style="display:none" onload="getData()">
    </iframe>
</body>
</html>
```

```  html
<!--b.html-->
<script>
    window.name="this is some data you got from b.html";
</script>
```

### **六、window.postMessage方法跨域**

 　　window.postMessage是一个安全的跨源通信方法。一般情况下，当且仅当执行脚本的页面使用相同的协议（通常都是http）、相同的端口（http默认80，https默认443）和相同的host(两个页面的document.domain的值相同)时，才允许不同页面上的脚本互相访问。`window.postMessage`提供了一个可控的机制来安全地绕过这一限制，当其在正确使用的情况下。`window.postMessage`解决的不是浏览器与服务器之间的交互，解决的是浏览器不同窗口之间的通信问题，可以做的就是同步两个网页，当然这两个网页需要属于同一个基础域名。

　　**example 1:**

　　在a页面打开了一个不同源的b页面，你想要让a和b这两个页面互相通信，例如，a要访问b的`LocalStorage`。又或者，a页面里的iframe的`src`是不同源的b页面，你想要让a和b两个页面互相通信，比如依旧是想通过a访问b的`LocalStorage`。

　　此时的解决办法是：利用HTML5中新增的**跨文档通信API**，这个API为`window`对象新增了一个`window.postMessage`方法，允许跨窗口通信，不论这两个窗口是否同源。a就可以把它的Local Storage发给b，反之，依然可行。

　　**window.postMessage(message, targetOrigin, [transfer])**三个参数分别表示为：

- `message`是向目标窗口发送的数据；
- `targetOrigin`属性来指定哪些窗口能收到消息事件，其值可以是字符串“*”（表示无限制）或者一个URI（或者说是发送消息的目标域名）；
- `transfer`可选参数，是一串和`message`同时传递的`Transferable`对象，这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。

　　另外就是，消息的接收方必须有监听事件，否则发送消息时就会报错。

　　**window.addEventListener("message", onmessage);** `onmessage`接收到的`message`事件包含三个属性：

　　data: 从其他`window`中传递过来的数据。

　　origin: 调用`postMessage`时消息发送窗口的`origin`。这个`origin`不能保证是该窗口的当前或未来`origin`，因为`postMessage`被调用后可能被导航到不同的位置。

　　source: 对发送消息的窗口对象的引用；您可以使用此来在具有不同origin的两个窗口间建立双向通信。

```javascript
//在a页面执行
var popUp = window.open('http://localhost:3000', 'title');
popUp.postMessage('Hello World!', 'http://localhost:3000');
```

　　同时在http://localhost:3000的页面里监听message事件：

```javascript
window.onload = function() {
    window.addEventListener('message', onmessage);
}
function onmessage(event) {
    if(event.origin == 'http://localhost:8080') {//"发送方a的域名"
        console.log(event.data);//"Hello World!"
    } else {
		console.log(event.data);//"Hello World!"
    }
}
```

我们来看另外一个例子：

``` javascript
//发送端代码
var domain = "https://localhost",
    index = 1,
    target = window.open(`${domain}/postmesssage-target.html`);
function send()  {
    setInterval(() => {
        target.postMessage(`第${index++}次数据发送`, domain);
    }, 1000);
}
window.addEventListener("message", (e)=>{
    if(e.data === 'ok')
        send();
    else
        console.log(e.data);
});
```

``` html
//接收端代码
<head>
    <script>
        opener.postMessage("ok", opener.domain);
    </script>
</head>
<body>
<p id = "test"></p>
<script>
    var test = document.querySelector("#test");
    window.addEventListener("message", (e)=>{
        if(e.origin !== "http://localhost") {
            return void 0;
        }
        test.innerText = e.data;
    });
</script>
</body>
```

　　上面页面中，接受页面已经加载了，这时发送一个消息给发送端，发送端再开始向接收端发送数据。

### **七、片段识别符实现跨域**

　　片段识别符就是指URL的#号后面的部分。比如，http://example.com/x.html#fragment的`#fragment`。如果只是改变片段标识符，页面不会重复刷新。父窗口和iframe的子窗口之间的通讯或者是`window.open`打开的子窗口之间的通讯。

　　父窗口可以把信息，写入子窗口的片段标识符。

``` javascript
var src= originURL + '#' + data;
document.getElementById('myIFrame').src = src;
```

　　子窗口通过监听`hashchange`事件得到通知。

```javascript
window.onhashchange = checkMessage;
function checkMessage() {
    var message = window.location.hash;
    //...  
}
```

　　同样，子窗口也可以改变父窗口的片段标识符。

``` javascript
parent.location.href = target + '#' + hash;
```

　　总之，父窗口改变子窗口的url的`#`号后面的部分，后者把要传递的的参数写在#后面，子窗口监听`window.onhashchange`事件，得到通知，读取`window.location.hash`解析出有用的数据。同时子窗口也可以向父窗口传递数据。

### Reference

##### 首次发布于[博客园](https://www.cnblogs.com/princess-knight/p/9311197.html)

[跨域同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

[跨域解决方案大全](https://www.2cto.com/kf/201610/559565.html)

[跨域问题汇总](https://segmentfault.com/a/1190000012158485)