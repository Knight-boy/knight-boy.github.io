---
title: "Fetch函数"
date: 2018-10-06
image: /assets/img/blog/default.png
description: >
  Frame model! 
author: author2
comments: true
---

**Fetch API**提供了一个JavaScript接口，用于访问和操作HTTP管道的部分，例如请求和响应。它还提供了一个全局`fetch()`方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。—[Using-Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)

　　上面这种功能以前是使用`XMLHttpRequest`实现的。**Fetch**提供了一个更好的替代方案，可以比较容易地被其他技术使用，如`Sevice Work`。Fetch还提供了单个逻辑位置来定义其它HTTP相关概念，例如**CORS**和**HTTP**的扩展。

**注意：**`fetch`规范与`jquery.ajax()`主要有两种方式的不同，牢记：

- 当接收到一个代表错误的HTTP状态码时，从`fetch()`返回的**Promise**不会被标记为**reject**，即使该状态码是**404**和**500**。相反，它会将Promise状态标记为**resolve**(但会将resolve的返回值的`ok`属性设置为`false`)，仅当网络故障时或请求被阻止时，才会标记为`reject`。
- 默认情况下，`fetch`不会从服务端发送或接收任何Cookies，如果站点依赖于用户`session`，则会导致未经认证的请求(会发送cookies,必须设置`credentials`选项)。
{: .message}

### fetch请求

　　一个基本的`fetch`请求使用起来很简单，如下代码，请求某url处的`json`数据资源：

~~~js
fetch('http://example.com/data.json')
	.then(response => response.json())
	.then(myJson => console.log(myJson));
~~~

　　若使通过网络获取的JSON文件打印到控制台上。最简单的用法就是通过提供`fetch()`想获取资源的路径，然后通过返回包含相应结果的promise(一个`Response`对象)。

　　当然它只是一个Http响应，不是真正的JSON内容。为了获取Json文件中的内容，我们使用`json()`方法(通过`Request`和`Response`对象实现)。

**注意：**Body mixin还有其他相似的方法，用于获取其他类型的内容。参考[**#Body**](#body)
{: .message}

　　最好使用符合[内容安全策略(CSP)](https://developer.mozilla.org/zh-CN/docs/Web/Security/CSP/CSP_policy_directives)的*url*，而不是直接指向资源地址的方式进行**Fetch**请求。

### 请求参数

`fetch()`接受第二个可选参数，一个可以控制不同配置的`init`对象：

参考[`fetch()`](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch)，查看所有可选的配置和更多描述。

~~~js
postData('http://example.com/answer',{answer: 42})
	.then(data => console.log(data))
	.catch(error => console.error(error))
function postData(url,data) {
  //Default options are marked with *
  return fetch(url, {
    body: JSON.stringify(data),// must match 'Content-Type' header
    cache: 'no-cache',// *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin',// include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Sxample',
      'content-type': 'application/json'
    },
    method: 'POST',// *GET, POST, PUT, DELETE, etc.
    mode: 'cors',// no-cors, cors, *same-origin
    redirect: 'follow',// manual, *follow, error
    referrer: 'no-referrer',// *client, no-referrer
  })
  .then(response => response.json())
}
~~~

### 带凭据的请求

为了让浏览器发送包含凭据的请求(即使是跨域源)，要将`credentials:'include'`添加到传递给`fetch()`方法的`init`对象。

~~~js
fetch('https://example.com', {
  credentials: 'include'
})
~~~

如果你只想在请求URL与调用脚本位于同一源处时发送凭据，请将`credentials`设置成 `same-origin`。
如果确保浏览器不在请求中包含凭据，请将`credentials`设置为`omit`。

### 上传JSON数据

~~~js
var url = 'https://example.com/profile';
var data = {username: 'example'};
fetch(url, {
  method: 'POST',
  body: JSON.stringify(data),
  headers: new Headers({
    'Content-Type': 'application/json'
  })
}).then(res => res.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
~~~

### 上传文件

可以通过HTML`<input type="file" />`元素，`FormData()`和`fetch()`上传文件。

~~~js
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");
formData.append('username','abc123');
formData.append('avatar',fileField.files[0]);
fetch('https://example.com/profile/avator', {
  method:'PUT',
  body: formData
}).then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
~~~

### 上传多个文件

可以通过HTML`<input type="file" multiple>`元素，`FormData()`和`fetch()`上传文件

~~~js
var formData = new FormData();
var photos = document.querySelector("input[type='file'][multiple]");
formData.append('title','My Vacation');
formData.append('photos',photos.files);
fetch(url,{
  method: 'POST',
  body: formData
}).then(response => response.json())
.then(response => console.log('Success:', JSON.stringify(response)))
.catch(error => console.error('Error', error))
~~~

如果遇到网络故障，`fetch()`promise将会reject，并附加一个`TypeError`对象。虽然这个情况经常是遇到了权限问题或类似问题——404(并不是一个安全故障).若想要精确判断是否fetch成功，需要包含promise resolved的情况，此时再判断`Response.ok()`是否为`true`。如下：

~~~js
fetch('flowers.jpg', {
  if(response.ok) {
    return response.blob();
  }
  throw new Error('Network response was not ok.');
}).then(function(myBlob){
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
}).catch(function(error){
  console.log('There has been a problem with your fetch operation',error.message);
})
~~~

除了传递`fetch()`一个资源地址，还可以通过使用`Request()`构造函数来创建一个`request`对象，然后再作为参数传给`fetch()`：

~~~js
var myHeaders = new Headers();
var myInit = {
  method: 'GET',
  headers: myHeaders,
  mode: 'cors',
  cache: 'default'
};
var myRequest = new Request('flowers.jpg', myInit);
fetch(myRequest).then(function(response) {
  return response.blob();
}).then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
})
~~~

`Request()`和`fetch()`接受同样的参数，你甚至可以传入一个已经存在的`request`对象来创造一个拷贝：

~~~js
var anotherRequest = new Request(myRequest, myInit);
~~~

创建一个拷贝就可以再次使用`request/response`了，当然也可以使用不同的`init`参数。

**注意：**`clone()`方法也可以用于创建一个拷贝。它在语义上有一点不同于其他拷贝的方法。其他方法（比如拷贝一个 response）中，如果 request 的 body 已经被读取过，那么将执行失败，然而 clone() 则不会失败。
{: .message}