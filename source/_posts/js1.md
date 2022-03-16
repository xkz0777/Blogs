---
title: JavaScript 学习笔记（1）
date: 2021-07-22 14:43:29
tags: ['Web','Javascript']
img: /medias/featureimages/js1.jpg
author: xkz
categories: Web
summary: JS学习笔记之概述篇
---

# JavaScript 概述

JavaScript 是一种**解释型的脚本语言**，可以在独立安装的 JavaScript 解释器（如 Node.js）或嵌入在浏览器内核中的 JavaScript 解释器中执行。

注意：JavaScript 和 Java 是完全不同的两种语言（Java 为编译型语言）

JavaScript 是**面向对象的弱类型语言**，由数据来决定数据类型，通过操作 DOM（Document Object Model）来操控网页。

JavaScript 遵循 ECMAScript 标准，2015 年发布了 ECMAScript 6，简称 ES6 版本，是对 JavaScript 的大更新，之后的版本每年一更。

可以在 [MDN](https://developer.mozilla.org/zh-CN/docs/Web) 查询有关的帮助文档。

## 如何执行 JavaScript 代码

JavaScript 代码需要嵌在 HTML 代码中才能执行，有三种方式将 HTML 代码嵌入 HTML 代码中：

### 将代码嵌入在元素事件中

```html
<body>
    <button onclick="console.log('hello world')">
        输出Hello world.
    </button>
</body>
```

运行后可以得到图示按钮，点击后便可在控制台中看到 `hello world`。

<img src="1.png" alt="运行效果" style="zoom:50%;" />

注：`console.log()` 用于向控制台输出内容，类似于其他语言的 `print()`。

### 将代码写在 script 标签中

```html
<body>
    <script>
        document.write('欢迎')
    </script>
</body>
```

运行后页面中会显示欢迎。

### 独立文件

将 JavaScript 代码写在外部 `.js` 文件中，并在 \<head> 标签或 \<body> 标签中引入 \<script> 标签（在哪里引用取决于希望 JavaScript 什么时候执行），标签的 `src` 属性为 `.js` 文件的路径。

`.js` 文件如下：

```javascript
for (let i = 0; i < 10; ++i) {
    console.log(i);
}
```

\<head> 标签中内容如下

```html
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="JavaScript/1.js"></script>
</head>
```

可以发现浏览器控制台中依次输出数字 0 - 9

注意：使用该方法嵌入 JavaScript 代码时，**标签内不能写任何语句**（写了也没事，但是不会被执行）。

