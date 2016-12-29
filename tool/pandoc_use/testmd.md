#HTTP 熟悉&陌生

##你所没有深入的HTTP

Internet有两个核心协议: IP和TCP，这样讲述起来似乎会很漫长。

基本概念

> 超文本传输协议 (HTTP-Hypertext transfer protocol) 是一种详细规定了浏览器和万维网服务器之间互相通信的规则，通过因特网传送万维网文档的数据传送协议。

- HTTP是用于客户端与服务端之间的通信。
- 传输层的TCP是基于网络层的IP协议的,而应用层的HTTP协议又是基于传输层的TCP协议的。


``注意``: HTTP协议只规定了客户端与服务端的通信规则，而没有规定其通讯协议，只是现在的大部分实现都是将TCP作为通讯协议。

###打开网页时发生了什么

简单地来说，当我们在浏览器上输入URL的敲下回车的时候。

 - 浏览器需要查找域名[^domain]的IP，从不同的缓存直至DNS服务器。
 - 浏览器会给web服务器发送一个HTTP请求
 - 服务器“处理”请求
 - 服务器发回一个HTML响应
 - 浏览器渲染HTML到页面。

在[StackOverflow](http://stackoverflow.com/questions/2092527/what-happens-when-you-type-in-a-url-in-browser)上有一个这样的回答会比较详细。

 - browser checks cache; if requested object is in cache and is fresh, skip to #9
 - browser asks OS for server's IP address
 - OS makes a DNS lookup and replies the IP address to the browser
 - browser opens a TCP connection to server (this step is much more complex with HTTPS)
 - browser sends the HTTP request through TCP connection
 - browser receives HTTP response and may close the TCP connection, or reuse it for another request
 - browser checks if the response is a redirect (3xx result status codes), authorization request (401), error (4xx and 5xx), etc.; these are handled differently from normal responses (2xx)
 - if cacheable, response is stored in cache
 - browser decodes response (e.g. if it's gzipped)
 - browser determines what to do with response (e.g. is it a HTML page, is it an image, is it a sound clip?)
 - browser renders response, or offers a download dialog for unrecognized types
 
忽略一些细节便剩下了

1. 从浏览器输入URL
2. 浏览器找到服务器，服务器返回HTML文档
3. 从对应的服务器下载资源

说说第一步，开始时我们输入的是URI(统一资源标识符,Uniform Resource Identifier)，它还有另外一个名字叫统一资源定位器(URL[^URL],Uniform Resource Locator)。

###URL组成

网址算是URL的一个俗称，让我们来看看一个URL的组成，以HTTP版IOT中的URL为例。

``http://b.phodal.com/athome/1``

开始之前，我们需要标出URL的80端口以及json文件的全称，那么上面的网址就会变成

``http://b.phodal.com:80/athome/1.json``

那么对于这个URL的就有下面几部分组成

 - ``http://`` http说的是这个URL用的是HTTP协议，至于``//``是一个分隔符，用法和C语言中的``;``一样。这样的协议还可以是coap,https,ftp等等。
 - ``b`` 是子域名，一个域名在**允许**的情况下可以有不限数量的子域名。
 - ``phodal.com`` 代表了一个URL是phodal.com下面的域名
 - ``80`` 80是指80端口，默认的都是80，对于一个不是80端口的URL应该是这样的``http://iot-coap.phodal.com:8896/``
 - ``athome`` 指的是虚拟目录部分，或者文件路径
 - ``1.json``看上去就是一个文件名，然而也代表着这是一个资源。
 
对就一个稍微复杂点的例子就是

``http://ebook.designiot.cn/#你所没有深入的http``

这里的``#``后面是锚部分，如果你打开这个URL就会发现会直接跳转到相应的锚部分，对就于下面这样的一个例子来说

``http://www.phodal.com/search/?q=iot&type=blog``

``?``后面的``q=iot&type=blog``的部分是参数部分，通常用于查询或者、搜索。

##一次HTTP GET请求

当我们打开最小物联网系统的一个页面时，如[http://b.phodal.com/athome/1.json](http://b.phodal.com/athome/1.json)

我们在浏览器上看到的结果是

```javascript
[
  {
    "id": 1,
    "temperature": 19,
    "sensors1": 31,
    "sensors2": 7.5,
    "led1": 0
  }
]
```

只是我们看到的是结果，忽略了这其中的过程，于是我们用curl[^curl]命令来看看详细的情况。

```bash
curl -I -s http://b.phodal.com/athome/1.json
```

出于某种原因考虑，删去了其中一些元素，剩下下面这些。

```bash
HTTP/1.1 200 OK
Content-Type: application/json
Date: Fri, 05 Sep 2014 15:05:49 GMT

[{"id":1,"temperature":19,"sensors1":31,"sensors2":7.5,"led1":0}]
```

我们用curl命令向服务器发起了GET请求，服务器返回了上面的结果。

###HTTP响应	
	
一个HTTP响应由三部分组成

- 状态行(状态码)
- 消息报头(响应报头)
- 响应正文(消息体)

####HTTP响应	状态码

在上面的结果中，状态行是

```bash
HTTP/1.1 200 OK
```

返回的状态码是200，OK是状态码的原因短语。

如果是一个跳转的页面，它就可能是下面的结果:

```bash
HTTP/1.0 301 MOVED PERMANENTLY
Date: Mon, 08 Sep 2014 12:04:01 GMT
Content-Type: text/html; charset=utf-8	
```

HTTP Status有五种状态，而这五种状态又有所细分，提一下这五种状态，详细可参见[http://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81](http://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)

- 1xx消息
- 2xx成功
- 3xx重定向
- 4xx客户端错误
- 5xx服务器错误


如

 - 200 ok  - 成功返回状态，对应，GET,PUT,PATCH,DELETE.
 - 201 created  - 成功创建。
 - 304 not modified   - HTTP缓存有效。
 - 400 bad request   - 请求格式错误。
 - 401 unauthorized   - 未授权。
 - 403 forbidden   - 鉴权成功，但是该用户没有权限。
 - 404 not found - 请求的资源不存在
 - 405 method not allowed - 该http方法不被允许。
 - 410 gone - 这个url对应的资源现在不可用。
 - 415 unsupported media type - 请求类型错误。
 - 422 unprocessable entity - 校验错误时用。
 - 429 too many request - 请求过多。

####HTTP响应	响应报头

在这次响应中，返回了两个报头，即

```bash
Content-Type: application/json
Date: Fri, 05 Sep 2014 15:05:49 GMT
```

Content-Type和Date，在这里的Context-Type是application/json，而通常情况下我们打开一个网站时，他的Content-Type应该是text/html。

```bash
Content-Type: text/html;
```

Content-Type是最重要的报头。

####HTTP响应 响应正文

正文才是我们真正想要的内容，上面的都是写给浏览器看的，一般的人不会去关注这些。

```javascript
HTTP/1.1 200 OK
Server: phodal.com/0.17.5
Content-Type: application/json

[{"id":1,"temperature":19,"sensors1":31,"sensors2":7.5,"led1":0}]
``````

通常这是以某种格式写的，在这里是以JSON写的，而对于一个网站的时候则是HTML，如:

```html
<html>
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	
</body>
</html>		
```

那么这次GET请求返回的就是:

```bash
HTTP/1.0 200 OK
Date: Mon, 08 Sep 2014 12:04:01 GMT
Content-Type: text/html; charset=utf-8	

<html>
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	[{"id":1,"temperature":19,"sensors1":31,"sensors2":7.5,"led1":0}]		
</body>
</html>	
```

虽然与第一次请求的结果在游览器上看似乎是一样的(ps:可能有微小的差异)，然而其本质是不同的。

推荐及参考书目：

 - 《Web性能权威指南》
 - 《图解HTTP》
 - 《RESTful Web Services Cookbook》
 - 《RESTful Web APIs》
 
[^domain]:形如http://www.phodal.com
 
[^URL]: URL 是 URI 的子集

[^curl]: curl是利用URL语法在命令行方式下工作的开源文件传输工具。
