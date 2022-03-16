---
title: JavaScript 学习笔记（2）
date: 2021-07-22 16:35:24
tags: ['Web','Javascript']
author: xkz
img: /medias/featureimages/js2.jpg
categories: Web
summary: JS学习笔记之语法篇（上）
---

# JavaScript 语法（上）

JavaScript 是大小写敏感的语言，在语句结束时可以使用分号（也可以不用，此时浏览器中负责执行 JavaScript 代码的引擎会自动在每个语句的结尾补上 `;`），注释与 Java 完全相同。

## 变量

用 `var` 关键字声明变量，如：

```javascript
for (var i = 0; i < 10;++i){
    console.log(i);
}
```

变量名只能包含字母、数字、下划线**以及** `$`，不能以数字开头，不能含有保留字，采用驼峰命名法。

注：

+ 起初，JavaScript 不要求用 `var` 关键字声明变量（同 Python），**这样声明的变量为全局变量**，带来的后果是，在同一个页面的不同的 JavaScript 文件中，如果都不用 `var` 声明，恰好都使用了变量 `i`，将造成变量 `i` 互相影响，产生难以调试的错误结果，因此这是不好的习惯；而使用 `var` 声明的变量则不是全局变量，它的范围被限制在该变量被声明的函数体内。为了修补 JavaScript 这一严重设计缺陷，ECMA 在后续规范中推出了 strict 模式，在 strict 模式下运行的 JavaScript 代码，强制通过 `var` 声明变量，未使用 `var` 声明变量就使用的，将导致运行错误。启用 strict 模式的方法是在 JavaScript 代码的第一行写上：

	```javascript
	'use strict';
	```

+ 在 ES6 中，引入了关键字 `let` 和 `const` 来替代 `var`，ES6 更推荐我们使用前面两者而不是使用 `var`，具体的原因读者可以自行搜索（在“函数”部分也有部分介绍）。

## 数据类型

用 `typeof()`  函数或是 `typeof + 变量`（此法 typeof 和变量之间有空格）可以获取数据类型，如：

```javascript
console.log(typeof 'hello world') // string
```

### 基本类型

#### number

有如下几种

```javascript
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法
-99; // 负数
0123; // 八进制
0xfffff; // 十六进制
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

#### string

用单引号或双引号括起来，如 `hello world`。里面的每个字符都是 Unicode 编码的，在内存中占 2 个字节，查看 Unicode 编码：

```javascript
"许".charCodeAt().toString(16) // 8bb8
```

字符串中可以使用转义字符如 `'\n'`，`'\r'`，`'\t'` 等。

此外，也可以使用反引号来表示**模板字符串**，如：

```javascript
const age = 20
console.log(`I am ${age}`) // I am 20
```

此外，反引号**还可以表示多行字符串**。

`length` 属性为字符串长度。

常见方法：（其中 RE 表示正则表达式（Regular Expression））

|         方法          |                             作用                             |
| :-------------------: | :----------------------------------------------------------: |
|     toUpperCase()     |                             大写                             |
|     toLowerCase()     |                             小写                             |
|       indexOf()       | 搜索指定字符串首次出现的位置，可以设置起始检索位置，不可以搭配 RE 使用 |
|     lastIndexOf()     |          搜索指定字符串最后一次出现的位置，其余同上          |
|       search()        | 搜索指定字符串首次出现的位置，不可以设置起始检索位置，可以搭配 RE 使用 |
| substring(start, end) |              子串，左闭右开，不可以接受负的索引              |
| substr(start, length) |                  子串，第二个参数是子串长度                  |
|   slice(start, end)   |                    子串，可以接受负的索引                    |
|        split()        | 按给定的字符串分隔字符串，如果没有参数，切割为单个字符，返回一个数组 |
|       replace()       | 替换首个匹配，可以使用 RE，不会改变调用它的字符串，返回新字符串 |
|       concat()        |                  连接字符串，等同于加运算符                  |
|        trim()         |                    删除字符串两端的空白符                    |
|       charAt()        |         返回指定下标位置的字符，和使用 `[]` 效果相同         |
|     charCodeAt()      |             返回指定下标位置字符的 Unicode 编码              |

#### boolean

做运算时，`true` 可以当作 1，`false` 可以当作 0，此外，JavaScript 把 `null`、`undefined`、`0`、`NaN` 和空字符串 `''` 视为 `false`，**其他值一概视为** `true`。

#### null

类似 Java 中的 `null` 和 Python 中的 `None`。

#### undefined

表示未定义，如未声明或声明后未初始化的变量。

### 引用类型

#### 数组（Array）

##### 创建方式

+ 使用构造函数：

```javascript
const arr = new Array(4, 5, 6)
for (const i of arr) {
    console.log(i)
}
```

+ 直接使用 `[]`：

```javascript
const arr = [1, 2, 3.14, 'Hello', null, true];
```

由上面可以看出，数组内的元素可以是任何类型。

JavaScript 中，**数组也是对象**，可以访问 `length` 属性获取数组长度（**该属性可以被修改**，即数组长度可变）。在 JavaScript 中，**数组访问越界不会有任何错误**：

```javascript
const arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```

同时，可以使用 `delete` 运算符来删除元素，但是**会在数组留下未定义的空洞**。

##### 常见的方法

|   方法    |                             作用                             |
| :-------: | :----------------------------------------------------------: |
| IndexOf() |                      搜索指定元素的位置                      |
|  slice()  |         返回数组的切片，区间左闭右开，不会改变原数组         |
|  push()   |                向末尾添加元素，返回新数组长度                |
|   pop()   |                  删除最后的元素，返回该元素                  |
| unshift() |                向头部添加元素，返回新数组长度                |
|  shift()  |                  删除第一个元素，返回该元素                  |
|  sort()   |                    排序，自定义排序见后面                    |
| reverse() |                             反转                             |
| splice()  | 可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素，返回删除的元素 |
| concat()  | 把当前的 `Array` 和另一个 `Array` 连接起来，并返回一个新的 `Array`，不会改变原数组 |
|  join()   | 把当前 `Array` 的每个元素都用指定的字符串连接起来，然后返回连接后的字符串 |

##### 常见的函数

|           函数            |                             作用                             |
| :-----------------------: | :----------------------------------------------------------: |
|        String(arr)        | 将 `arr` 数组转成字符串，每个元素间用逗号分隔，等价于 `arr.join(',')` |
|    Array.isArray(arr)     |                    判断 `arr` 是不是数组                     |
| Math.max.apply(null, arr) |    查找 `arr` 中的最大值（null 意为没有对象去调用该方法）    |
| Math.min.apply(null, arr) |                    查找 `arr` 中的最小值                     |

`slice()` 方法使用时，如果**不带参数，相当于复制数组**，**一直选取到结尾，可以省略第二个参数**，如果选取的元素离结尾近，可以用倒数的下标，如

```javascript
const a = ['A', 'B', "C"]
const b = a.slice(-2, -1) // 等价于 a.slice(a.length - 2, a.length - 1)
const c = a.slice(-2)
console.log(b) // ["B"]
console.log(c) // ["B", "C"]
```

`splice()` 使用示例：

```javascript
const arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];
// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook'); // 返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```

`concat()` 方法还可以接收任意个元素和 `Array`，并且自动把 `Array` 拆开，然后全部添加到新的 `Array` 里。

`join()` 方法使用示例：

```javascript
const arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```

如果 `Array `的元素不是字符串，**将自动转换为字符串后再连接**。

##### 关联数组

由于数组也是一个对象，因此它的每个元素的索引都是属性，我们**可以自定义属性（非数字）**，例如：

```javascript
const a = ['A', 'B', 'C'];
a.name = 'Hello';
for (const x in a) {
    console.log(x); // 0, 1, 2, name 
}
console.log(a.length); // 3
```

值得注意的是，此时的 `length` 属性只会算上数字索引，因此关联数组只能使用 `for in` 循环来进行遍历。

##### 多维数组

即 `Array` 套 `Array`（和 Python 很像）。

##### 遍历数组

+ 最简单：循环变量为数组下标

+ `for in` 循环：循环变量为**数组元素的索引（包括非数字索引）**

	```javascript
	var a = ['A', 'B', 'C'];
	a.name = 'Hello';
	for (var x in a) {
	  console.log(x); // '0', '1', '2', 'name'
	}
	```

+ `for of` 循环：循环变量为**数组的元素（不包括非数字索引对应的元素）**

	```javascript
	var a = ['A', 'B', 'C'];
	a.name = 'Hello';
	for (var x of a) {
	    console.log(x); // 'A', 'B', 'C'
	}
	```

+ `forEach()` 方法：见迭代方法。

#### Map

`Map` 是一组键值对的结构，具有极快的查找速度（类似 Python 的字典）。

定义 `Map`：使用构造函数

```javascript
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95
```

此时传入一个**二维数组**作为参数，也可以初始化一个空 `Map`（不带任何参数）

常见方法：

|      方法       |                             作用                             |
| :-------------: | :----------------------------------------------------------: |
| set(key, value) | 添加键值对，当 `key` 已经存在，会用新的 `value` 覆盖现有的 `value` |
|    get(key)     |                  获取 `key` 对应的 `value`                   |
|   delete(key)   |                        删除对应键值对                        |

#### Set

和 STL 中的 `std::set` 差不多，是一组不重复元素的集合

要创建一个 `Set`，需要提供一个 `Array` 作为输入，或者直接创建一个空 `Set`：

```javascript
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3, 3]); // 含1, 2, 3, 重复的3被过滤
```

常见方法：

|    方法     |   作用   |
| :---------: | :------: |
|  add(key)   | 添加元素 |
| delete(key) | 删除元素 |

#### 迭代方法

ES6 规定，`Array`、`Map`、`Set` 都属于 `iterable`（可迭代）类型，可迭代类型可以用 `for of` 循环来遍历，同时还有五个迭代方法，每个方法接收两个参数，第一个参数为当前项要运行的 `function` ，第二个参数为运行 `function` 的作用域影响 `this` 的值，一般用不到。以下的示例中均只传入 `function` 参数，其中回调函数（callback function） `function` 传入参数：第一个参数为迭代对象，第二个参数为迭代对象的索引（也即下标），第三个对象为数组本身，其中第二、三个参数都可以省略，用到的数组：

```javascript
const todos = [{
        id: 1,
        text: 'Take out trash',
        isCompleted: true
    },
    {
        id: 2,
        text: 'Meeting with boss',
        isCompleted: true
    },
    {
        id: 3,
        text: 'Dentist appointment',
        isCompleted: false
    }
]
```

+ forEach()：给定数组每一项运行 `function`，没有返回值。

	```javascript
	todos.forEach(function (todo) {
	    console.log(todo.text) // 'Take out trash', 'Meeting with boss', 'Dentist appointment'
	})
	```

+ map()：给定数组每一项运行 `function`，返回每次函数调用的结果组成的数组。

	```javascript
	const todoText = todos.map(function (todo) {
	    return todo.text;
	})
	console.log(todoText) 
	// ["Take out trash", "Meeting with boss", "Dentist appointment"]
	```

+ filter()：给定数组每一项运行 `function`，如果该函数会返回 `true`，则返回为 `true` 项组成的数组。

	```javascript
	const completedText = todos.filter(function (todo) {
	    return todo.isCompleted
	}).map(function (completed) {
	    return completed.text
	})
	console.log(completedText) // ["Take out trash", "Meeting with boss"]
	```

+ every()：给定数组每一项运行 `function`，如果该函数对每一项都返回 `true`，则返回值为 `true`，否则相反。

	```javascript
	console.log(todos.every(function (todo) {
	    return todo.isCompleted
	})) // false
	```

+ some()：给定数组每一项运行 `function`，如果该函数对某一项都返回 `true`，则返回值为 `true`，否则相反。

	```javascript
	console.log(todos.some(function (todo) {
	    return todo.isCompleted
	})) // true
	//lambda 写法：console.log(todos.some(todo => todo.isCompleted))
	```

`every()` 可以看作是任意，而 `some()` 是存在。

这里由于函数写成了参数，因此可以写成 lambda 表达式的形式（一行内可以表达），这里先给出例子，具体写法见“函数”部分。

#### 对象（Object）

JavaScript 的对象是一组由键 - 值组成的无序集合，以 `xxx: xxx` 形式声明，用 `,` 隔开。

注意，最后一个键值对不需要在末尾加 `,`。

例如：

```javascript
var person = {
    name: 'Bob',
    age: 20,
    tags: ['js', 'web', 'mobile'],
    city: 'Beijing',
    hasCar: true,
    zipcode: null
};
```

其中键又称为对象的属性，访问属性通过 `.` 操作符来完成，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须用 `''` 括起来。

访问不存在的属性会返回 `undefined`，JavaScript 的对象是动态类型，可以任意给一个对象添加或删除属性。

可以用 `in` 操作符检测对象是否有某一属性，此时应该注意，对象的**每个属性都是字符串类型**，但属性对应的值可以是任意数据类型。

```javascript
var xkz = {
    name: 'xkz',
    'middle-school': 'No.5 Middle School'
};
xkz['name'] // 'xkz'
xkz.name // 'xkz'
xkz.middle-school // Error
xkz.'middle-school' // Error
xkz['middle-school'] // 'No.5 Middle School'

xkz.age = 20 // 新增 age 属性
delete xkz.age //删除 age 属性
delete xkz['name'] 

'name' in xkz // true
'toString' in xkz // true
```

其中 `toString` 属性是继承自 `Object` 的，要判断一个属性是否是某个对象自身拥有的，而不是继承得到的，可以用 `hasOwnProperty()` 方法：

```javascript
xkz.hasOwnProperty('name') // true
xkz.hasOwnProperty('toString') // false
```

对象的解构赋值：

```javascript
const person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    address: {
        street: '50 main st',
        city:'Boston'
    }
}

const {
    firstName,
    lastName,
    address: {
        city
    }
} = person
console.log(city) // 'Boston'
```

如果接收值的变量与对象属性不同名，可以使用如下操作：

```javascript
const {
    firstName: fName
} = person

console.log(fName) // 'John'
```

注意此时 `firstName` 不是变量，`fName` 才是变量。

使用解构赋值时，如果对应的属性不存在，变量将被赋值为 `undefined`，此时可以使用默认值来避免了不存在的属性返回 `undefined` 的问题：

```javascript
const {
    gender,
    married = false
} = person

console.log(gender, married) // undefined false
```

注：对数组也可以有解构赋值操作，例如：

```javascript
const [x, y, z] = ['hello', 'JavaScript', 'ES6'];
console.log(x, y, z) // hello JavaScript ES6
```

该方法可以用于交换两个变量的值：

```javascript
let x = 1, y = 2;
[x, y] = [y, x];
```

#### 正则表达式（RegExp）

正则表达式的语法在所有支持 RE 的语言中都相同，如下（其中顿号 `、` 表示或）：

| 操作符 |                             匹配                             |                             示例                             |
| :----: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   .    | 除了 `'\r'`、`'\n'` 以外的任何字符，在小括号、中括号表达式中，只会匹配自身，如 `[.]` 只会匹配 `.` 字符 |                  `(\d.)` 表示 `1.`、`2.` 等                  |
|   []   |                字符集，对单个字符给出取值范围                | `[abc]` 表示 `a`、`b`、`c`，`[a-z]` 表示 `a` 到 `z` 单个字符 |
|  [^]   |               非字符集，对单个字符给出排除范围               |         `[^abc]` 表示除`a`、`b`、`c` 以外的单个字符          |
|   *    |                 前一个字符 0 次或无限次扩展                  |         `abc*` 表示 `ab`、`abc`、`abcc`、`abccc` 等          |
|   +    |                 前一个字符 1 次或无限次扩展                  |            `abc+` 表示 `abc`、`abcc`、`abccc` 等             |
|   ?    |                  前一个字符 0 次或 1 次扩展                  |                   `abc?` 表示 `ab`、`abc`                    |
|   \|   |                    左右表达式中的任意一个                    |                 `abc|def` 表示 `abc`、`def`                  |
|  {m}   |                     扩展前一个字符 m 次                      |                     `ab{2}c` 表示 `abbc`                     |
| {m,n}  |               扩展前一个字符 m 至 n 次（含 n）               |                `ab{1,2}c` 表示 `abc`、`abbc`                 |
|   ^    |               匹配字符串开头（与非字符集不同）               |            `^abc` 表示 `abc` 且在一个字符串的开头            |
|   $    |                        匹配字符串结尾                        |            `abc$` 表示 `abc` 且在一个字符串的结尾            |
|   ()   |              分组标记，内部只能使用 \| 操作符               |      `(abc)` 表示 `abc`，(abc\|def) 表示 `abc`、`def`       |
|   \d   |                     数字，等价于 `[0‐9]`                     |                                                              |
|   \w   |               单词字符，等价于 `[A‐Za‐z0‐9_]`                |                                                              |
|   \s   |                             空格                             |                                                              |

例：

|                              RE                              |                 匹配                  |
| :----------------------------------------------------------: | :-----------------------------------: |
|                     Java((S\|s)cript)?                    |  `Java`、`JavaScript`、`Javascript`   |
| `^[A‐Za‐z]+$`（当 `^` 和 `$` 同时出现时，表示只能匹配 `^` 和 `$` 之间的内容） |       由 26 个字母组成的字符串        |
|            `^[A‐Za‐z0‐9]+$`（注意与`^\w+$`不同）             |    由 26 个字母和数字组成的字符串     |
|                          `^ ‐?\d+$`                          |           整数形式的字符串            |
|                      `[\u4e00‐\u9fa5]`                       |               中文字符                |

注意：正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符。例如，在待匹配文本为 `JavaScript`，正则表达式为 `Java(Script)?` 时，会匹配到 `JavaScript` 而不是 `Java`。

在 Python 中（乱入），RE 用 `r'正则表达式'` 即原生字符串的形式，亦或是普通字符串表示，在 JavaScript 中，有两种方式表示正则表达式：

+ `/正则表达式/`
+ `new RegExp('正则表达式')`

两种方法分别对应 Python 中的前两种表示方式，也即前者不需要注意字符串的转义问题，而后者需要，例如在表示 `\d` 时后者要用 `\\d` 表示。

用这两种方式都可以创建 `RegExp` 对象，常用方法：

+ `test(str)`

	返回 `str` 是否匹配 RE（`true` 或 `false`），例如：

	```javascript
	`/^\d{3}\-\d{3,8}$/.test('010-12345'); // true`
	```

+ `excc(str)`

	如果正则表达式中定义了组（`()` 内为一个分组），就可以在 `RegExp` 对象上用 `exec()` 方法提取出子串来。匹配成功返回 `Array`，第一个元素是正则表达式匹配到的整个字符串，后面的字符串表示匹配成功的子串。失败则返回 `null`，例如：

	```javascript
	const re = /^(\d{3})-(\d{3,8})$/;
	re.exec('010-12345'); // ['010-12345', '010', '12345']
	re.exec('010 12345'); // null
	```

这两个方法都可以指定模式，如 `'g'` 表示全局匹配，`'i'` 表示忽略大小写，`'m'`表示执行多行匹配。

正则表达式还可用于字符串方法中，通常用于 `search()` 和 `replace()` 方法：例如

```javascript
const str = "Welcome to USTC"
str.search(/ustc/i) // 11
```

#### Date

例如：

```javascript
const date = new Date('2019-3-29')
date.getFullYear() // 2019
```

#### Math

例如：

```javascript
const a = 10
const b = 20
console.log(Math.max(a, b)) // 20
```

还有常见的其他方法，如 `abs()`、`sin()`、`exp()`、`pow()`、`random()` 等，需要使用时可以查阅 [手册](https://www.w3school.com.cn/jsref/jsref_obj_math.asp)。

#### JSON

JSON 是 JavaScript Object Notation 的缩写，是一种数据交换格式。

###### 语法规则

+ 数据为键值对，其中数据类型有：number, string, boolean, null, array, object 共六种。键必须用 `""` 包裹起来，并用 `键:值` 的形式表示。

+ 数据由逗号分隔。

+ 大括号保存对象：

	```json
	{
	    "name":"Google", 
	    "url":"www.google.com"
	}
	```

+ 方括号保存数组：

	```json
	"sites":[
	    {"name":"Google", "url":"www.google.com"},
	    {"name":"Taobao", "url":"www.taobao.com"}
	]
	```

JavaScript 内置了 JavaScript 对象和 JSON 对象的转换方法：

JavaScript 对象转成 JSON 字符串：使用 `JSON.stringify()` 方法：

```javascript
const person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    address: {
        street: '50 main st',
        city: 'Boston'
    },
    todos: [{
            id: 1,
            text: 'Take out trash',
            isCompleted: true
        },
        {
            id: 2,
            text: 'Meeting with boss',
            isCompleted: true
        },
        {
            id: 3,
            text: 'Dentist appointment',
            isCompleted: false
        }
    ]
}

JSON.stringify(person) // {"firstName":"John","lastName":"Doe","age":30,"address":{"street":"50 main st","city":"Boston"},"todos":[{"id":1,"text":"Take out trash","isCompleted":true},{"id":2,"text":"Meeting with boss","isCompleted":true},{"id":3,"text":"Dentist appointment","isCompleted":false}]}
```

如果希望输出的字符串更漂亮一些（按缩进输出），可以使用：

```javascript
JSON.stringify(person, null, '\t')
```

得到

```json
{
	"firstName": "John",
	"lastName": "Doe",
	"age": 30,
	"address": {
		"street": "50 main st",
		"city": "Boston"
	},
	"todos": [
		{
			"id": 1,
			"text": "Take out trash",
			"isCompleted": true
		},
		{
			"id": 2,
			"text": "Meeting with boss",
			"isCompleted": true
		},
		{
			"id": 3,
			"text": "Dentist appointment",
			"isCompleted": false
		}
	]
}
```

第三个参数用于添加缩进，如这里使用 Tab 作为缩进。

第二个参数用于控制如何筛选对象的键值，如果我们只想输出指定的属性，可以传入 `Array`：

```javascript
JSON.stringify(person, ['firstName', 'lastName'], '\t')
```

得到：

```json
{
	"firstName": "John",
	"lastName": "Doe"
}
```

注意：如果**指定属性也是一个对象，需要数组中继续传入需要筛选的属性，否则会得到一个空对象**：

```javascript
JSON.stringify(person, ['address', 'todos'], '\t')
```

得到：

```json
{
	"address": {},
	"todos": [
		{},
		{},
		{}
	]
}
```

```javascript
JSON.stringify(person, ['address', 'street', 'city', 'todos', 'text'], '\t')
```

得到：

```json
{
	"address": {
		"street": "50 main st",
		"city": "Boston"
	},
	"todos": [
		{
			"text": "Take out trash"
		},
		{
			"text": "Meeting with boss"
		},
		{
			"text": "Dentist appointment"
		}
	]
}
```

第二个参数还可以传入一个函数，这样对象的每个键值对都会被函数先处理，如果值依然是对象，会递归地进行处理，例如：

```javascript
function convert(key, value) {
    if (typeof value === 'string') {
        return value.toUpperCase();
    }
    return value;
}

JSON.stringify(person, convert, '\t')
```

得到：

```json
{
	"firstName": "JOHN",
	"lastName": "DOE",
	"age": 30,
	"address": {
		"street": "50 MAIN ST",
		"city": "BOSTON"
	},
	"todos": [
		{
			"id": 1,
			"text": "TAKE OUT TRASH",
			"isCompleted": true
		},
		{
			"id": 2,
			"text": "MEETING WITH BOSS",
			"isCompleted": true
		},
		{
			"id": 3,
			"text": "DENTIST APPOINTMENT",
			"isCompleted": false
		}
	],
	"hobbies": [
		"MOVIES",
		"MUSIC"
	]
}
```

如果我们还想要精确控制如何序列化对象，可以定义一个 `toJSON()` 方法，直接返回 JSON 应该序列化的数据：

```javascript
const person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    address: {
        street: '50 main st',
        city: 'Boston'
    },
    todos: [{
            id: 1,
            text: 'Take out trash',
            isCompleted: true
        },
        {
            id: 2,
            text: 'Meeting with boss',
            isCompleted: true
        },
        {
            id: 3,
            text: 'Dentist appointment',
            isCompleted: false
        }
    ],
    hobbies: ['movies', 'music'],
    toJSON: function () {
        return {
            'name': `${this.firstName} ${this.lastName}`,
            'age': this.age
        }
    }
}
console.log(JSON.stringify(person))
```

得到：

```json
{"name":"John Doe","age":30}
```

JSON 字符串转成 JavaScript 对象：使用 `JSON.parse()` 方法：

```javascript
const person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    address: {
        street: '50 main st',
        city: 'Boston'
    },
    todos: [{
            id: 1,
            text: 'Take out trash',
            isCompleted: true
        },
        {
            id: 2,
            text: 'Meeting with boss',
            isCompleted: true
        },
        {
            id: 3,
            text: 'Dentist appointment',
            isCompleted: false
        }
    ],
    hobbies: ['movies', 'music'],
}
personJSON = JSON.stringify(person)
console.log(JSON.parse(personJSON))
```

得到：

```javascript
address:
	city: "Boston"
	street: "50 main st"
	[[Prototype]]: Object
age: 30
firstName: "John"
hobbies: Array(2)
	0: "movies"
	1: "music"
	length: 2
	[[Prototype]]: Array(0)
lastName: "Doe"
todos: Array(3)
	0: {id: 1, text: "Take out trash", isCompleted: true}
	1: {id: 2, text: "Meeting with boss", isCompleted: true}
	2: {id: 3, text: "Dentist appointment", isCompleted: false}
	length: 3
	[[Prototype]]: Array(0)
[[Prototype]]: Object
```

`JSON.parse()` 还可以接收一个函数，用来转换解析出的属性：

```javascript
var obj = JSON.parse('{"name":"Jack","age":14}', function (key, value) {
    if (key === 'name') {
        return `student ${value}`;
    }
    return value;
});
console.log(JSON.stringify(obj)); // {"name":"student Jack","age":14}
```

### 数据类型转换

有以下全局函数可以进行转换：

+ String()
+ Number()
+ parseInt()
+ parseFloat()

还有 `toString()` 等方法。

## 运算符、表达式

和 Java 几乎一致，不同点：相等运算符 `==` 和恒等运算符 `===`：前者在比较时，**只比较数值，不会比较数据类型**，而后者两个都会比较，因此一般我们使用后者来判断相等。

此外，值得注意的是，`NaN` 与所有其他值都不想等，包括自己，唯一能判断 `NaN` 的方法是 `isNaN()` 函数。

## 条件判断、循环

和 Java 不能说一模一样，只能说完全相同，不过 JavaScript 的 `for in` 循环比较奇怪：循环得到的是一个对象的所有**属性**，例如：

```javascript
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    console.log(key); // 'name', 'age', 'city'
}
```

由于 `Array ` 也是对象，而它的**每个元素的索引被视为对象的属性**，因此，`for in` 循环可以直接循环出 `Array` 的索引，但**不包括** `length` **属性**。

