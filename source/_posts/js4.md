---
title: JavaScript 学习笔记（4）
date: 2021-08-09 13:40:51
tags: ['Web','Javascript']
author: xkz
img: /medias/featureimages/js4.jpg
categories: Web
summary: JS学习笔记之浏览器操作篇
---

# 浏览器操作

## 浏览器对象

### window

`window` 对象不但充当全局作用域，而且表示浏览器窗口。

`window` 对象有 `innerWidth` 和 `innerHeight` 属性，可以获取浏览器窗口的内部宽度和高度。内部宽高是指除去菜单栏、工具栏、边框等占位元素后，用于显示网页的净宽高。对应的，还有 `outerWidth` 和 `outerHeight` 属性，可以获取浏览器窗口的整个宽高。

兼容性：IE <= 8 不支持，利用该特性可以判断浏览器版本：

```javascript
const width = window.innerWidth || document.body.clientWidth;
```

### navigator

`navigator` 对象表示浏览器的信息，最常用的属性包括：

+ `navigator.appName`：浏览器名称；
+ `navigator.appVersion`：浏览器版本；
+ `navigator.language`：浏览器设置的语言；
+ `navigator.platform`：操作系统类型；
+ `navigator.userAgent`：浏览器设定的 `User-Agent` 字符串。

**注意**，`navigator` 的信息可以很容易地被用户修改，所以读取的值不一定是正确的。

### screen

`screen` 对象表示屏幕的信息，常用的属性有：

+ screen.width：屏幕宽度，以像素为单位
+ screen.height：屏幕高度，以像素为单位
+ screen.colorDepth：返回颜色位数，如 8、16、24

### location

`location` 对象表示当前页面的 URL 信息。例如，一个完整的 URL：

```
http://www.example.com:8080/path/index.html?a=1&b=2#TOP
```

可以用 `location.href` 获取。要获得URL各个部分的值，可以这么写：

```
location.protocol; // 'http'
location.host; // 'www.example.com'
location.port; // '8080'
location.pathname; // '/path/index.html'
location.search; // '?a=1&b=2'
location.hash; // 'TOP'
```

要加载一个新页面，可以调用 `location.assign()`。如果要重新加载当前页面，调用 `location.reload()` 方法非常方便。

### document

`document` 对象表示当前页面。由于 HTML 在浏览器中以 DOM 形式表示为树形结构，`document` 对象就是**整个 DOM 树的根节点**。

`document` 的 `title` 属性是从 HTML 文档中的 &lt;title> 标签中的内容，但是可以动态改变。

`document`对象还有一个 `cookie` 属性，可以获取当前页面的 Cookie。

Cookie 是由服务器发送的 key-value 标示符。因为 HTTP 协议是无状态的，但是服务器要区分到底是哪个用户发过来的请求，就可以用 Cookie 来区分。当一个用户成功登录后，服务器发送一个 Cookie 给浏览器，例如 `user=ABC123XYZ(加密的字符串)...`，此后，浏览器访问该网站时，会在请求头附上这个 Cookie，服务器根据 Cookie即可区分出用户。

Cookie 还可以存储网站的一些设置，例如，页面显示的语言等等。

可以通过 `document.cookie` 读取到当前页面的 Cookie。

由于在 HTML 页面中引入第三方的 JavaScript 代码是允许的，如果第三方代码中存在恶意代码，将直接获取到用户信息，为了解决这个问题，服务器在设置 Cookie 时可以使用 `httpOnly`，设定了 `httpOnly` 的 Cookie 将不能被 JavaScript 读取。

### history

目前不建议使用。

## 操作 DOM

DOM 是树形结构，对某个 DOM 节点可以有如下操作：

+ 更新：更新该 DOM 节点的内容，相当于更新了该 DOM 节点表示的 HTML 的内容
+ 遍历：遍历该 DOM 节点下的子节点，以便进行进一步操作
+ 添加：在该 DOM 节点下新增一个子节点，相当于动态增加了一个 HTML 节点
+ 删除：将该节点从 HTML 中删除，相当于删掉了该 DOM 节点的内容**以及它包含的所有子节点**

### DOM 查找

有两组方式，第一组：`document.getElementById()` 、`document.getElementsByTagName()`、`document.getElementsByClassName()`。

要精确地选择 DOM，可以先定位父节点，再从父节点开始选择，以缩小范围：

```javascript
// 返回ID为'test'的节点：
var test = document.getElementById('test');

// 先定位ID为'test-table'的节点，再返回其内部所有tr节点：
var trs = document.getElementById('test-table').getElementsByTagName('tr');

// 先定位ID为'test-div'的节点，再返回其内部所有class包含red的节点：
var reds = document.getElementById('test-div').getElementsByClassName('red');

// 获取节点test下的所有直属子节点:
var cs = test.children;

// 获取节点test下第一个、最后一个子节点：
var first = test.firstElementChild;
var last = test.lastElementChild;
```

对于 `document.getElementsByClassName()` 方法，如果某节点的 `class` 属性是多个 class 选择器混用，例如：

```html
<div id="test-div">
    <div class="c-red">
        <p id="test-p">JavaScript</p>
        <p>Java</p>
    </div>
    <div class="c-red c-green">
        <p>Python</p>
        <p>Ruby</p>
        <p>Swift</p>
    </div>
    <div class="c-green">
        <p>Scheme</p>
        <p>Haskell</p>
    </div>
</div>
```

其中内层的第二个 &lt;div&gt; 标签即为多个 class 选择器混用，如果调用

```javascript
document.getElementsByClassName('c-red');
```

会得到含两个 &lt;div&gt; 标签的 `HTMLCollection`：

```javascript
HTMLCollection(2)
	0: div.c-red
	1: div.c-red.c-green
```

可以类似数组对其进行操作：

```javascript
document.getElementsByClassName('c-red')[1].getElementsByTagName('p');
/*
HTMLCollection(3) [p, p, p]
	0: p
	1: p
	2: p
*/
```

也可以直接调用

```javascript
document.getElementsByClassName('c-red c-green')
```

两个**类名用空格隔开**，此时只会得到第二个 &lt;div> 标签，但仍然是一个 `HTMLCollection`，还要用下标来访问。

第二组：`document.querySelector()` 和 `document.querySelectorAll()`。

```javascript
// 通过querySelector获取ID为q1的节点：
var q1 = document.querySelector('#q1');

// 通过querySelectorAll获取q1节点内的符合条件的所有节点：
var ps = q1.querySelectorAll('div.highlighted > p');
```

接收 CSS 选择器作为参数。

注意：对于 `document.querySelector()` 方法，如果有多个满足的节点，只会选取第一个，例如：

```javascript
const firstDiv = document.querySelector('.c-red');
console.log(firstDiv.lastElementChild.innerHTML); // Java
```

上面的 `lastElementChild` 不要写成 `lastChild`（也有该属性，但是不能获得正确内容，在该例中会得到 `undefined`）

对于多个选择器组合，只要简单的把选择器名连起来（中间没有任何符号）即可，例如：

```javascript
document.querySelector('.c-red.c-green').querySelectorAll('p');
/*
NodeList(3) [p, p, p]
	0: p
	1: p
	2: p
*/
```

还可以有更复杂的选择器：

一个 `class` 属性为 `"user-panel main"` 的 &lt;div> 标签：

```html
<div class="user-panel main">
```

里面包含一个 `name` 属性为 `"login"` 的 &lt;input> 标签：

```html
<input name="login"/>
```

选择方式如下：

```
const el = document.querySelector("div.user-panel.main input[name='login']");
```

使用第二组方式获取 DOM 节点更方便，但是效率相对较低一些。

### DOM 更新

#### 直接修改节点文本

+ 修改 `innerHTML` 属性

	不但可以修改一个 DOM 节点的文本内容，还可以直接通过 HTML 片段修改 DOM 节点内部的子树：

	```javascript
	// 获取<p id="p-id">...</p>
	var p = document.getElementById('p-id');
	// 设置文本为abc:
	p.innerHTML = 'ABC'; // <p id="p-id">ABC</p>
	// 设置HTML:
	p.innerHTML = 'ABC <span style="color:red">RED</span> XYZ';
	// <p>...</p>的内部结构已修改
	```

+ 修改 `innerText` 或 `textContent` 属性

	可以自动对字符串进行HTML编码，保证无法设置任何HTML标签：

	```javascript
	// 获取<p id="p-id">...</p>
	var p = document.getElementById('p-id');
	// 设置文本:
	p.innerText = '<script>alert("Hi")</script>';
	// HTML被自动编码，无法设置一个<script>节点:
	// <p id="p-id">&lt;script&gt;alert("Hi")&lt;/script&gt;</p>
	```

	两者的区别在于读取属性时，`innerText` 不返回隐藏元素的文本，而 `textContent` 返回所有文本。

#### 修改属性

只要通过 `HTMLNode.attribute` 的形式即可访问属性，例如 `p.id` 可以获取 `id` 属性，`p.style.color` 可以获取 CSS 中 `color` 属性。但是对于 `font-size` 之类的属性，需要把所有斜杠去掉并换成驼峰式：`p.style.fontSize`。

对获取到的 DOM 节点，还有四个方法：

+ `getAttribute("属性名")`
+ `setAttribute("属性名", 属性值)`
+ `hasAttribute("属性名")`
+ `removeAttribute("属性名")`

### DOM 添加

如果这个 DOM 节点是空的，例如 `<div></div>`，直接使用 `innerHTML = '<span>child</span>'` 就可以修改 DOM 节点的内容，相当于“插入”了新的 DOM 节点。

如果不是空的，此时需要

1. 获取需要插入的节点（使用 DOM 查找，或自己创建）
2. 将该节点插入目标节点中

其中插入的方法有：

`appendChild()` 和 `insertBefore()`。

例如，对下面的 HTML 结构

```html
<p id="js">JavaScript</p>
<div id="list">
    <p id="java">Java</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
</div>
```

如果要把上面的 &lt;p> 标签添加到列表中

```javascript
var js = document.getElementById('js'),
    list = document.getElementById('list');
list.appendChild(js);
```

现在，HTML 结构变成了这样：

```html
<!-- HTML结构 -->
<div id="list">
    <p id="java">Java</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
    <p id="js">JavaScript</p>
</div>
```

因为我们插入的 `js` 节点已经存在于当前的文档树，因此这个节点首先会从原先的位置删除，再插入到新的位置。

如果我们希望在 `python` 节点前插入 `cpp` 节点，可以使用 `document.createElement()` 方法创建新的元素，并用 `insertBefore()` 方法插入：

```javascript
var cpp = document.createElement('p'); // 创建新元素
cpp.innerText = 'C++'; //设置属性
cpp.id = 'cpp';
var python = document.getElementById('python');
var list = document.getElementById('list');
list.insertBefore(cpp, python);
```

现在，HTML 结构变成了这样：

```html
<!-- HTML结构 -->
<div id="list">
    <p id="java">Java</p>
    <p id="cpp">C++</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
    <p id="js">JavaScript</p>
</div>
```

注意：我们应尽量少的操作 DOM 树，因为每次修改 DOM 树，都导致重新 layout。

1. 如果同时创建父元素和子元素时，应该在内存中先将子元素添加到父元素，再将父元素一次性挂到页面
2. 如果只添加多个平级子元素时,，就要将所有子元素，临时添加到文档片段中。再将文档片段整体添加到页面

文档片段：内存中，临时保存多个平级子元素的虚拟父元素，用法和普通父元素完全一样

用法：

1. 创建片段：

	```javascript
	var frag = document.createDocumentFragment();
	```

2. 将子元素临时追加到 `frag` 中：

	```javascript
	frag.appendChild(child);
	```

3. 将 `frag` 追加到页面：

	```javascript
	parent.appendChild(frag); 
	```

	`append()` 之后，`frag` **自动释放**。

### DOM 删除

要删除一个节点，首先要获得该节点本身以及它的父节点，然后，调用父节点的 `removeChild()`  方法把自己删掉：

```javascript
// 拿到待删除节点:
var self = document.getElementById('to-be-removed');
// 拿到父节点:
var parent = self.parentElement;
// 删除:
var removed = parent.removeChild(self);
removed === self; // true
```

注意到删除后的节点虽然不在文档树中了，但其实它还在内存中，可以随时再次被添加到别的位置。

遍历一个父节点的子节点并进行删除操作时，要注意，`children `属性是一个只读属性，并且它**在子节点变化时会实时更新**。

例如，对于如下HTML结构：

```html
<div id="parent">
    <p>First</p>
    <p>Second</p>
</div>
```

当我们用如下代码删除子节点时：

```javascript
var parent = document.getElementById('parent');
parent.removeChild(parent.children[0]);
parent.removeChild(parent.children[1]); // <-- 浏览器报错
```

浏览器报错：`parent.children[1]` 不是一个有效的节点。原因就在于，当 `<p>First</p>` 节点被删除后，`parent.children` 的节点数量已经从 2 变为了 1，索引 `[1]` 已经不存在了。

因此，删除多个节点时，要注意 `children` 属性时刻都在变化，此时最好的方式是，创建一个数组作为拷贝，然后再删除（避免掉对 `children` 属性的遍历），例如：

```html
<!-- HTML结构 -->
<ul id="test-list">
    <li>JavaScript</li>
    <li>Swift</li>
    <li>HTML</li>
    <li>C++</li>
    <li>CSS</li>
    <li>DirectX</li>
</ul>
```

把与 Web 开发技术不相关的节点删掉：

```javascript
const relative = ['JavaScript', 'CSS', 'HTML'];
const ul = document.getElementById('test-list');
const arr = Array.from(ul.children)
arr.forEach(li => {
    if (!relative.includes(li.innerText)) {
        ul.removeChild(li)
    }
})
```

## 操作表单

### 获取表单数据

在 HTML 部分介绍了 &lt;input> 标签及其对应的属性，主要有以下几种

+ 文本框，对应的 `<input type="text">`，用于输入文本；
+ 密码框，对应的 `<input type="password">`，用于输入口令；
+ 单选框，对应的 `<input type="radio">`，用于选择一项；
+ 复选框，对应的 `<input type="checkbox">`，用于选择多项；
+ 下拉框，对应的 `<select>`，用于选择一项；
+ 隐藏文本，对应的 `<input type="hidden">`，用户不可见，但表单提交时会把隐藏文本发送到服务器。

如果我们获得了一个 `<input>` 节点的引用，就可以直接调用 `value` 获得对应的用户输入值：

```javascript
// <input type="text" id="email">
var input = document.getElementById('email');
input.value; // '用户输入的值'
```

这种方式可以应用于 `text`、`password`、`hidden` 以及 `select`。但是，对于单选框和复选框，`value` 属性返回的永远是 HTML 预设的值，而我们需要获得的实际是用户是否勾上了选项，所以应该用 `checked` 判断：

```javascript
// <label><input type="radio" name="weekday" id="monday" value="1"> Monday</label>
// <label><input type="radio" name="weekday" id="tuesday" value="2"> Tuesday</label>
var mon = document.getElementById('monday');
var tue = document.getElementById('tuesday');
mon.value; // '1'
tue.value; // '2'
mon.checked; // true或者false
tue.checked; // true或者false
```

可以直接修改属性来设置值，例如：

```javascript
// <input type="text" id="email">
var input = document.getElementById('email');
input.value = 'test@example.com'; // 文本框的内容已更新
```

### 提交表单

+ 通过 `<form>` 元素的 `submit()` 方法

	例如，响应一个 `<button>` 的 `click` 事件，在 JavaScript 代码中提交表单：

	```html
	<!-- HTML -->
	<form id="test-form">
	  	<input type="text" name="test">
	  	<button type="button" onclick="doSubmitForm()">Submit</button>
	</form>
	
	<script>
	function doSubmitForm() {
	  	var form = document.getElementById('test-form');
	  	// 可以在此修改form的input...
	  	// 提交form:
	  	form.submit();
	}
	</script>
	```

	这种方式的缺点是扰乱了浏览器对表单的正常提交。浏览器默认点击 `<button type="submit">` 时提交表单，或者用户在最后一个输入框按回车键。

+ 响应 `<form>` 本身的 `onsubmit` 事件，在提交 form 时作修改：

	```html
	<!-- HTML -->
	<form id="test-form" onsubmit="return checkForm()">
	    <input type="text" name="test">
	    <button type="submit">Submit</button>
	</form>
	
	<script>
	function checkForm() {
	    var form = document.getElementById('test-form');
	    // 可以在此修改form的input...
	    // 继续下一步:
	    return true;
	}
	</script>
	```

	注意要 `return true` 来告诉浏览器继续提交，如果 `return false`，浏览器将不会继续提交表单，这种情况通常对应用户输入有误，提示用户错误信息后终止提交表单。

在检查和修改 `<input>` 时，要充分利用 `<input type="hidden">` 来传递数据。

例如，很多登录表单希望用户输入用户名和密码，但是，出于安全考虑，提交表单时不传输明文密码，而是密码的加密数据（如MD5）。如果直接修改 `<input>`：

```html
<!-- HTML -->
<form id="login-form" method="post" onsubmit="return checkForm()">
    <input type="text" id="username" name="username">
    <input type="password" id="password" name="password">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var pwd = document.getElementById('password');
    // 把用户输入的明文变为MD5:
    pwd.value = toMD5(pwd.value);
    // 继续下一步:
    return true;
}
</script>
```

当用户输入完密码提交时，密码框的显示会突然从几个 `*` 变成 32 个 `*`（因为 MD5 有 32 个字符）。

要想不改变用户的输入，可以利用 `<input type="hidden">` 实现：

```html
<!-- HTML -->
<form id="login-form" method="post" onsubmit="return checkForm()">
    <input type="text" id="username" name="username">
    <input type="password" id="input-password">
    <input type="hidden" id="md5-password" name="password">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var input_pwd = document.getElementById('input-password');
    var md5_pwd = document.getElementById('md5-password');
    // 把用户输入的明文变为MD5:
    md5_pwd.value = toMD5(input_pwd.value);
    // 继续下一步:
    return true;
}
</script>
```

注意到 `id` 为 `md5-password` 的 `<input>` 标记了 `name="password"`，而用户输入的 `id` 为 `input-password` 的 `<input>` 没有 `name` 属性。没有 `name` 属性的 `<input>` 的数据不会被提交。

例如，检查用户注册信息是否正确，要求：

+ 用户名必须是 3-10 位英文字母或数字
+ 密码必须是 6-20 位
+ 两次输入密码必须一致

其中表单部分：

```html
<form id="test-register" action="#" target="_blank"
    onsubmit="return checkRegisterForm()">
    <p id="test-error" style="color:red"></p>
    <p>
        用户名: <input type="text" id="username" name="username">
    </p>
    <p>
        密码: <input type="password" id="password" name="password">
    </p>
    <p>
        重复密码: <input type="password" id="password-2">
    </p>
    <p>
        <button type="submit">提交</button> <button type="reset">重置</button>
    </p>
</form>
```

函数如下：

```javascript
var checkRegisterForm = function () {
    const userName = document.getElementById('username').value,
        passWord1 = document.getElementById('password').value,
        passWord2 = document.getElementById('password-2').value,
        p = document.getElementById('test-error'),
        re1 = /[a-zA-Z0-9]{3,10}/,
        re2 = /[\S]{6,20}/;
    if (!re1.test(userName)) {
        p.innerHTML = '用户名不合法';
        alert('用户名不合法')
        return false;
    } else if (!re2.test(passWord1)) {
        p.innerHTML = '密码不合法'
        alert('密码不合法')
        return false;
    } else if (passWord1 != passWord2) {
        p.innerHTML = '两次输入密码不一致';
        alert('两次输入密码不一致');
        return false;
    }
    return true;
}
```

## Promise 对象

由于 JavaScript 是单线程语言其所有网络操作，浏览器事件，都必须是异步执行。**异步执行可以用回调函数实现**，但这种方法容易变成多层回调嵌套，即回调地狱，例如：xkz 今天要上四门课，数分、线代、电磁学和大雾实验，每门课都需要上 1s（乱编的），以函数输出课程名作为课程结束的标志，如果使用普通的回调，可以如下：

```js
function mathAnalysis(fn) {
    setTimeout(() => {
        fn('数分');
    }, 1000);
}

function linearAlgebra(fn) {
    setTimeout(() => {
        fn('线代');
    }, 1000);
}

function electromagnetism(fn) {
    setTimeout(() => {
        fn('电磁');
    }, 1000);
}

function bigFog(fn) {
    setTimeout(() => {
        fn('大雾');
    }, 1000);
}

mathAnalysis((courseName) => {
    console.log(courseName);
    linearAlgebra((courseName) => {
        console.log(courseName);
        electromagnetism((courseName) => {
            console.log(courseName);
            bigFog((courseName) => {
                console.log(courseName);
            })
        })
    })
});
```

可以发现，当回调层数增加时，代码会变得臃肿难以维护，因此更好的方案是使用 Promise 对象，ES6 原生提供了 Promise 对象。Promise 对象代表了未来将要发生的事件，用来传递异步操作的消息。

Promise 对象代表一个异步操作，有三种状态：

+ pending: 初始状态，不是成功或失败状态。
+ fulfilled: 意味着操作成功完成。
+ rejected: 意味着操作失败。

**只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态**。这也是 Promise 这个名字的由来。

以 AJAX 为例，pending 就是正在等待服务器返回结果，resolved 就是 AJAX 成功，rejected 就是失败。

### 创建 Promise

要想创建一个 Promise 对象、可以使用 new 来调用 Promise 的构造器来进行实例化。

```js
const promise = new Promise(function(resolve, reject) {    
    // 异步处理    
    // 处理结束后、调用resolve 或 reject 
});
```

Promise 构造函数包含一个参数和一个带有 resolve（解析）和 reject（拒绝）两个参数的回调。在回调中执行一些操作（例如异步），如果一切都正常，则调用 resolve，否则调用 reject。

**resolve 和 reject 将异步数据传出来，而通过 `Promise.prototype.then()`方法可以获取异步数据。**

### 链式调用和 Promise.prototype.then 方法

`Promise.prototype.then` 方法返回的是一个新的 Promise 对象，因此可以采用链式写法。连续执行两个或者多个异步操作是一个常见的需求，**在上一个操作执行成功之后，开始下一个的操作，并带着上一步操作所返回的结果**。我们可以通过创造一个 Promise 链来实现这种需求，例如，对于之前上课的例子，我们可以将其改写为：

```js
function mathAnalysis() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('数分');
        }, 1000);
    })
}

function linearAlgebra() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('线代');
        }, 1000);
    })
}

function electromagnetism() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('电磁');
        }, 1000);
    })
}

function bigFog() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('大雾');
        }, 1000);
    })
}

mathAnalysis().then((data) => { //then()方法的回调函数中的参数接收resolve()的内容
    console.log(data);
    return linearAlgebra();
}).then((data) => {
    console.log(data);
    return electromagnetism();
}).then((data) => {
    console.log(data);
    return bigFog();
}).then((data) => {
    console.log(data);
});
```

### Promise.prototype.catch 方法

`Promise.prototype.catch` 方法是 `Promise.prototype.then(null, rejection)` 的别名，用于指定发生错误时的回调函数，例如：

```js
getJSON("/posts.json").then(function(posts) {
    // some code 
}).catch(function(error) {  
    // 处理前一个回调函数运行时发生的错误  
    console.log('发生错误！', error); 
});
```

Promise 对象的错误具有"冒泡"性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 catch 语句捕获：

```js
getJSON("/post/1.json").then(function(post) {
    return getJSON(post.commentURL); 
}).then(function(comments) {
    // some code 
}).catch(function(error) {
    // 处理前两个回调函数的错误 
});
```

因此，通常只在最后设置一个 catch 语句捕获错误。



