---
title: CSS学习笔记
date: 2021-07-22 14:43:22
author: xkz
tags: 
 - CSS
 - Web
categories: Web
img: /medias/featureimages/css.jpg
summary: CSS学习笔记
---

# CSS 学习笔记

## CSS 概述

CSS：Cascading Style Sheets，层叠样式表。

在早期的 Web 技术中，网页的内容和样式都由 HTML 来确定，标签的属性用来确定样式，但随着 Web 技术的发展，HTML 只用来确定网页的内容，而由 CSS 来决定内容的样式，这样**使得内容和样式相分离，便于修改样式**，如可以简单的修改网站主题、对网站进行换肤等。

## CSS 的三种添加方式

### 行内添加

即**为每个标签增加 `style` 属性**，属性内为大括号中的内容。

例：

```html
<p style="color:red; font-size: 20px;">
    行内添加
</p>
```

可以把该段落内的文字设成字体大小为 20 像素，颜色为红色。

### 内嵌样式

**把所有的样式写在 &lt;style&gt; 标签内，并将该标签放在 html 文件的 &lt;head&gt; 标签部分**，例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        p {
            color: red;
            font-size: 20px;
        }
    </style>
</head>
<body>
    <p>内嵌添加</p>
</body>
</html>
```

前面的两种方法**无法重用公共的 CSS 代码**，当页面很多时，会导致每个文件都很大，而且难以维护（修改时，相同的样式需要全部找出来一个个修改），因此推荐单独写 `.css` 文件（即下面的第三种方法）。

### 链接样式（单独文件）

例：在站点文件夹中新建 `css` 目录，并在其中创建 `style.css` 文件，文件内容如下（也就是基本语法部分）：

```css
p {
    color:red;
    font-size: 20px;
}
```

之后在 head 标签部分添加：

```html
<link rel="stylesheet" href="css/style.css">
```

其中 `href` 属性应该为对应 `css` 文件的相对路径。

该方式的优点：

+ 实现 HTML 代码和 CSS 代码的**完全分离**，使得同一份样式可以在不同的 HTML 文件中使用。
+ 可以**复用公共的 CSS 代码**，减小了文件大小，同时也**便于维护**。
+ 可以**在同一个 HTML 文档内部引用多个 css 文件**
+ 可以使用到浏览器的缓存机制，从而加快网页的加载速度，提高用户的体验。

注：其实单独文件时也可以采用导入式，但不如链接式来得好，有兴趣的读者可以自行搜索。

三种方式添加的优先级：遵循**就近原则**，即**行内样式 > 内嵌样式 > 链接样式**。

## CSS 选择器（selector）

基本语法如下：（最后一行可以不用添加分号）

```css
选择器 {
	属性名1: 属性值1; /*注释*/
    属性名2: 属性值2;
}
```

注：有些地方称选择器为选择符。

### 选择器类型

#### 标签（元素）选择器

如之前的 p 即为标签类型的选择器，其他标签也可以作为选择器，如：

```css
h1 {
    color: red;
    font-size: 12px;
}
```

#### class 选择器

使用 `.` 声明该选择器，如

```css
.one {
    font-size: 20px;
}
```

之后在需要使用该样式的标签内引入属性 `class="one"`（使用时并不带 `.`），例如：

```html
<p class="one">类别一段落</p>
```

注意，**class 选择器可以匹配多个标签**，也即声明 `one` 选择器后，可以在多个标签中使用 `class="one"` 样式，如还可以使用：

```html
<h1 class="one">类别一标题</h1>
```

同时，一个标签可以设置多个 `class` 属性，如

```html
<p class="red bold">段落</p>
```

#### id 选择器

使用 `#` 声明该选择器，如：

```css
#one {
    font-size: 12px;
}
```

之后**使用时只能唯一匹配某个标签**（使用时也不带 `#`），例如：

```html
<p id="one">id_one</p>
```

每个 id 在页面中只能使用一次（区别于 class 选择器）

也可以和元素选择器一起使用：

```css
p#one {
    font-size: 12px;
}
```

#### 通配选择器

使用 `*` 声明该选择器：

```css
* {
    color: red;
}
```

能设置所有元素的样式。

### 选择器的复合

#### 交集选择器

类别选择器可以和标签选择器复合，如：

```css
p.one {
    color: red;
}
.one {
    font-style: italic;
}
```

`p.one` 意为同时满足是 &lt;p&gt; 标签和 `class` 属性为 `one` 的元素才会被设置样式。此时应用 `one` 选择器时，所有带 `class="one"` 属性的标签都会变成斜体，但是**只有 p 标签会变成红色**，例如：

```html
<h1 class="one">一级标题</h1>
<p class="one">段落部分</p>
```

效果图：

<img src="C:/Users/82137/AppData/Roaming/Typora/draftsRecover/1.png" alt="效果图" style="zoom:50%" />

注意，交集选择器中如果有元素选择器，必须使用元素选择器开头，选择器之间不加任何符号（也没有空格）。

还可以有更复杂的嵌套，和下面介绍的关系选择器嵌套如下：

```css
p {
    font-family:helvetica, sans-serif; font-size:1.2em;
}

.specialtext {
    font-style: italic;
}

p.specialtext {
    color:red;
}

p.specialtext span {
    font-weight:bold;
}
```

HTML 部分：

```html
<h1 class="specialtext">标题部分</h1>
<p class="specialtext">
    普通段落部分
    <span>
        被span标签包裹的部分
    </span>
</p>
```

此时标题部分只会变成斜体（第二个选择器），普通段落会被前三个选择器作用，&lt;span> 标签包裹的部分会被四个选择器叠加作用，效果图：

<img src="C:/Users/82137/AppData/Roaming/Typora/draftsRecover/2.png" alt="效果图" style="zoom:50%;" />

#### 并集选择器（选择器集体声明）

例：

```css
p, h1 {
    font-size: 20px;
}
```

只要**在需要集体声明的选择器名间加逗号**即可。

### 关系选择器

+ 后代选择器：`祖先标签 样式作用的标签`（注意空格）：

```css
p span {
    color: red;
}
```

只要**在不同层级的标签之间添加空格**即可，将会为后代设置样式（例如这里的 &lt;span&gt;）。

注意：这里只要求**祖先**标签在前，**而不是严格的父标签**，并且可以多级迭代，如：

```css
body p span {
    color: red;
}
```

如果要求是严格的父标签，则应该使用如下格式：

+ 子代选择器：`父标签>样式作用的标签`：

```css
p>span {
    color: red;
}
```

+ 相邻兄弟（adjacent-sibling）选择器：`前一个标签+样式作用的相邻同级标签`：

```css
h2+p {
    font-size: 20px;
}
```

此时，**只有紧邻在 &lt;h2> 标签后的同级 &lt;p> 标签会被该样式作用**。

+ 兄弟（general-sibling）选择器：`某标签~样式作用的同级标签`：

```css
h2~p {
    font-size: 20px;
}
```

此时，只要某个 &lt;p> 标签出现在同级的 &lt;h2> 标签后（未必相邻），就会被该样式作用。

### 属性选择器

一共有五种形式：

+ `[属性名]`：选择含有指定属性的元素
+ `[属性名=属性值]`：选择含有指定属性和属性值的元素
+ `[属性名^=属性值]`：选择属性值以指定值开头的元素
+ `[属性名$=属性值]`：选择属性值以指定值结尾的元素
+ `[属性名*=属性值]`：选择属性值中含有某值的元素的元素

例如：

```css
p[title*=e] {
    color: orange;
}
```

### 伪类选择器（Pseudo-classes）

伪类即不存在的元素，用于选中如第一个子元素、被点击的元素、鼠标移入的元素这样的元素。

伪类一般情况下使用 `:` 开头，例如：

+ `:first-child`：第一个子元素
+ `:last-child`：最后一个子元素
+ `:nth-child()`：选中第 n 个子元素
	+ 取数字时，即选中第 n 个子元素（n 从 1 开始）
	+ 取 `n` 时选中所有元素；取 `2n` 或 `even` 选中偶数位的元素；取 `2n+1` 或 `odd` 表示选中奇数位的元素。

还有 `:first-of-type`、`:last-of-type`、`:nth-of-type()` 等选择器，区别为前面三种选择器是根据所有子元素进行排序，后面三	种在同类型元素中进行排序。

+ `:not()`：将符合条件的元素从选择器中去除。

例如：

```css
ul>li:not(:nth-of-type(3)) {
    color: yellowgreen;
}
```

将选中无序列表中除了第三个元素以外的元素。

特别地，关于 &lt;a&gt; 标签有特别的伪类选择器：

+ `a:link`：表示未访问过的链接
+ `a:visited`：表示访问过的链接
+ `a:hover`：用来表示鼠标移入的状态
+ `a:active`：用来表示鼠标点击状态

注意：如果需要这些样式，必须按照上面的顺序来写，否则会出现前面的样式被覆盖的情况。

### 伪元素选择器（Pseudo-elements）

伪元素使用 `::` 开头，例如：

+ `::first-letter` 表示第一个字母

+ `::first-line`：表示第一行
+ `::selection`：表示选中的内容
+ `::before`：元素的开始 
+ `::after`：元素的最后

其中，`::before` 和 `::after` 必须结合 `content` 属性来使用，例如：

```css
div::before {
    content: '『';
}

div::after {
    content: '』';
}
```

## 样式的继承和优先级

### 继承

`font-size`、`color` 等样式，子元素没有特别设置时，将会继承父元素的样式，但 `background-color` 等样式不会被继承，具体可以查阅文档。

### 优先级

样式的优先级为：

行内样式 > id 选择器 > 类和伪类选择器 > 标签（元素）选择器 > 通配选择器 > 继承样式

也就是样式设置的范围越大，其优先级就越低。

可以在某个样式后面添加 `!important`，则此时该样式会获取到最高的优先级，但开发时应该尽量少使用，因为这将使得代码的维护变得困难。

## 单位

### 长度单位

+ 像素

+ 百分比

	将属性值设为父元素的百分比

+ em

	em 是相对于元素的字体大小来计算的，1em = 1font-size

+ rem

	rem 是相对于根元素的字体大小来计算的

### 颜色

+ 直接使用颜色名，如 `red`、`orange` 等。

+ RGB 和 RGBA，例：

	rgb(255, 0, 0) 表示红色

	rgba(255, 0, 0, .5) 设置半透明

	`#ff0000` 也可以表示红色，当两两重复时可以简写为 `#f00`

+ HSLA 值（用的很少）

	H：色相；S：饱和度；L：亮度。

## 文档流

CSS 是“层叠样式表”，意为其对网页是一层一层设置样式，最终叠在一起的，用户看到的是最上面的一层。

在这些层中，最底下的一层称为文档流（normal flow），文档流是网页的基础，元素默认都是在文档流中进行排列。

### 文档流中元素的特点

#### 块元素

+ 块元素会在页面中独占一行，并自上向下垂直排列
+ 默认宽度是父元素的全部（会把父元素撑满）
+ 默认高度是被内容（子元素）撑开

#### 行内元素

不会独占一行，自左向右水平排列。默认宽度和高度都被内容撑开。

### 脱离文档流的特点

#### 块元素

块元素不再独占页面的一行，宽度和高度默认都被内容撑开。

#### 行内元素

会变成块元素，特点和块元素一样。

即脱离文档流后便不再区分块元素和行内元素

## 盒子模型

在 CSS 中，每个元素都是一个盒子，包含以下几个部分：

- 内容区（content）
- 内边距（padding）
- 边框（border）
- 外边距（margin）

示意图如下所示：

<img src="../images/css/box-model.gif" style="zoom:75%;" />

将元素设置为矩形的盒子后，对页面的布局就变成将不同的盒子摆放到不同的位置。

### 内容区

`width` 和 `height` 用来设置内容区的大小。

### 内边距

`padding` 设置内边距。

内容区的背景颜色会延伸到内边距上。

### 边框

`border-width` 属性用来设置宽度，默认为 3px。可以设多个值用来指定四个方向边框的宽度值（或者分别用 `border-top-width` 等分别指定，但这样太麻烦）。

在有多个值时，指定的顺序依次为上、右、下、左（顺时针）。

`border-color` 用来设置边框颜色，如果没有则自动使用 `color` 的颜色值。

`border-style` 用来设置边框样式，可能取值：

- `solid`：表示实线
- `dotted`：点状虚线
- `dashed`：虚线
- `double`：双线

`border` 属性可以简写，设置边框所有的相关样式（**没有顺序要求**），例如：

```css
border: solid 10px orange;
```

通常使用的方式为，先用 `border` 设置大致的样式，再用具体的 `border-top` 等设置细节。

盒子的可见框的大小，由内容区、内边距和边框共同决定。但是可以用 `box-sizing` 设置盒子尺寸的计算方式。可选值：

+ `content-box`：默认值，width 和 height 用来设置内容区的大小
+ `border-box`：width 和 height 用来设置整个盒子可见框的大小

### 外边距

用 `margin` 设置外边距，外边距不会影响盒子可见框的大小，但是会影响盒子的位置。

`margin` 可以设置负值，元素将向相反方向移动。

### 水平方向布局

元素在水平方向必须满足如下等式：

margin + border + padding + width = 父元素宽度

如果等式不成立，称为过度约束，如果 width 和 margin-left、margin-right 中某个值为 auto，则会自动调整为 auto 的那个值以使等式成立，如果没有，则会自动调整 margin-right。

利用这个特性，可以用 `margin: 0 auto;` 使元素在父元素中居中。

### 垂直方向布局

#### 溢出

垂直方向没有上面的等式，如果子元素的 border + padding + height（即子元素的大小）超过了父元素的高度，会从父元素中溢出，可以用 `overflow` 属性来设置父元素如何处理溢出的子元素。可选值：

+ `visible`：默认值，将显示溢出部分
+ `hidden`：将裁减溢出内容
+ `scroll`：生成两个滚动条
+ `auto`：根据需要生成滚动条

#### 外边距的重叠

**相邻**的垂直外边距会出现重叠现象。

**兄弟元素间**的相邻垂直外边距会取两者之间的较大值（两者都是正值）。

如果相邻的外边距一正一负，则取两者的和；如果相邻的外边距都是负值，则取两者中绝对值较大的。

兄弟元素之间外边距的重叠对于开发是有利的，不需要进行处理。

**父子元素间**相邻外边距，子元素的会传递给父元素（上外边距）

父子外边距的重叠会影响到页面的布局，必须要进行处理。

处理方式：

+ 让外边距不再重叠。此时可以选择给父元素添加 border-top。
+ 不设置子元素的 margin，改为设置父元素的 padding。

前面两者都会改变父元素的盒子大小，都需要重新设置父元素大小，后面会介绍更好的解决方式。

### 行内元素的盒子模型

+ 行内元素不支持设置宽度和高度
+ 行内元素可以设置 padding、border、margin，但是垂直方向的 padding、border、margin 不会影响页面的布局

可以用 `display` 属性设置元素显示的类型，可选值：

+ `inline`：将元素设置为行内元素
+ `block`：将元素设置成块元素
+ `inline-block`：将元素设置成行内块元素。行内块元素兼具二者特点，既可以设置宽高又不会独占一行（类似于图片）
+ `table`：将元素设置为表格
+ `none`：元素不在页面中显示

注意，`visibility` 属性也可用于设置元素的显示状态，可选值为 `visible`（默认值）和 `hidden`。

`display: none` 会使元素彻底消失，而 `visibility: none` 只是会使元素隐形，元素依然会占据原来的空间。

### 阴影、轮廓和圆角

`box-shadow` 用来设置元素的阴影效果，阴影不会影响页面布局。例如：

`box-shadow: 10px 10px 50px rgba(0, 0, 0, .3);`，前两个值为偏移量，第三个值是模糊半径，第四个值为阴影颜色。

`outline` 用来设置元素的轮廓线，和 border 用法相同，但不会影响盒子可见框大小。

`border-radius` 用来设置圆角，例如 `border-radius: 50%;` 可以把元素设为一个圆形。

## 浮动

浮动一开始是为了让文字能够环绕在块元素的周围，但后面也用于设置水平布局。

通过 `float` 属性可以设置元素浮动，可选值：

`none`：默认值，元素不浮动

`left`：元素向左浮动

`right`：元素向右浮动。

### 浮动的特点

+ 元素设置浮动以后，水平布局的等式便不需要强制成立。

+ 设置浮动会使元素**从文档流中脱离**，不再占用文档流的位置，所以元素下边的还在文档流中的元素会自动向上移动。
+ 浮动以后元素会向父元素的左侧或右侧移动，但浮动元素默认**不会从父元素中移出**。
+ 浮动元素向左或向右移动时，**不会超过它前边的其他浮动元素**。
+ **如果浮动元素的上边是一个没有浮动的块元素，则浮动元素无法上移**。
+ 浮动元素不会盖住文字，文字会自动环绕在浮动元素的周围，所以我们可以利用浮动来**设置文字环绕图片的效果**。

### 浮动缺陷

#### 高度塌陷

因为浮动将使得子元素脱离文档流，其父元素的高度不再被子元素撑开，会产生**高度塌陷**问题。

解决方式：

+ 给父元素设置固定高度，但这会使得当子元素的高度变化时，父元素的高度不能跟着变化，不好。
+ 开启父元素的 BFC，后面会具体介绍。

#### 后面的非浮动元素上移

可以通过 `clear` 属性来清除它**之前**（按照 DOM 顺序）的浮动元素对当前元素所产生的影响，可选值：

`left`：清除左侧浮动元素对当前元素的影响

`right`：清除右侧浮动元素对当前元素的影响

`both`：清除两侧中最大影响的那侧

#### clearfix

用如下的 clearfix 可以同时解决高度塌陷和外边距重叠问题：

```css
.clearfix::before,
.clearfix::after{
    content: '';
    display: table;
    clear: both;
}
```

原理为，设置伪元素 `before` 隔开父元素和设置了外边距的子元素，使二者不相邻，便可以解决外边距重叠问题，`display: table` 是为了防止空元素占据空间。设置伪元素 `after` 清空了它之前所有元素浮动的影响，使得 `after` 排列在最后一个元素的下面，因此可以撑开父元素，便可以解决高度塌陷问题。

事实上不使用伪元素，而在源代码中手动添加两个空的 &lt;div&gt; 在最前面和最后面也可以，但是这使用结构来解决样式，不符合结构和样式分离的设定，维护起来也不方便。

### BFC

BFC（Block Formatting Context) 块级格式化环境，是 CSS 中的一个隐含属性（也即没有 `bfc` 属性，也无法直接设置其开启）。

开启 BFC 后的元素会变成一个独立的布局区域，特点：

+ **不会被浮动元素所覆盖**

+ **子元素和父元素外边距不会重叠**（可以很好地解决之前的问题）

+ **可以包含浮动的子元素**（此时父元素的高度会被浮动的子元素撑开）

开启 BFC 的方式：

+ 浮动
+ 行内块元素（inline-block）
+ 绝对定位（position 为 absolute 或 fixed，在后面会讲到）
+ overflow 不为 visible 和 clip
+ display: flow-root

其中，使用 `display: flow-root` 方式可以开启元素的 BFC 而不产生任何其他影响，建议采用。

## 定位

定位通过 `position` 属性来设定，通过定位可以将元素摆放到页面的任意位置。

`position` 可选值为：

- `static`：默认值，不开启定位
- `relative`：相对定位
- `absolute`：绝对定位
- `fixed`：固定定位
- `sticky`：粘滞定位

### 相对定位

参照于元素在文档流中的位置进行定位，需要设置偏移量（offset），否则不会改变位置。

偏移量通过 `top`、`bottom`、`left` 和 `right` 设置，通常情况下只会水平方向使用一个，垂直方向使用一个。这些属性设置后，只有在定位开启时才会生效。

相对定位会提升元素的层级（比浮动层级更高），但不会使元素脱离文档流，也不会改变元素的性质（即块元素还是块元素，行内元素还是行内元素）。

相对定位的元素移动后，其原本在文档流中占据的位置依然存在（类似于灵魂出窍）。

### 绝对定位

- 开启绝对定位后，如果不设置偏移量元素的位置不会发生变化
- 开启绝对定位后，元素会从文档流中脱离
- 绝对定位会改变元素的性质，行内变成块，块的宽高被内容撑开
- 绝对定位会使元素提升一个层级
- 绝对定位元素是相对于其包含块进行定位的

包含块（containing block）正常情况下是离当前元素最近的祖先**块**元素。绝对定位的包含块是离它最近的**开启了定位的祖先元素**，如果所有的祖先元素都没有开启定位则根元素 &lt;html&gt; 就是它的包含块。

绝对定位是用的最多的定位，相对定位大多情况下都是配合绝对定位使用（因为需要确定绝对定位的包含块）。

开启绝对定位后，水平方向布局的等式需要修改：左边还要加上 left 和 right。left 和 right 也可以设为 auto，没有 auto 会默认跳转 right，右边变为包含块的**内容区宽度**。

垂直方向需要满足等式：top + margin + border + padding + height = 包含块高度。

### 固定定位

相对于浏览器视口（viewport）进行定位，不会随网页滚动条滚动而移动。

### 粘滞定位

在元素到达某个位置时将其固定。

### 元素的层级

开启定位后的元素会提升层级，可以通过 `z-index` 属性指定元素层级。`z-index` 值为整数，值越大元素层级越高，越优先显示。

兄弟元素间，层级相同时，优先显示靠下的元素。

祖先元素的层级再高也不会盖住后代元素。

## 字体、背景和文本

### 字体

`color` 设置字体颜色，`font-size` 设置字体大小，`font-family` 设置字体。常见字体类别：

+ `serif`：衬线字体
+ `sans-serif`：非衬线字体
+ `monospace`：等宽字体
+ `cursive`：草书字体
+ `fantasy`：艺术字

指定字体的类别，浏览器会自动使用该类别下的字体。可以同时指定多个字体，使用 `,` 隔开，浏览器会优先使用前面的，如果不能用再找下一个。

对于用户没有的字体，还可以用 `font-face` 引入服务器上的字体给用户使用：

```css
@font-face {
    /* 指定字体的名字 */
    font-family:'myfont' ;
    /* 服务器中字体的路径 */
    src: url('./font/xxx.ttf') format("truetype");
}
```

### 图标字体（icon-font）

在网页中使用一些简单的图标时，如果用图片，具有体积较大、不灵活（如无法改变颜色）、会失真等缺点。因此可以直接使用图标字体。

常用的图标字体网站为 [Font Awesome](https://fontawesome.com/)。将其 css 和 webfonts 文件夹下载后引入到项目中，再把 `all.css` 作为外部 css 文件引入，就可以使用图标字体了。例如：

```css
<i class="fas fa-bell" style="font-size:80px; color:red;"></i>
```

也可以使用 HTML 实体：

```css
<span class="fas">&#xf0f3;</span>
```

可以到官网查询所需的图标。

还可以通过伪元素批量设置图标字体：

```css
li::before{
    content: '\f1b0';
    /* 如果是 fab 选下面这个 */
    /* font-family: 'Font Awesome 5 Brands'; */
    /* 如果是 fas 选下面的两个 */
    font-family: 'Font Awesome 5 Free';
    font-weight: 900; 
    color: blue;
    margin-right: 10px;
}
```

### 行高、加粗、斜体

行高通过 `line-height` 设置，是文字实际占有的高度，可以直接指定大小（px、em），也可以直接设置一个整数，将会是字体的指定倍数。字会处在行高的中间，上下平均分配。行高还可以用于设置文字行间距：行间距 = `line-height` - `font-size`。

加粗通过 `font-weight` 设置；斜体通过 `font-style` 设置。

### 字体的简写属性

`font: 加粗 斜体 字体大小/行高 字体族`，行高可以省略不写，如果不写使用默认值 1.33. 例如：

```css
font: bold italic 50px/2  微软雅黑, 'Times New Roman', Times, serif;
```

### 文本

`text-align` 文本的水平对齐

可选值：`left`、`right`、`center`、`justify`。

`vertical-align` 设置元素垂直对齐的方式

可选值：`baseline`（默认）、`top`、`bottom`、`middle`。

图片默认和边框直接有距离，可以设置 `vertical-align: bottom` 使其贴着边线。

`text-decoration` 文本修饰

可选值：`none`、`underline`、`line-through`、`overline` 


