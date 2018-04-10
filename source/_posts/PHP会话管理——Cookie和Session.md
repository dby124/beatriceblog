---
title: PHP会话管理——Session和Cookie
date: 2018-04-10 10:18:38
tags: [cookie&session]
categories: [PHP]
---

会话管理包括：Session和Cookie，主要功能都是把客户户端和服务器关联起来，用于管理和查看用户在网站中的状态。

## 背景

Web应用程序是使用HTTP协议传输数据的。**HTTP协议是无状态的协议**。一旦数据交换完毕，客户端与服务器端的连接就会关闭，再次交换数据需要建立新的连接。这就意味着服务器无法从连接上跟踪会话。即用户A购买了一件商品放入购物车内，当再次购买商品时服务器已经无法判断该购买行为是属于用户A的会话还是用户B的会话了。要跟踪该会话，必须引入一种机制。

Cookie就是这样的一种机制。它可以弥补HTTP协议无状态的不足。在Session出现之前，基本上所有的网站都采用Cookie来跟踪会话。
> 可以把Cookies看成为http协议的一个扩展

## Cookie和Session对比


特性 | Cookie | Session
---|---|---
定位 | cookie 是由服务器发送到浏览器的变量 | Session用来追踪每个用户的会话
调用语句 | setcookie() ，必须在任何输出前调用,位于 <html> 标签之前 | session_start()，必须在任何输出前调用,位于 <html> 标签之前
存放位置 | 基于客户端浏览器，存放在内存/硬盘 | 服务端内存
存储大小 | 单个cookie小于3K | 无限制（过大会导致内存溢出）
生命周期 | 浏览器会话期间/设定时间 | 超时时间（未访问过服务器的最长时间）
依赖性 | 依赖于浏览器 | 依赖于Cookie
安全性 | 不安全 | 较安全

### Cookie
Cookie意为“甜饼”，是由W3C组织提出，最早由Netscape社区发展的一种机制。目前Cookie已经成为标准，所有的主流浏览器如IE、Netscape、Firefox、Opera等都支持Cookie。
> 注意：Cookie功能需要浏览器的支持。如果浏览器不支持Cookie（如大部分手机中的浏览器）或者把Cookie禁用了，Cookie功能就会失效。不同的浏览器采用不同的方式保存Cookie。

**Cookie的工作原理**：由于HTTP是一种无状态的协议，服务器单从网络连接上无从知道客户身份。怎么办呢？就给客户端们颁发一个通行证吧，每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了。


**Set-Cookie 和 Cookie**：两个专门负责设置以及发送cookie的++http头部++。

**Cookie工作流程**：

Cookie实际上是一小段的文本信息。（1）整个客户端请求服务器，（2）如果服务器需要记录该用户状态，则通过服务器返回给客户端一个包含Set-Cookie这个头部的++http响应++来指示客户端建立一个cookie。客户端浏览器会把Cookie保存起来，直到这个cookie过期。（3）当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。（4）服务器检查该Cookie，以此来辨认用户状态，然后响应请求。注意：服务器还可以根据需要修改Cookie的内容。一个cookie的设置以及发送过程分为以下四步：

![http_cookie](/images/http_cookie.png)

> setcookie() 定义一个和其余的 HTTP 标头一起发送的 cookie。和其它标头一样，++cookie 必须在脚本的任何其它输出之前发送++（这是协议限制）。这需要将本函数的调用放到任何输出之前，包括 &amp;lt;html&amp;gt; 和 &amp;lt;head&amp;gt; 标签以及任何空格。如果在调用 setcookie() 之前有任何输出，本函数将失败并返回 FALSE。++因此必须在发送任何HTML(必须位于 <html> 标签之前)、文本信息前调用session_start() 函数++。 


- **Cookie的限制：**
    - 浏览器记录Cookie的容量不能大于4KB；
    - 每个浏览器只能保存某个服务器上的20个Cookie。如果保存更多，则最先保存的就会被删除；
    - 每个用户的浏览器最多只能访问300个Cookie。


- **创建Cookie**：setcookie(变量名，变量值，过期时间，在服务器上的有效路径，boolean是否通过安全的HTTPS链接传送);
    - Cookie不会在设置本页生效，在测试时，要在Cookie过期之前，通过另外一个页面来访问。
    - 工作时间：
        - 不设置过期时间：重新打开浏览器则失效；表示这个cookie生命周期为浏览器会话期间，只要关闭浏览器窗口，cookie就消失了。这种生命期为浏览会话期的cookie被称为++会话cookie++。
        - time（）+ n秒数：设定过n秒后Cookie失效；
        - mktime（时，分，秒，月，日，年），设定为到规定时间失效；
        > ++会话cookie++一般不保存在硬盘上而是保存在**内存**里。如果设置了过期时间，浏览器就会把cookie保存到**硬盘**上，关闭后再次打开浏览器，这些cookie依然有效直到超过设定的过期时间。


- **Cookie的有效期**：Cookie的maxAge决定着Cookie的有效期，单位为秒（Second）。Cookie中通过getMaxAge()方法与setMaxAge(int maxAge)方法来读写maxAge属性。

    -  maxAge属性为正数，则表示该Cookie会在maxAge秒之后自动失效。浏览器会将maxAge为正数的Cookie持久化，即写到对应的Cookie文件中。无论客户关闭了浏览器还是电脑，只要还在maxAge秒之前，登录网站时该Cookie仍然有效。
    - maxAge为负数的Cookie，为临时性Cookie，称为会话Cookie，不会被持久化，不会被写到Cookie文件中。
    - maxAge为0，则表示删除该Cookie。Cookie机制没有提供删除Cookie的方法，因此通过设置该Cookie即时失效实现删除Cookie的效果。失效的Cookie会被浏览器从Cookie文件或者内存中删除。

    ```
    //设置cookie过期时间
    Cookie cookie = new Cookie("username","helloweenvsfei"); // 新建Cookie
    cookie.setMaxAge(Integer.MAX_VALUE); // 设置生命周期为MAX_VALUE
    response.addCookie(cookie); // 输出到客户端
    //删除cookie
    Cookie cookie = new Cookie("username","helloweenvsfei"); // 新建Cookie
    cookie.setMaxAge(0); // 设置生命周期为0，不能为负数
    response.addCookie(cookie); // 必须执行这一句
    ```
    > 注意：从客户端读取Cookie时，包括maxAge在内的其他属性都是不可读的，也不会被提交。浏览器提交Cookie时只会提交name与value属性。maxAge属性只被浏览器用来判断Cookie是否过期。

- **Cookie数组**：将cookie各个变量联系起来，组成数组，利用$_COOKIE[’cookie‘]输出。Cookie对象使用key-value属性对的形式保存用户状态，一个Cookie对象保存一个属性对，一个request或者response同时使用多个Cookie。

- **提高Cookie安全性**的方法有：
    - ++设置HttpOnly为true++。该属性值的作用就是防止Cookie值被页面脚本读取。
    > 但是设置HttpOnly属性，HttpOnly属性只是增加了攻击者的难度，Cookie盗窃的威胁并没有彻底消除，因为cookie还是有可能传递的过程中被监听捕获后信息泄漏。

    - ++设置Secure为true++。给Cookie设置该属性时，只有在https协议下访问的时候，浏览器才会发送该Cookie。
    > 把cookie设置为secure，只保证cookie与WEB服务器之间的数据传输过程加密，而保存在本地的cookie文件并不加密。如果想让本地cookie也加密，得自己加密数据。

    - ++给Cookie设置有效期++。

特性：
- **Cookie不提供修改、删除操作**。如果要修改某个Cookie，只需要新建一个同名的Cookie，添加到response中覆盖原来的Cookie。如果要删除某个Cookie，只需要新建一个同名的Cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie。
> 注意：修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，例如name、path、domain等，都要与原Cookie完全一样。否则，浏览器将视为两个不同的Cookie不予覆盖，导致修改、删除失败。
- **Cookie具有不可跨域名性**。根据Cookie规范，浏览器依据域名判断一个网站是否能操作另一个网站Cookie的（域名相同才可以操作）。同一个一级域名下的两个二级域名也不能交互使用，只有严格相同的域名可以交互使用。
> 如：浏览器访问Google只会携带Google的Cookie，而不会携带Baidu的Cookie。Google也只能操作Google的Cookie，而不能操作Baidu的Cookie。Cookie在客户端是由浏览器来管理的。浏览器能够保证Google只会操作Google的Cookie而不会操作Baidu的Cookie，从而保证用户的隐私安全。
- **Unicode编码**：保存中文。中文与英文字符不同，中文属于Unicode字符，在内存中占4个字符，而英文属于ASCII字符，内存中只占2个字节。Cookie中使用Unicode字符时需要对Unicode字符进行编码，否则会乱码。
> 提示：Cookie中保存中文只能编码。一般使用UTF-8编码即可。不推荐使用GBK等中文编码，因为浏览器不一定支持，而且JavaScript也不支持GBK编码。
- **BASE64编码**：保存二进制图片.Cookie不仅可以使用ASCII字符与Unicode字符，还可以使用二进制数据。例如在Cookie中使用数字证书，提供安全度。使用二进制数据时也需要进行编码。
> 注意：本程序仅用于展示Cookie中可以存储二进制内容，并不实用。由于浏览器每次请求服务器都会携带Cookie，因此Cookie内容不宜过多，否则影响速度。Cookie的内容应该少而精。


除了cookie，客户端传递数据到服务器端的方式还包括**POST**（数据包含在http请求的内容区域内）和**GET**（数据包含在请求的url中）。这两种传递数据的方式，比起用cookies来传递数据更稳定，因为cookie可能被禁用，但是以GET以及POST方式传递数据时，不存在这种情况。

### Session
Session是服务器端使用的一种记录客户端状态的机制，使用上比Cookie简单一些，相应的也增加了服务器的存储压力。

Session技术则是服务端的解决方案，它是通过服务器来保持状态的。由于Session这个词汇包含的语义很多，因此需要在这里明确一下。 
- Session的含义
    - 把客户端浏览器与服务器之间一系列交互的动作称为一个Session。涉及到Session持续的时间和操作等等；
    - Session指的是服务器端为客户端所开辟的存储空间，在其中保存的信息就是用于保持状态。涉及Session中存放的内容及匹配的内容方法等。
 
- Session使用流程：
    - 创建Session。在服务器端程序运行的过程中创建Session，同时，服务器为该Session生成唯一的Session id，这个Session id在随后的请求中会被用来匹配已经创建的Session；
    - 在Session被创建之后，就可以调用Session相关的方法往Session中增加内容了，而这些内容只会保存在服务器中，发到客户端的只有Session id；
    - 当客户端再次发送请求的时候，会将这个Session id带上，服务器接受到请求之后就会依据Session id找到相应的Session，从而再次使用之。
> 正是这样一个过程，用户的状态也就得以保持了。


**Session里的信息应该尽量精简**。因为Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session。如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。

Session在用户第一次访问服务器的时候自动创建。需要注意只有访问JSP、Servlet等程序时（向服务器发送请求时）才会创建Session，只访问HTML、IMAGE等静态资源并不会创建Session。Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃（active）”了一次。

**Session的有效期**

由于会有越来越多的用户访问服务器，因此Session也会越来越多。为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。

Session的超时时间为maxInactiveInterval属性，可以通过对应的getMaxInactiveInterval()获取，通过setMaxInactiveInterval(longinterval)修改。

Session的超时时间也可以在web.xml中修改。另外，通过调用Session的invalidate()方法可以使Session失效。

**Session对浏览器的要求**

Session保存在服务器，对客户端是透明的。由于++Session需要使用Cookie作为识别标志++，它的正常运行仍然需要客户端浏览器的支持。Session依据Cookie来识别是否为同一用户。该Cookie为服务器自动生成的，它的maxAge属性一般为–1，表示仅当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效。

> 注意：新开的浏览器窗口会生成新的Session，但子窗口除外。子窗口会共用父窗口的Session。例如，在链接上右击，在弹出的快捷菜单中选择“在新窗口中打开”时，子窗口便可以访问父窗口的Session。

**URL地址重写**

如果客户端浏览器将Cookie功能禁用，或者不支持Cookie怎么办？例如，绝大多数的手机浏览器都不支持Cookie。Java Web提供了另一种解决方案：URL地址重写。

URL地址重写是对客户端不支持Cookie的解决方案。URL地址重写的原理是将该用户Session的id信息重写到URL地址中。服务器能够解析重写后的URL获取Session的id。这样即使客户端不支持Cookie，也可以使用Session来记录用户状态。HttpServletResponse类提供了encodeURL(Stringurl)实现URL地址重写。

### Cookie与Session的区别

如果说Cookie机制是通过检查客户身上的“通行证”来确定客户身份的话，那么Session机制就是通过检查服务器上的“客户明细表”来确认客户身份。Session相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。

cookie数据存放在客户的浏览器上，session数据放在服务器上；
- cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用session；
- session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用COOKIE；
- 单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能超过3K；


## 小结

Cookie和Session的方案虽然分别属于客户端和服务端，但是服务端的session的实现对客户端的cookie有依赖关系的，上面我讲到服务端执行session机制时候会生成session的id值，这个id值会发送给客户端，客户端每次请求都会把这个id值放到http请求的头部发送给服务端，而这个id值在客户端会保存下来，保存的容器就是cookie，因此当我们完全禁掉浏览器的cookie的时候，服务端的session也会不能正常使用（注意：有些资料说ASP解决这个问题，当浏览器的cookie被禁掉，服务端的session任然可以正常使用，ASP我没试验过，但是对于网络上很多用php和jsp编写的网站，我发现禁掉cookie，网站的session都无法正常的访问）。

参考：
1. [理解Cookie和Session机制](https://my.oschina.net/xianggao/blog/395675?fromerr=GC9KVenE)
2. 改进版：[理解Cookie和Session机制](https://www.cnblogs.com/andy-zhou/p/5360107.html)