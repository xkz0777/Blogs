---
title: JavaScript 学习笔记（6）
date: 2021-08-09 23:58:07
tags: ['Web','Javascript']
author: xkz
img: /medias/featureimages/js6.jpg
categories: Web
summary: JS学习笔记之Ajax篇
---

# AJAX

## 概述

AJAX 即 Asynchronous JavaScript and XML 的缩写，意思就是**用 JavaScript 执行异步网络请求**。

AJAX 不是新的编程语言， 而是一种将现有的标准组合在一起使用的新方式。  

如果仔细观察一个 Form 的提交，你就会发现，一旦用户点击“Submit”按钮，表单开始提交，浏览器就会刷新页面，然后在新页面里告诉你操作是成功了还是失败了。如果不幸由于网络太慢或者其他原因，就会得到一个 404 页面。

这就是 Web 的运作原理：一次 HTTP 请求对应一个页面。

如果要让用户留在当前页面中，同时发出新的HTTP请求，就必须用 JavaScript 发送这个新请求，接收到数据后，再用 JavaScript 更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

## AJAX 的特点

### 优点

1. 可以无需刷新页面而与服务器端进行通信。
2. 允许根据用户事件（例如鼠标悬停，判断用户名重复等）来更新部分页面内容。

### 缺点
1. 没有浏览历史，不能回退
2. 存在跨域问题（同源）
3.  SEO 不友好（因为 AJAX 的内容爬虫没法直接爬到，但是也有办法爬取到（需要得知对应的接口））

## 浏览器的同源策略

同源策略（Same-Origin Policy）最早由 Netscape 公司提出， 是浏览器的一种安全策略。  

### 同源的定义

如果两个 URL 的 protocol、port (如果有指定的话)和 host 都相同的话，则这两个 URL 是**同源**。这个方案也被称为“协议/主机/端口元组”，或者直接是 “元组”。（“元组” 是指一组项目构成的整体，双重/三重/四重/五重/等的通用形式）。

简单来说就是：协议、域名、端口号 必须完全相同。

违背同源策略就是跨域。  

下表给出了与 URL `http://store.company.com/dir/page.html` 的源进行对比的示例:

| URL                                               | 结果 | 原因                               |
| :------------------------------------------------ | :--- | :--------------------------------- |
| `http://store.company.com/dir2/other.html`        | 同源 | 只有路径不同                       |
| `http://store.company.com/dir/inner/another.html` | 同源 | 只有路径不同                       |
| `https://store.company.com/secure.html`           | 失败 | 协议不同                           |
| `http://store.company.com:81/dir/etc.html`        | 失败 | 端口不同 ( `http://` 默认端口是80) |
| `http://news.company.com/dir/other.html`          | 失败 | 主机不同                           |

### 跨源网络访问

同源策略控制不同源之间的交互，例如在使用 `XMLHttpRequest`（下面会提到）时则会受到同源策略的约束。这些交互通常分为三类：

+ 跨域写操作（Cross-origin writes）一般是被允许的。

	例如链接（links），重定向以及表单提交。特定少数的 HTTP 请求需要添加 preflight。

+ 跨域资源嵌入（Cross-origin embedding）一般是被允许的。

+ 跨域读操作（Cross-origin reads）一般是不被允许的。

### 如何解决跨域

#### JSONP

JSONP（JSON with Padding），是一个非官方的跨域解决方案，**只支持 GET 请求**。  

在网页有一些标签天生具有跨域能力，如&lt;img> &lt;link> &lt;script>。**JSONP 就是利用 &lt;script> 标签的跨域能力来发送请求的。**  

案例：

```js
//获取 input 元素
const input = document.querySelector('input');
const p = document.querySelector('p');

//声明 handle 函数
function handle(data){
    input.style.border = "solid 1px #f00";
    //修改 p 标签的提示文本
    p.innerHTML = data.msg;
}

//绑定事件
input.onblur = function(){
    //获取用户的输入值
    let username = this.value;
    //向服务器端发送请求 检测用户名是否存在
    //1. 创建 script 标签
    const script = document.createElement('script');
    //2. 设置标签的 src 属性
    script.src = 'http://127.0.0.1:8000/check-username';
    //3. 将 script 插入到文档中
    document.body.appendChild(script);
}
```

#### CORS

CORS（Cross-Origin Resource Sharing），跨域资源共享。CORS 是 HTTP 的一部分，它允许服务端来指定哪些主机可以从这个服务端加载资源。CORS 是官方的跨域解决方案， 它的特点是**不需要在客户端做任何特殊的操作， 完全在服务器端进行处理**， 支持 GET 和 POST 请求（还有 PUT、DELETE 等）。 跨域资源共享标准新增了一组 HTTP 首部字段， 允许服务器声明哪些源站通过浏览器有权限访问哪些资源。

CORS 是通过设置一个响应头来告诉浏览器，该请求允许跨域，浏览器收到该响应以后就会对响应放行。  

## XML

XML，即 EXtensible Markup Language（可扩展标记语言），名字跟 HTML 很像，被设计用来**传输和存储数据**  ，与 HTML 不同的是，XML 的标签**没有预定义**。

### XML 与 HTML

XML 不是 HTML 的替代。

XML 和 HTML 为不同的目的而设计：

XML 被设计为传输和存储数据，其焦点是数据的内容。

HTML 被设计用来显示数据，其焦点是数据的外观。

HTML 旨在显示信息，而 XML 旨在传输信息。

### XML 仅仅是纯文本

例如：

```xml
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```

### XML 与 AJAX

最初，AJAX 向服务器发起请求后，服务器将数据以 XML 的形式传回，但现在已经被 JSON 替代。

## HTTP 协议

HTTP，即 hypertext transport protocol（超文本传输协议）的缩写，协议详细规定了浏览器和万维网服务器之间互相通信的规则。可以参考维基百科对 HTTP 协议的介绍 [超文本传输协议 - 维基百科](https://zh.wikipedia.org/wiki/超文本传输协议)。

### HTTP 请求

#### 请求报文

以百度搜索 `http` 为例：

```
请求行		GET（方法）  /s?tn=02003390_42_hao_pg&ie=utf-8&wd=http  HTTP/1.1（HTTP版本）
请求头（格式都是 xx: xx（注意空格））
		Host: www.baidu.com
        Cookie: xxx（一大串）
        User-Agent: Mozilla/5.0
空行
请求体      GET时请求体为空，POST时可能不为空，例如username=admin&password=admin
```

#### 常见请求方法

|  方法  |                             描述                             |
| :----: | :----------------------------------------------------------: |
|  GET   |                   请求获取 URL 位置的资源                    |
|  HEAD  | 请求获取 URL 位置资源的响应消息报告，即获得该资源的头部信息  |
|  POST  | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。 |
|  PUT   |    请求向 URL 位置存储一个资源，**覆盖**原 URL 位置的资源    |
| DELETE |                  请求删除URL位置存储的资源                   |
| PATCH  |      是对 PUT 方法的补充，用来对已知资源进行局部更新 。      |

其中 GET 和 POST 请求是最常用的。

### HTTP 响应

#### 相应报文

```
行      HTTP/1.1（HTTP版本）  200（状态码，status_code）  OK（响应状态字符串）
头      Content-Type: text/html;charset=utf-8
		Content-encoding: gzip
空行    
体      html代码
```

常见状态码：200、404、403、401 等等

## AJAX 的使用

### 基本使用

步骤如下：

1. 创建 `XMLHttpRequest` 对象。

	```js
	const xhr = new XMLHttpRequest();
	```

2. 规定请求的类型、URL 以及是否异步处理请求。

	使用 `XMLHttpRequest` 对象的 `open(method,url,async)` 方法：

	method：请求的类型，通常为 GET 或 POST

	url：文件在服务器上的位置，如果 GET 请求带有参数，直接加上即可，参数通常按照 `Query String Parameters` 格式： `?key=value&key=value`。

	async：true（异步）或 false（同步），**默认为 `true`，一般不用写**

	例如：

	```js
	xhr.open('GET', 'http://127.0.0.1:8000/server?a=100&b=200&c=300')
	```

	（上例中的参数没有具体意义）

3. 将请求发送到服务器。

	使用 `XMLHttpRequest` 对象的 `send(string)` 方法，其中 GET 请求无参数，`string` 为 POST 请求发给服务器的参数，作为 POST 请求的请求体。

	注意：请求体没有固定的格式要求，通常应该与服务器端约定好数据格式，例如 `Form Data` 格式：`key=value&key=value` 键值对形式，当 `Content-type` 为 `application/x-www-form-urlencoded;charset=utf-8` 时，参数会以 `Form Data` 的形式（数据为 String 键值对格式）传递给接口，并且不会显示在接口 url 上。

4. 事件绑定。

	AJAX 请求是异步执行的，也就是说，要通过回调函数获得响应，该回调函数通过 `XMLHttpRequest` 对象的 `onreadystatechange()` 方法设置，例如：

	```js
	xhr.onreadystatechange = function(){
	//判断 (服务端返回了所有的结果)
	    if(xhr.readyState === 4){
	        //判断响应状态码 200  404  403 401 500
	        // 2xx 成功
	        if(xhr.status >= 200 && xhr.status < 300){
	            // 处理结果  行 头 空行 体
	            // 响应 
	            console.log(xhr.status);//状态码
	            console.log(xhr.statusText);//状态字符串
	            console.log(xhr.getAllResponseHeaders());//所有响应头
	            console.log(xhr.response);//响应体
	            // 设置 result 的文本
	            const result = document.getElementById('result')
	            result.innerHTML = xhr.response;
	        }
	    }
	}
	```

	其中 `readyState` 为 `XMLHttpRequest` 对象的属性，一共有五种取值，如下：

	 0 － （未初始化）还没有调用 `send()` 方法
	 1 － （载入）已调用 `send()` 方法，正在发送请求
	 2 － （载入完成）`send()` 方法执行完成，已经接收到全部响应内容
	 3 － （交互）正在解析响应内容
	 4 － （完成）响应内容解析完成，可以在客户端调用了

	`onreadystatechange()` 方法顾名思义是当 `readyState` 属性改变后会调用回调函数，由于我们只希望完成了响应之后再调用回调函数，因此需要判断 `readyState` 的值是否为 4，之后判断响应码，如果响应码为 `2xx`（2 开头）表示成功（百分之九十九的情况都是 200）。

#### 设置请求头

上面说到了 `Content-type` 为 `application/x-www-form-urlencoded;charset=utf-8`，设置的方式为：

```js
xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
```

也可以自定义请求头信息，例如：

```js
xhr.setRequestHeader('name','USTC');
```

上面的请求头会被浏览器安全机制拦截，此时需要服务器端设置对应的响应头：

```js
response.setHeader('Access-Control-Allow-Header', '*');
```

意为可以接受所有类型的请求头，上面的代码通常由后端人员完成。

#### 处理服务端的返回数据

上面提到过，服务端通常将数据以 JSON 的格式返回（通常是对象经过 `JSON.stringify()` 序列化得到的 JSON 字符串），此时有两种方法解析：

1. 使用 `JSON.parse()` 方法，手动解析 JSON 字符串。

2. 设置 `XMLHttpRequest` 对象的 `responseType` 属性，让 `XMLHttpRequest` 对象自动对其进行解析：

	```js
	xhr.responseType = 'json';
	```

#### IE 缓存问题

反人类浏览器 IE 会对 AJAX 请求结果作缓存，如果服务器数据更新快、时效性强，会导致 AJAX 请求的返回结果不是实时的，此时可以在 GET 请求的 url 中加上时间戳参数：

```js
xhr.open("GET",'http://127.0.0.1:8000/server?t='+Date.now());
```

#### 请求超时与网络异常

可以设置 `XMLHttpRequest` 对象的 `timeout` 属性，例如 `xhr.timeout = 2000` 可以设置服务器超过 2000 ms 还未响应时取消 AJAX 请求，并触发 `ontimeout()` 方法，设置超时回调：

```js
xhr.ontimeout = function(){
	alert("网络异常, 请稍后重试!!");
}
```

还可以设置网络异常回调：

```js
xhr.onerror = function(){
    alert("你的网络似乎出了一些问题!");
}
```

#### 手动取消 AJAX 请求

可以使用 `XMLHttpRequest` 对象的 `abort()` 方法取消请求，例如，可以设置一个按钮来取消 AJAX 请求：

```js
let xhr = null;
btn = document.getElementById('button')
btn.onclick = function(){
    xhr.abort();
}
```

此时应该注意的是，`xhr` 应该**在函数外先声明**，做一个全局对象，这样才能保证发送请求和取消请求对应的是同一个 `XMLHttpRequest` 对象。

#### 重复发送请求

当用户重复发送相同的请求时，会给服务器造成很大的压力，此时我们可以判断之前是不是有相同的请求，如果有，则取消之前的请求（通过上面提到的 `abort()` 方法），完整的代码如下：

```js
const btn = document.querySelector('button');
let x = null;

//标识变量
let isSending = false; // 是否正在发送AJAX请求
btn.onclick = function () {
    //判断标识变量
    if (isSending) { // 如果正在发送, 则取消该请求, 创建一个新的请求
        x.abort();
    }
    x = new XMLHttpRequest();
    //修改标识变量的值
    isSending = true;
    x.open("GET", 'http://127.0.0.1:8000/delay');
    x.send();
    x.onreadystatechange = function () {
        if (x.readyState === 4) {
            //修改标识变量
            isSending = false;
        }
    }
}
```

值得注意的是，在回调函数中修改标识变量时，判断发送结束的标志应该是 `x.readyState === 4`，**不用判断状态码**。

### 使用 jQuery

jQuery 在全局对象 `jQuery`（也就是 `$`）绑定了三个函数 `get()`、`post()`、`ajax()` 函数，可以处理 Ajax 请求。

#### 基本函数

+ `$.get(url, 请求参数, 回调函数, 响应体类型)`

	接收四个参数，url 和使用 `XMLHttpRequest` 对象是一样的，参数以对象的形式传入，作为第二个参数，回调函数作为第三个参数，例如：

	```js
	$.get('http://127.0.0.1:8000/jquery-server', {a:100, b:200}, function(data){
	    console.log(data);
	},'json');
	```

+ `$.post(url, 请求参数, 回调函数)`

	例如：

	```js
	$.post('http://127.0.0.1:8000/jquery-server', {a:100, b:200}, function(data){
	    console.log(data);
	});
	```

+ `$.ajax(url, settings)`

	其中 `settings` 为对象，常用的选项如下：

	|    属性     |                             含义                             |
	| :---------: | :----------------------------------------------------------: |
	|     url     |                             url                              |
	|    data     | 请求参数，为对象类型，如果是 GET 请求，data 将被转换成 query 附加到 URL 上，如果是 POST 请求，根据 contentType 把 data 序列化成合适的格式 |
	|    type     |           请求类型，为字符串，如 `'GET'`、`'POST'`           |
	|   headers   |               发送的额外的 HTTP 头，为对象类型               |
	|  dataType   | 响应体类型，可以指定为 `'html'`、`'xml'`、`'json'`、`'text'` 等 |
	| contentType | 发送 POST 请求的格式，默认值为 `'application/x-www-form-urlencoded; charset=UTF-8'`，也可以指定为 `text/plain`、`application/json` |
	|   timeout   |                           超时时间                           |
	|   success   |                为方法，需要写成功时的回调函数                |
	|    error    |                          失败的回调                          |

	其他属性可以参考 W3C 文档 [jQuery ajax - ajax() 方法](https://www.w3school.com.cn/jquery/ajax_ajax.asp)。

	例如：
	
	```js
	$.ajax({
	    //url
	    url: 'http://127.0.0.1:8000/jquery-server',
	    //参数
	    data: {a:100, b:200},
	    //请求类型
	    type: 'GET',
	    //响应体结果
	    dataType: 'json',
	    //成功的回调
	    success: function(data){
	        console.log(data);
	    },
	    //超时时间
	    timeout: 2000,
	    //失败的回调
	    error: function(){
	        console.log('出错啦!!');
	    },
	    //头信息
	    headers: {
	        c:300,
	        d:400
	    }
	});
	```

### 使用 axios

与使用 jQuery 差不多，在 GitHub 上有其 API 的 [完善介绍](https://github.com/axios/axios)，这里略过。
