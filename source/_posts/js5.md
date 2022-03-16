---
title: JavaScript 学习笔记（5）
date: 2021-08-09 23:43:08
tags: ['Web','Javascript']
author: xkz
img: /medias/featureimages/js5.jpg
categories: Web
summary: JS学习笔记之jQuery篇
---

# jQuery

核心理念：“Write Less, Do More“，能够极大简化 DOM 操作，并能消除浏览器差异，jQuery 只是一个 `jquery-xxx.js` 文件，使用时只需要在页面的 `<head>` 标签内引入 jQuery 文件即可，其中 jQuery 文件可以从官网上下载，也可以直接使用 CDN 或是官网 jQuery 文件的 url （更常用），例如 https://code.jquery.com/jquery-3.6.0.min.js 为最新版本的 jQuery 文件。

jQuery 有大量的 API，可以查阅官网的 [API文档](https://api.jquery.com/)。

## $ 符号

`$ ` 是著名的 jQuery 符号。实际上，jQuery 把所有功能全部封装在一个全局变量 `jQuery` 中，而 `$` 是变量 `jQuery` 的别名：

`$` **本质上就是一个函数**，但是函数也是对象，于是 `$` 除了可以直接调用外，也可以有很多其他属性。

绝大多数时候，我们都直接用 `$`。但是，如果 `$` 被占用了，我们需要让 `jQuery` ”释放“ `$` 变量，然后使用 `jQuery` 这个变量：

```javascript
$; // jQuery(selector, context)
jQuery.noConflict();
$; // undefined
jQuery; // jQuery(selector, context)
```

这种做法的原理是 jQuery 在占用 `$` 之前，先在内部保存了原来的 `$` ,调用 `jQuery.noConflict()` 时会把原来保存的变量还原。

## jQuery 查找

### 按 ID 查找

用 `$('#ID名称')` 的形式查找：

```javascript
// 查找<div id="abc">:
var div = $('#abc');
```

**返回 jQuery 对象，类似数组，它的每个元素都是一个引用了 DOM 节点的对象**。

以上面的查找为例，如果 `id `为 `abc` 的 `<div>` 存在，返回的 jQuery 对象如下：

```html
[<div id="abc">...</div>]
```

如果 `id` 为 `abc` 的 `<div>` 不存在，返回的 jQuery 对象如下：

```
[]
```

总之 jQuery 的选择器**不会返回 `undefined` 或者 `null`**，这样的好处是不必在下一行判断` if(div === undefined)`。

jQuery 对象和 DOM 对象之间可以互相转化：

```javascript
var div = $('#abc'); // jQuery对象
var divDom = div.get(0); // 假设存在div，获取第1个DOM元素
var another = $(divDom); // 重新把DOM包装为jQuery对象
```

通常情况下直接使用 jQuery 对象更加方便。如果拿到了一个 DOM 对象，可以简单地调用 `$(DomObject)` 把它变成 jQuery对象，这样就可以方便地使用 jQuery 的 API 了。

### 按 tag 查找

按 tag 查找只需要写上 tag 名称就可以了：

```javascript
var ps = $('p'); // 返回所有<p>节点
ps.length; // 数一数页面有多少个<p>节点
```

### 按class查找

用 `$('.类名称')` 的形式查找：

```javascript
var a = $('.red'); // 所有节点包含`class="red"`都将返回
// 例如:
// <div class="red">...</div>
// <p class="green red">...</p>
```

通常很多节点有多个 class，我们可以查找同时包含 `red` 和 `green` 的节点：

```javascript
var a = $('.red.green'); // 注意没有空格！（类似 document.querySelectorAll()方法）
// 符合条件的节点：
// <div class="red green">...</div>
// <div class="blue green red">...</div>
```

### 按属性查找

一个 DOM 节点除了 `id` 和 `class` 外还可以有很多属性，很多时候按属性查找会非常方便，比如在一个表单中按属性来查找：

```javascript
var email = $('[name=email]'); // 找出<??? name="email">
var passwordInput = $('[type=password]'); // 找出<??? type="password">
var a = $('[items="A B"]'); // 找出<??? items="A B">
```

当属性的值包含空格等特殊字符时，需要用双引号括起来。

按属性查找还可以使用前缀查找或者后缀查找（此时类似正则表达式）：

```javascript
var icons = $('[name^=icon]'); // 找出所有name属性值以icon开头的DOM
// 例如: name="icon-1", name="icon-2"
var names = $('[name$=with]'); // 找出所有name属性值以with结尾的DOM
// 例如: name="startswith", name="endswith"
```

这个方法尤其适合通过 `class` 属性查找，且**不受 `class` 包含多个名称的影响：**

```javascript
var icons = $('[class^="icon-"]'); // 找出所有class包含至少一个以`icon-`开头的DOM
// 例如: class="icon-clock", class="abc icon-home"
```

### 组合查找

组合查找就是把上述简单选择器组合起来使用。如果我们查找 `$('[name=email]')`，很可能把表单外的 `<div name="email">` 也找出来，但我们只希望查找 `<input>`，就可以这么写：

```javascript
var emailInput = $('input[name=email]'); // 不会找出<div name="email">
```

根据 `tag` 和 `class` 来组合查找也很常见：

```javascript
var tr = $('tr.red'); // 找出<tr class="red ...">...</tr>
```

### 多项选择器

多项选择器就是把多个选择器用 `,` 组合起来一块选：

```javascript
$('p,div'); // 把<p>和<div>都选出来
$('p.red,p.green'); // 把<p class="red">和<p class="green">都选出来
```

要注意的是，**选出来的元素是按照它们在 HTML 中出现的顺序排列的，而且不会有重复元素**。例如，`<p class="red green">` 不会被上面的 `$('p.red,p.green')` 选择两次。

### 层级选择器

如果两个 DOM 元素具有层级关系，就可以用 `$('ancestor descendant')` 来选择，**层级之间用空格隔开**。例如：

```javascript
<!-- HTML结构 -->
<div class="testing">
    <ul class="lang">
        <li class="lang-javascript">JavaScript</li>
        <li class="lang-python">Python</li>
        <li class="lang-lua">Lua</li>
    </ul>
</div>
```

要选出 JavaScript，可以用层级选择器：

```javascript
$('ul.lang li.lang-javascript'); // [<li class="lang-javascript">JavaScript</li>]
$('div.testing li.lang-javascript'); // [<li class="lang-javascript">JavaScript</li>]
```

因为 `<div>` 和 `<ul>` 都是 `<li>` 的祖先节点，所以上面两种方式都可以选出相应的 `<li>` 节点。

要选择所有的 `<li>` 节点，用：

```javascript
$('ul.lang li');
```

这种层级选择器相比单个的选择器好处在于，它缩小了选择范围，因为首先要定位父节点，才能选择相应的子节点，这样避免了页面其他不相关的元素。

例如：

```javascript
$('form[name=upload] input');
```

就把选择范围限定在 `name` 属性为 `upload` 的表单里。如果页面有很多表单，其他表单的 `<input>` 不会被选择。

多层选择也是允许的：

```javascript
$('form.test p input'); // 在form表单选择被<p>包含的<input>
```

### 子选择器

子选择器 `$('parent>child')` 类似层级选择器，但是**限定了层级关系必须是父子关系**，还是以上面的例子：

```javascript
$('ul.lang>li.lang-javascript'); // 可以选出[<li class="lang-javascript">JavaScript</li>]
$('div.testing>li.lang-javascript'); // [], 无法选出，因为<div>和<li>不构成父子关系
```

### 过滤器

过滤器一般不单独使用，它通常附加在选择器上，帮助我们更精确地定位元素。

```javascript
$('ul.lang li'); // 选出JavaScript、Python和Lua 3个节点

$('ul.lang li:first-child'); // 仅选出JavaScript
$('ul.lang li:last-child'); // 仅选出Lua
$('ul.lang li:nth-child(2)'); // 选出第N个元素，N从1开始
$('ul.lang li:nth-child(even)'); // 选出序号为偶数的元素
$('ul.lang li:nth-child(odd)'); // 选出序号为奇数的元素
```

### 表单相关

针对表单元素，jQuery 还有一组特殊的选择器：

+ `:input`：可以选择 `<input>`，`<textarea>`，`<select>` 和 `<button>`；
+ `:file`：可以选择 `<input type="file">`，和 `input[type=file]` 一样；
+ `:checkbox`：可以选择复选框，和 `input[type=checkbox]` 一样；
+ `:radio`：可以选择单选框，和 `input[type=radio]` 一样；
+ `:focus`：可以选择当前输入焦点的元素，例如把光标放到一个 `<input>` 上，用 `$('input:focus')` 就可以选出；
+ `:checked`：选择当前勾上的单选框和复选框，用这个选择器可以立刻获得用户选择的项目，如 `$('input[type=radio]:checked')`；
+ `:enabled`：可以选择可以正常输入的 `<input>`、`<select>` 等，也就是没有灰掉的输入；
+ `:disabled`：和 `:enabled` 正好相反，选择那些不能输入的。

此外，jQuery 还有很多有用的选择器，例如，选出可见的或隐藏的元素：

```javascript
$('div:visible'); // 所有可见的div
$('div:hidden'); // 所有隐藏的div
```

### 查找和过滤

当我们拿到一个 jQuery 对象后，**还可以以这个对象为基准，进行查找和过滤**。

最常见的查找是在某个节点的所有子节点中查找，使用 `find()` 方法，它本身**又接收一个任意的选择器**。

例如如下的HTML结构：

```html
<ul class="lang">
    <li class="js dy">JavaScript</li>
    <li class="dy">Python</li>
    <li id="swift">Swift</li>
    <li class="dy">Scheme</li>
    <li name="haskell">Haskell</li>
</ul>
```

用 `find()` 查找：

```javascript
var ul = $('ul.lang'); // 获得<ul>
var dy = ul.find('.dy'); // 获得JavaScript, Python, Scheme
var swf = ul.find('#swift'); // 获得Swift
var hsk = ul.find('[name=haskell]'); // 获得Haskell
```

如果要从当前节点开始向上查找，使用 `parent()` 方法：

```javascript
var swf = $('#swift'); // 获得Swift
var parent = swf.parent(); // 获得Swift的上层节点<ul>
var a = swf.parent('.red'); // 获得Swift的上层节点<ul>，同时传入过滤条件。如果ul不符合条件，返回空jQuery对象
```

对于位于同一层级的节点，可以通过 `next()`、`prev()`、`nextAll()`、`prevAll()`、`siblings()` 方法，例如：

当我们已经拿到 `Swift` 节点后：

```javascript
var swift = $('#swift');

swift.next(); // Scheme
swift.next('[name=haskell]'); // 空的jQuery对象，因为Swift的下一个元素Scheme不符合条件[name=haskell]
swift.nextAll('[name=haskell]').html(); // Haskell

swift.prev(); // Python
swift.prev('.dy'); // Python，因为Python同时符合过滤器条件.dy

arr = swift.siblings().map(function () {
    return this.innerHTML;
});
for (const lang of arr) {
    console.log(lang); // JavaScript Python Scheme Haskell
}
```

#### 过滤

和函数式编程的 map、filter 类似，jQuery 对象也有类似的方法。

`filter()` 方法可以过滤掉不符合选择器条件的节点：

```javascript
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var a = langs.filter('.dy'); // 拿到JavaScript, Python, Scheme
```

或者传入一个函数，要特别注意**函数内部的 `this` 被绑定为 DOM 对象，不是 jQuery 对象**：

```javascript
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
langs.filter(function () {
    return this.innerHTML.indexOf('S') === 0; // 返回S开头的节点
}); // 拿到Swift, Scheme
```

里面的 `function` 不能写成箭头函数！否则会造成 `this` 的指向错误。

`map()` 方法把一个 jQuery 对象包含的若干 DOM 节点转化为其他对象：

```javascript
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var arr = langs.map(function () {
    return this.innerHTML;
}).get(); // 用get()拿到包含string的Array：['JavaScript', 'Python', 'Swift', 'Scheme', 'Haskell']
```

此外，一个 jQuery 对象如果包含了不止一个 DOM 节点，`first()`、`last() `和 `slice()` 方法可以返回一个新的 jQuery 对象，把不需要的 DOM 节点去掉：

```javascript
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var js = langs.first(); // JavaScript，相当于$('ul.lang li:first-child')
var haskell = langs.last(); // Haskell, 相当于$('ul.lang li:last-child')
var sub = langs.slice(2, 4); // Swift, Scheme, 参数和数组的slice()方法一致
```

例如，对于下面的 HTML 文档，希望提取表单中的信息，并转成 JSON 格式：

```html
<form id="test-form" action="#" onsubmit="return f();">
    <p><label>Name: <input name="name"></label></p>
    <p><label>Email: <input name="email"></label></p>
    <p><label>Password: <input name="password" type="password"></label></p>
    <p>Gender: <label><input name="gender" type="radio" value="m" checked>
            Male</label> <label><input name="gender" type="radio" value="f">
            Female</label></p>
    <p><label>City: <select name="city">
                <option value="BJ" selected>Beijing</option>
                <option value="SH">Shanghai</option>
                <option value="CD">Chengdu</option>
                <option value="XM">Xiamen</option>
            </select></label></p>
    <p><button type="submit">Submit</button></p>
</form>
```

接收的函数如下：

```javascript
function f() {
    let json = {};
    const data = {};
    data.name = $('input[name="name"]').val();
    data.email = $('input[name="email"]').val();
    data.password = $('input[name="password"]').val();
    data.gender = $('input[type=radio]:checked').val() === 'm' ? "Male" : "Female";
    data.city = $('option:checked').text();
    json = JSON.stringify(data);
    console.log(json);
}
```

## jQuery 操作

获取了 jQuery 对象后，便可以对该对象对应的 DOM 节点进行操作。

### 修改 Text 和 HTML

jQuery 对象的 `text()` 和 `html()` 方法分别获取节点的文本和原始HTML文本，例如，如下的 HTML 结构：

```html
<ul id="test-ul">
    <li class="js">JavaScript</li>
    <li name="book">Java &amp; JavaScript</li>
</ul>
```

分别获取文本和HTML：

```javascript
$('#test-ul li[name=book]').text(); // 'Java & JavaScript'
$('#test-ul li[name=book]').html(); // 'Java &amp; JavaScript'
```

**无参数调用 `text()` 是获取文本，传入参数就变成设置文本**，HTML 类似。

**一个 jQuery 对象可以包含 0 个或任意个DOM 对象，它的方法实际上会作用在对应的每个 DOM 节点上。**（重点！！）

在上面的例子中：

```javascript
$('#test-ul li').text('JS'); // 两个节点都变成了JS
```

所以 jQuery 对象的另一个好处是我们**可以执行一个操作，作用在对应的一组 DOM 节点上**。**即使选择器没有返回任何 DOM节点，调用 jQuery 对象的方法仍然不会报错：**

```javascript
// 如果不存在id为not-exist的节点：
$('#not-exist').text('Hello'); // 代码不报错，没有节点被设置为'Hello'
```

这意味着 jQuery 帮你免去了许多 `if` 语句。

### 修改 CSS

jQuery 对象有“批量操作”的特点，这用于修改 CSS 非常方便了。考虑下面的HTML结构：

```html
<ul id="test-css">
    <li class="lang dy"><span>JavaScript</span></li>
    <li class="lang"><span>Java</span></li>
    <li class="lang dy"><span>Python</span></li>
    <li class="lang"><span>Swift</span></li>
    <li class="lang dy"><span>Scheme</span></li>
</ul>
```

要高亮显示动态语言，调用 jQuery 对象的 `css('name', 'value')` 方法：

```javascript
$('#test-css li.dy>span').css('background-color', '#ffd351').css('color', 'red');
```

注意，**jQuery 对象的所有方法都返回一个 jQuery 对象（可能是新的也可能是自身），这样我们可以进行链式调用**。

jQuery 对象的 `css()` 方法可以这么用：

```javascript
var div = $('#test-div');
div.css('color'); // '#000033', 获取CSS属性
div.css('color', '#336699'); // 设置CSS属性
div.css('color', ''); // 清除CSS属性
```

为了和 JavaScript 保持一致，**CSS 属性可以用 `'background-color'` 和 `'backgroundColor'` 两种格式**。

**`css()` 方法将作用于 DOM 节点的 `style` 属性，具有最高优先级**。**如果要修改 `class` 属性，可以用 jQuery 提供的下列方法：**

```javascript
var div = $('#test-div');
div.hasClass('highlight'); // false， class是否包含highlight
div.addClass('highlight'); // 添加highlight这个class
div.removeClass('highlight'); // 删除highlight这个class
```

下面分别用 `css()` 方法和 `addClass()` 方法高亮显示 JavaScript：

```html
<style>
.highlight {
    color: #dd1144;
    background-color: #ffd351;
}
</style>

<div id="test-highlight-css">
    <ul>
        <li class="py"><span>Python</span></li>
        <li class="js"><span>JavaScript</span></li>
        <li class="sw"><span>Swift</span></li>
        <li class="hk"><span>Haskell</span></li>
    </ul>
</div>
```

用 `css()`：

```javascript
let js = $('div#test-highlight-css li.js');
js.css('color', '#dd1144').css('background-color', '#ffd351');
```

用 `addClass()`：

```javascript
let js = $('div#test-highlight-css li.js');
js.addClass('highlight');
```

### 显示和隐藏 DOM

要隐藏一个 DOM，我们可以设置 CSS 的 `display` 属性为 `none`，利用 `css()` 方法就可以实现。不过，要显示这个 DOM 就需要恢复原有的 `display` 属性，这就得先记下来原有的 `display` 属性到底是 `block` 还是 `inline` 还是别的值。

考虑到显示和隐藏 DOM 元素使用非常普遍，jQuery 直接提供 `show()` 和 `hide()` 方法，我们不用关心它是如何修改 `display` 属性的，总之它能正常工作：

```javascript
var a = $('a[target=_blank]');
a.hide(); // 隐藏
a.show(); // 显示
```

注意，隐藏 DOM 节点并未改变 DOM 树的结构，它只影响 DOM 节点的显示。这和删除 DOM 节点是不同的。

### 获取 DOM 信息

利用 jQuery 对象的若干方法，我们直接可以获取 DOM 的高宽等信息，而无需针对不同浏览器编写特定代码：

```javascript
// 浏览器可视窗口大小:
$(window).width(); // 800
$(window).height(); // 600

// HTML文档大小:
$(document).width(); // 800
$(document).height(); // 3500

// 某个div的大小:
var div = $('#test-div');
div.width(); // 600
div.height(); // 300
div.width(400); // 设置CSS属性 width: 400px，是否生效要看CSS是否有效
div.height('200px'); // 设置CSS属性 height: 200px，是否生效要看CSS是否有效
```

`attr()` 和 `removeAttr()` 方法用于操作 DOM 节点的属性：

```javascript
// <div id="test-div" name="Test" start="1">...</div>
var div = $('#test-div');
div.attr('data'); // undefined, 属性不存在
div.attr('name'); // 'Test'
div.attr('name', 'Hello'); // div的name属性变为'Hello'
div.removeAttr('name'); // 删除name属性
div.attr('name'); // undefined
```

`prop()` 方法和 `attr()` 类似，但是 HTML5 规定有一种属性在 DOM 节点中可以没有值，只有出现与不出现两种，例如：

```html
<input id="test-radio" type="radio" name="test" checked value="1">
```

等价于：

```html
<input id="test-radio" type="radio" name="test" checked="checked" value="1">
```

`attr()` 和 `prop()` 对于属性 `checked` 处理有所不同：

```javascript
var radio = $('#test-radio');
radio.attr('checked'); // 'checked'
radio.prop('checked'); // true
```

`prop()` 返回值更合理一些。不过，用 `is()` 方法判断更好：

```javascript
var radio = $('#test-radio');
radio.is(':checked'); // true
```

类似的属性还有 `selected`，处理时最好用 `is(':selected')`。

### 操作表单

对于表单元素，jQuery 对象统一提供 `val()` 方法获取和设置对应的 `value` 属性：

HTML 文档：

```html
<input id="test-input" name="email" value="test">
<select id="test-select" name="city">
    <option value="BJ" selected>Beijing</option>
    <option value="SH">Shanghai</option>
    <option value="SZ">Shenzhen</option>
</select>
<textarea id="test-textarea">Hello</textarea>
```

JavaScript 代码：

```javascript
var input = $('#test-input'),
    select = $('#test-select'),
    textarea = $('#test-textarea');

input.val(); // 'test'
input.val('abc@example.com'); // 文本框的内容已变为abc@example.com

select.val(); // 'BJ'
select.val('SH'); // 选择框已变为Shanghai

textarea.val(); // 'Hello'
textarea.val('Hi'); // 文本区域已更新为'Hi'
```

### 添加 DOM

要添加新的 DOM 节点，除了通过 jQuery 的 `html()` 这种暴力方法外，还可以用 `append()` 方法，例如：

```html
<div id="test-div">
    <ul>
        <li><span>JavaScript</span></li>
        <li><span>Python</span></li>
        <li><span>Swift</span></li>
    </ul>
</div>
```

如何向列表新增一个语言？首先要拿到 `<ul>` 节点：

```javascript
var ul = $('#test-div>ul');
```

然后，调用 `append()` 传入 HTML 片段：

```js
ul.append('<li><span>Haskell</span></li>');
```

除了接受字符串，`append()` 还可以传入原始的 DOM 对象，jQuery 对象和函数对象：

```js
// 创建DOM对象:
var ps = document.createElement('li');
ps.innerHTML = '<span>Pascal</span>';
// 添加DOM对象:
ul.append(ps);

// 添加jQuery对象:
ul.append($('#scheme'));

// 添加函数对象:
ul.append(function (index, html) {
    return '<li><span>Language - ' + index + '</span></li>';
});
```

传入函数时，要求返回一个字符串、DOM 对象或者 jQuery 对象。因为 jQuery 的 `append()` 可能作用于一组 DOM 节点，只有传入函数才能针对每个 DOM 生成不同的子节点。

`append()` 把DOM添加到最后，`prepend()` 则把DOM添加到最前。

另外注意，如果要添加的 DOM 节点已经存在于 HTML 文档中，用 `append()` 方法相当于移动一个 DOM 节点。

如果要把新节点插入到指定位置，例如，JavaScript 和 Python之间，那么，可以先定位到 JavaScript，然后用 `after()` 方法：

```js
var js = $('#test-div>ul>li:first-child');
js.after('<li><span>Lua</span></li>');
```

也就是说，同级节点可以用 `after()` 或者 `before()` 方法。

### 删除节点

要删除 DOM 节点，拿到 jQuery 对象后直接调用 `remove()` 方法就可以了。如果 jQuery 对象包含若干 DOM 节点，实际上可以一次删除多个 DOM 节点：

```javascript
var li = $('#test-div>ul>li');
li.remove(); // 所有<li>全被删除
```

例：

对于如下 HTML 结构，增加三个列表项 Lua、Pascal、Ruby，并按照字典序对列表项进行排序

```html
<div id="test-div">
    <ul>
        <li><span>JavaScript</span></li>
        <li><span>Python</span></li>
        <li><span>Swift</span></li>
    </ul>
</div>
```

```js
const ul = $('#test-div ul');
const arr = ['Lua', 'Pascal', 'Ruby'];
for (const lang of arr) {
    ul.append(`<li><span>${lang}</span></li>`);
}
const li = Array.from(ul.find('li span'));
const textList = li.map(e => e.innerText).sort();
li.forEach((item, i) => {
    item.innerText = textList[i];
});
```

## jQuery 事件

因为 JavaScript 在浏览器中**以单线程模式运行**，页面加载后，一旦页面上所有的 JavaScript 代码被执行完后，就**只能依赖触发事件来执行 JavaScript 代码**。

**浏览器在接收到用户的鼠标或键盘输入后，会自动在对应的 DOM 节点上触发相应的事件。如果该节点已经绑定了对应的 JavaScript 处理函数，该函数就会自动调用。**

由于不同的浏览器绑定事件的代码都不太一样，所以用 jQuery 来写代码，就屏蔽了不同浏览器的差异，我们总是编写相同的代码。

举个例子，假设要在用户点击了超链接时弹出提示框，我们用 jQuery 绑定一个 `click` 事件：

```html
<a id="test-link" href="#0">点我试试</a>
```

```js
// 获取超链接的jQuery对象:
var a = $('#test-link');
a.on('click', function () {
    alert('Hello!');
});
```

`on()` 方法用来绑定一个事件，我们需要传入事件名称和对应的处理函数。

**另一种更简化的写法是直接调用 `click()` 方法：**

```js
a.click(function () {
    alert('Hello!');
});
```

两者完全等价。我们通常用后面的写法。

jQuery 能够绑定的事件主要包括：

### 鼠标事件

+ click: 鼠标单击时触发；
+ dblclick：鼠标双击时触发；
+ mouseenter：鼠标进入时触发；
+ mouseleave：鼠标移出时触发；
+ mousemove：鼠标在 DOM 内部移动时触发；
+ hover：鼠标进入和退出时触发两个函数，相当于 mouseenter 加上 mouseleave。

### 键盘事件

键盘事件仅作用在当前焦点的 DOM 上，通常是 `<input>` 和 `<textarea>`。

+ keydown：键盘按下时触发；
+ keyup：键盘松开时触发；
+ keypress：按一次键后触发。

### 其他事件

+ focus：当 DOM 获得焦点时触发；
+ blur：当 DOM 失去焦点时触发；
+ change：当 `<input>`、`<select>` 或 `<textarea>` 的内容改变时触发；
+ submit：当 `<form>` 提交时触发；
+ ready：当页面被载入并且 DOM 树完成初始化后触发。

其中，`ready` 仅作用于 `document` 对象。由于 `ready` 事件在 DOM 完成初始化后触发，且只触发一次，所以非常适合用来写其他的初始化代码。假设我们想给一个 `<form>` 表单绑定 `submit` 事件，下面的代码没有预期的效果：

```html
<html>
<head>
    <script>
        // 代码有误:
        $('#testForm').on('submit', function () {
            alert('submit!');
        });
    </script>
</head>
<body>
    <form id="testForm">
        ...
    </form>
</body>
```

因为 JavaScript 在此执行的时候，`<form>` 尚未载入浏览器，所以 `$('#testForm)` 返回 `[]`，并没有绑定事件到任何 DOM 上。

所以我们**自己的初始化代码必须放到 `document` 对象的 `ready` 事件中，保证 DOM 已完成初始化：**

```html
<html>
<head>
    <script>
        $(document).on('ready', function () {
            $('#testForm).on('submit', function () {
                alert('submit!');
            });
        });
    </script>
</head>
<body>
    <form id="testForm">
        ...
    </form>
</body>
```

这样写就没有问题了。因为相关代码会在 DOM 树初始化后再执行。

由于 `ready` 事件使用非常普遍，所以可以这样简化：

```js
$(document).ready(function () {
    // on('submit', function)也可以简化:
    $('#testForm).submit(function () {
        alert('submit!');
    });
});
```

甚至还可以再简化为：

```js
$(function () {
    // init...
});
```

上面的这种写法最为常见。**如果你遇到 `$(function () {...})` 的形式，牢记这是 `document` 对象的 `ready` 事件处理函数。**

**可以反复绑定事件处理函数，它们会依次执行：**

```js
$(function () {
    console.log('init A...');
});
$(function () {
    console.log('init B...');
});
$(function () {
    console.log('init C...');
});
```

### 事件参数

有些事件，如 `mousemove` 和 `keypress`，我们需要获取鼠标位置和按键的值，否则监听这些事件就没什么意义了。所有事件都会传入 `Event` 对象作为参数，可以从 `Event` 对象上获取到更多的信息：

```js
$(function () {
    $('#testMouseMoveDiv').mousemove(function (e) {
        $('#testMouseMoveSpan').text('pageX = ' + e.pageX + ', pageY = ' + e.pageY);
    });
});
```

### 取消绑定

一个已被绑定的事件可以解除绑定，通过 `off('click', function)` 实现：

```js
function hello() {
    alert('hello!');
}

a.click(hello); // 绑定事件

// 10秒钟后解除绑定:
setTimeout(function () {
    a.off('click', hello);
}, 10000);
```

需要特别注意的是，下面这种写法是无效的：

```js
// 绑定事件:
a.click(function () {
    alert('hello!');
});

// 解除绑定:
a.off('click', function () {
    alert('hello!');
});
```

这是因为两个匿名函数虽然长得一模一样，但是它们是两个**不同**的函数对象，`off('click', function () {...})` 无法移除已绑定的第一个匿名函数。

**为了实现移除效果，可以使用 `off('click')` 一次性移除已绑定的 `click` 事件的所有处理函数。**

**同理，无参数调用 `off()` 一次性移除已绑定的所有类型的事件处理函数。**

### 事件触发条件

一个需要注意的问题是，事件的触发总是由用户操作引发的。例如，我们监控文本框的内容改动：

```js
var input = $('#test-input');
input.change(function () {
    console.log('changed...');
});
```

当用户在文本框中输入时，就会触发 `change` 事件。但是，**如果用 JavaScript 代码去改动文本框的值，将不会触发 `change` 事件**：

```js
var input = $('#test-input');
input.val('change it!'); // 无法触发change事件
```

有些时候，我们希望用代码触发 `change` 事件，**可以直接调用无参数的 `change()` 方法来触发该事件：**

```js
var input = $('#test-input');
input.val('change it!');
input.change(); // 触发change事件
```

**`input.change()` 相当于 `input.trigger('change')`，它是 `trigger()` 方法的简写。**

为什么我们希望手动触发一个事件呢？如果不这么做，很多时候，我们就得写两份一模一样的代码。

### 浏览器安全限制

在浏览器中，有些 JavaScript 代码只有在用户触发下才能执行，例如，`window.open()` 函数：

```js
// 无法弹出新窗口，将被浏览器屏蔽:
$(function () {
    window.open('/');
});
```

这些“敏感代码”只能由用户操作来触发：

```js
var button1 = $('#testPopupButton1');
var button2 = $('#testPopupButton2');

function popupTestWindow() {
    window.open('/');
}

button1.click(function () {
    popupTestWindow();
});

button2.click(function () {
    // 不立刻执行popupTestWindow()，3秒后执行:
    setTimeout(popupTestWindow, 3000);
});
```

当用户点击 `button1` 时，`click` 事件被触发，由于 `popupTestWindow()` 在 `click` 事件处理函数内执行，这是浏览器允许的，而 `button2` 的 `click` 事件并未立刻执行 `popupTestWindow()`，**延迟执行的 `popupTestWindow()` 将被浏览器拦截（不同浏览器对此反应不同，经测试，最新版 Chrome 依然会弹出新的窗口）。**

注：上面的代码中用到了 JavaScript 中的计时器，分为两类：

+ `setInterval(function,interval)`：**按照指定的周期** `interval`（以毫秒计）来调用函数或计算表达式。方法会不停地调用 `function`，直到 `clearInterval()` 被调用或窗口被关闭。

	`clearInterval()` 方法的常用语法如下：

	```js
	timer = setInterval(function,interval)；
	clearInterval(timer);
	```

+ `setTimeout(function,interval)`：在指定的毫秒数后调用函数或计算表达式。

