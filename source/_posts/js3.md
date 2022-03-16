---
title: JavaScript 学习笔记（3）
date: 2021-07-22 16:36:21
tags: ['Web','Javascript']
author: xkz
img: /medias/featureimages/js3.jpg
categories: Web
summary: JS学习笔记之语法篇（下）
---

# JavaScript 语法（下）

## 函数

### 声明

```javascript
function 函数名(参数表){
	函数体
}
```

如果没有 `return` 语句，函数执行完毕后也会返回结果，只是结果为 `undefined`。

如：

```javascript
function add(num1, num2){
	return num1 + num2
}
```

由于 JavaScript 的**函数也是一个对象**，上述定义的 `add()`函数实际上是一个函数对象，而**函数名** `add` **可以视为指向该函数的变量**。

因此，函数还有等价的定义方式：

```javascript
var abs = function (x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
};
```

在这种方式下，`function (x) { ... }` 是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量 `abs`，所以，通过变量 `abs ` 就可以调用该函数。

### 调用

JavaScript **允许传入任意个参数而不影响调用**：

```javascript
abs(10, 'blablabla'); // 返回10
abs(-9, 'haha', 'hehe', null); // 返回9
```

传入的参数比定义的少也没有问题：

```javascript
abs(); // 返回NaN
```

此时 `abs(x)` 函数的参数 `x` 将收到 `undefined`。

注：判断 `x` 为 `undefined` 的方法：

+ `x === undefined`
+ `typeof(x) == "undefined"`

#### argument

调用时，函数内部有 `arguments` **变量指向当前函数的调用者传入的所有参数**，其使用方式类似于数组，如：

```javascript
function abs() {
    if (arguments.length === 0) {
        return 0;
    }
    var x = arguments[0];
    return x >= 0 ? x : -x;
}

abs(); // 0
abs(10); // 10
abs(-9); // 9
```

#### rest 参数

ES6 标准引入了 `rest` 参数，用于接收多余参数：

```javascript
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

`rest` 参数只能写在最后，前面用 `...` 标识，传入的参数先绑定 `a`、`b`，多余的参数以数组形式交给变量 `rest`，当然，用 `argument` 变量即可完成 `rest` 的功能，只是更麻烦一些。

### 变量作用域

用 `var` 声明的变量如果在函数体内，作用域为整个函数体。

```javascript
function foo() {
    var x = 1;
    x = x + 1;
}

x = x + 2; // ReferenceError! 无法在函数体外引用变量x
```

同时，无法声明仅在 for 循环内生效的块级作用域变量，因此 ES6 引入了 `let` 关键字来声明变量（用 `let` 和 `const` 声明的变量是块级作用域的，和 Java 更像）：

```javascript
function foo() {
    for (var i=0; i<100; i++) {
        //
    }
    i += 100; // 仍然可以引用变量i
}

function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    // SyntaxError:
    i += 1;
}
```

如果不声明在任何函数内，则该变量是全局作用域的，事实上，JavaScript 默认有一个全局对象 `window`，全局作用域的变量实际上被绑定到 `window` 的一个属性，直接访问全局变量 `course` 和访问 `window.course` 是完全一样的。

不同函数内声明的变量互不影响。

对于同名变量，用 `var` 声明的内部变量会**覆盖**（不是屏蔽）外部变量，而用 `let` 声明的变量则是屏蔽作用，例如：

```javascript
function test() {
    var varTest = 'test var.';
    let letTest = 'test let.';

    {
        var varTest = 'varTest changed.';
        let letTest = 'letTest changed.';
    }

    console.log(varTest); // "varTest changed."，内部"{}"中声明的varTest变量覆盖外部的letTest声明
    console.log(letTest); // "test let."，内部"{}"中声明的letTest和外部的letTest不是同一个变量
}
```

内部函数可以访问外部的变量。

#### 变量提升

用 `var` 声明的变量具有变量提升特性，例如，

```javascript
function foo() {
    var x = 'Hello, ' + y;
    console.log(x);
    var y = 'Bob';
}

foo();
```

上面的语句不会报错，因为变量提升特性使得该函数与下面的函数等价：

```javascript
function foo() {
    var y; // 提升变量 y 的声明，此时 y 为 undefined
    var x = 'Hello, ' + y;
    console.log(x);
    y = 'Bob';
}
```

不过输出时将得到 `Hello, undefined`，因为赋值不会被提升。

#### 命名空间

由于全局变量会绑定到 `window` 上，如果不同的 JavaScript 文件使用了相同的全局变量，或者定义了相同名字的顶层函数，都会造成命名冲突，并且很难被发现，解决方案是把所有变量和函数绑定在一个对象中，该对象的名字类似于 C++ 中的命名空间，如：

```javascript
// 唯一的全局变量MYAPP:
var MYAPP = {};

// 其他变量:
MYAPP.name = 'myapp';
MYAPP.version = 1.0;

// 其他函数:
MYAPP.foo = function () {
    return 'foo';
};
```

### this 和 apply()

```javascript
var xiaoming = {
    name: '小明',
    birth: 2002,
    age: function () {
        var y = new Date().getFullYear();
        return y - this.birth;
    }
};

xiaoming.age; // function xiaoming.age()
xiaoming.age(); // 19
```

此时 `this` 指向当前对象，即 `xiaoming`。

```javascript
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 2002,
    age: getAge
};

xiaoming.age(); // 19, 正常结果
getAge(); // NaN
```

`getAge()` 返回 `NaN` 的原因是，此时调用 `getAge()` 等价于调用 `window.getAge()`（在之前提到过）。

为了修复这个设计错误，ECMA 规定，在 strict 模式下让函数的 `this` 指向 `undefined`（依然没有让 `this` 指向正确的位置），因此，为了让 `this` 指向正确的位置，可以调用函数对象自身的 `apply()` 方法，它接收两个参数，第一个参数就是需要绑定的 `this` 变量，第二个参数是 `Array`，表示函数本身的参数，例如：

```javascript
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 2002,
    age: getAge
};

xiaoming.age(); // 19
getAge.apply(xiaoming, []); // 19
```

另一个与 `apply()` 类似的方法是 `call()`，唯一区别是：

+ `apply() `把参数打包成 `Array` 再传入
+ `call()` 把参数按顺序传入

比如调用 `Math.max(3, 5, 4)`

```javascript
Math.max.apply(null, [3, 5, 4]); // 5
Math.max.call(null, 3, 5, 4); // 5
```

对普通函数调用，我们通常把 `this` 绑定为 `null`。

### 装饰器

利用 `apply()` 方法，我们还可以动态改变函数的行为（可以修改内置函数）。

JavaScript 的所有对象都是动态的，即使内置的函数，我们也可以重新指向新的函数。

现在假定我们想统计一下代码一共调用了多少次 `parseInt()`，可以把所有的调用都找出来，然后手动加上 `count += 1`，也可以用我们自己的函数替换掉默认的`parseInt()`：

```javascript
'use strict';

var count = 0;
var oldParseInt = parseInt; // 保存原函数

window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};

parseInt('10');
parseInt('20');
parseInt('30');
console.log('count = ' + count); // 3
```

### 多行 return

由于 JavaScript 引擎自动在未加分号的行末加上分号的特性，如果 `return` 语句分成多行，可能会出错，错误写法：

```javascript
function foo() {
    return
        { name: 'foo' };
}
```

正确写法：

```javascript
function foo() {
    return { // 这里不会自动加分号，因为{表示语句尚未结束
        name: 'foo'
    };
}
```

此外，有些语句不加分号会出现奇怪的 bug（特别是在声明的时候），因此写代码时还是应该尽量都加上分号。

### Lambda 表达式

JavaScript 中的 lambda 表达式被称作 Arrow Function（箭头函数），但我习惯称之为 lambda 表达式，其通常用于定义匿名函数（作为回调函数，配合下面的高阶方法），例如：

```javascript
x => x * x
```

等价于

```javascript
function (x) {
    return x * x;
}
```

如果函数体有多条语句，则不能省略大括号和 `return` 语句，如果有多个参数或者没有参数，参数列表需要加 `()`，例如

```javascript
const f = (x, y) => {
    const z = x + y;
    return z ** 2;
}

console.log(f(2, 3)); // 25
```

如果要返回一个对象，就要注意，如果是单表达式，这么写的话会报错：

```javascript
// SyntaxError:
x => { foo: x }
```

因为和函数体的`{ ... }`有语法冲突，所以要改为：

```javascript
// ok:
x => ({ foo: x })
```

由 lambda 表达式定义出来的匿名函数修复了 `this` 的错误指向，例如：

```javascript
const obj = {
    birth: 1990,
    getAge: function () {
        const fn = function () {
            return new Date().getFullYear() - this.birth; // this指向 window 或 undefined
        };
        return fn();
    }
};

console.log(obj.getAge()) // NaN
```

而用 lambda 表达式定义的匿名函数：

```javascript
const obj = {
    birth: 1990,
    getAge: function () {
        const fn = () => new Date().getFullYear() - this.birth; // this 指向 obj对象
        return fn();
    }
};

console.log(obj.getAge()); // 31
```

由于 `this` 在箭头函数中已经按照词法作用域绑定了，所以，用 `call()` 或者 `apply()` 调用箭头函数时，无法对 `this` 进行绑定，即传入的第一个参数被忽略（也即没有必要调用 `call()` 或 `apply()`）：

```javascript
const obj = {
    birth: 1990,
    getAge: function () {
        var fn = () => new Date().getFullYear() - this.birth; // this 指向 obj对象
        return fn.apply(window, []);
    }
};

console.log(obj.getAge()); // 31
console.log(obj.getAge.apply(window, [])); // NaN，注意 getAge() 并不是箭头函数
```

### 高阶函数

高阶函数（Higher-order function），接收函数作为参数，在“数组部分”提到的五个迭代方法就是高阶函数，其他的高阶函数：

+ reduce()

	`Array` 的 `reduce()` 方法把一个函数作用在这个 `Array` 的所有元素 `[x1, x2, x3...]` 上，这个函数必须接收两个参数， `reduce()` 把结果继续和序列的下一个元素做累积计算，其效果就是：

	```javascript
	[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)
	```

	例如，使用 `reduce()` 对数组求和：

	```javascript
	const arr = [1, 3, 5, 7, 9];
	arr.reduce(function (x, y) {
	    return x + y;
	}); // 25
	```

+ sort()

	不带任何参数的 `sort()` 方法会**默认把所有元素先转换为 String** 再从小到大进行排序，因此无法用于数字排序，但 `sort()` 作为高阶方法，可以接收自定义的比较函数来实现自定义的排序，例如：

	```javascript
	const arr = [1, 10, 2, 20]
	console.log(arr.sort()) // [1, 10, 2, 20]
	console.log(arr.sort((x, y) => x - y)) // [1, 2, 10, 20]
	```

	其中自定义的排序方法接收两个参数，如果第一个参数应该排在第二个参数前面，返回负数，如果第二个参数应该排在第一个参数前面，返回正数，如果相对位置不应该改变，返回 `0`。

+ find()

	`find() ` 方法用于查找符合条件的第一个元素，如果找到了，返回这个元素，否则，返回 `undefined`：

	```javascript
	const arr = ['Apple', 'pear', 'orange'];
	console.log(arr.find(s => s.toLowerCase() === s)); // pear, 因为pear全部是小写
	console.log(arr.find(s => s.toUpperCase() === s)); // undefined, 因为没有全部是大写的元素
	```

+ findIndex()

	`findIndex() ` 和 `find()` 类似，也是查找符合条件的第一个元素，不同之处在于 `findIndex()` 会返回这个元素的索引，如果没有找到，返回 `-1`。

例：手写 `string2int()` 函数：

```javascript
function string2int(s) {
    const arrChar = Array.from(s);
    const arrNum = arrChar.map(x => x - '0');
    return arrNum.reduce((x, y) => x * 10 + y);
}
```

### 生成器（generator）

generator（生成器）是 ES6 标准引入的新的**数据类型**。一个 generator **看上去像一个函数**，但**可以返回多次**。

JavaScript 中的 generator 类似于 Python 的 generator。

generator 定义如下：

```javascript
function* foo(x) {
    yield x + 1;
    yield x + 2;
    return x + 3;
}
```

和函数不同的是，generator **由 `function*` 定义**（注意多出的 `*` 号），并且，**除了 `return` 语句，还可以用 `yield` 返回多次。**

例如：用 generator 编写产生斐波那契数列的函数：

```javascript
function* fib(max) {
    var
        t,
        a = 0,
        b = 1,
        n = 0;
    while (n < max) {
        yield a;
        [a, b] = [b, a + b];
        n ++;
    }
    return;
}
```

直接调用：

```javascript
fib(5); // fib {[[GeneratorStatus]]: "suspended", [[GeneratorReceiver]]: Window}
```

直接调用一个 generator 和调用函数不一样，**`fib(5)` 仅仅是创建了一个 generator 对象，还没有去执行它**。

调用 generator 对象有两个方法，一是**不断地调用 generator 对象的`next()`方法**：

```javascript
var f = fib(5);
f.next(); // {value: 0, done: false}
f.next(); // {value: 1, done: false}
f.next(); // {value: 1, done: false}
f.next(); // {value: 2, done: false}
f.next(); // {value: 3, done: false}
f.next(); // {value: undefined, done: true}
```

`next()` 方法会执行 generator 的代码，然后，**每次遇到 `yield x;` 就返回一个对象 `{value: x, done: true/false}`**，然后“暂停”。返回的 `value` 就是 `yield` 的返回值，`done` 表示这个 generator 是否已经执行结束了。如果 `done` 为 `true`，则 `value` 就是 `return` 的返回值。

当执行到 `done` 为 `true` 时，这个 generator 对象就已经全部执行完毕，不要再继续调用 `next()` 了。

第二个方法是直接用 `for of` 循环迭代 generator 对象，这种方式不需要我们自己判断 `done`：

```javascript
for (var x of fib(10)) {
    console.log(x); // 依次输出0, 1, 1, 2, 3, ...
}
```

generator 还有另一个巨大的好处，就是**把异步回调代码变成“同步”代码**。

没有 generator 时，用 Ajax 时需要这么写代码：

```javascript
ajax('http://url-1', data1, function (err, result) {
    if (err) {
        return handle(err);
    }
    ajax('http://url-2', data2, function (err, result) {
        if (err) {
            return handle(err);
        }
        ajax('http://url-3', data3, function (err, result) {
            if (err) {
                return handle(err);
            }
            return success(result);
        });
    });
});
```

回调越多，代码越难看。

有了 generator 后，用 Ajax 时可以这么写：

```javascript
try {
    r1 = yield ajax('http://url-1', data1);
    r2 = yield ajax('http://url-2', data2);
    r3 = yield ajax('http://url-3', data3);
    success(r3);
}
catch (err) {
    handle(err);
}
```

看上去是同步的代码，实际执行是异步的。

例：用 generator 写一个生成 `nextId()` 的函数：

```javascript
function* nextId() {
    let i = 1;
    while (true) {
        yield i;
        i++;
    }
}

x = nextId();
for (let i = 1; i < 100; ++i) {
    console.log(x.next().value); // 1 2 3...
}
```

## 面向对象

### 类和对象

在 ES6 中引入了类和对象的概念，使用 `class` 关键字可以声明一个类，其中 `constructor()` 方法为类的构造函数，在 `new` 语句中自动被调用：

```javascript
class Student {
    constructor(name) {
        this.name = name;
    }
    hello() {
        alert('Hello, ' + this.name + '!');
    }
}

const student = new Student('xkz');
student.hello(); // Hello, xkz!
```

#### 类的继承

用 `extends` 关键字进行继承（同 Java），子类的构造方法中，用 `super()` 来调用父类构造方法：

```javascript
class Student {
    constructor(name) {
        this.name = name;
    }
    hello() {
        alert('Hello, ' + this.name + '!');
    }
}

class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }
    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}

const xiaoming = new PrimaryStudent('xiaoming');
xiaoming.myGrade();
```

此时对象 `xiaoming` 已经获得了父类的 `hello()` 方法，当然我们也可以重写（override）该方法：

```javascript
class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用 super() 调用父类的构造方法!并且需要在第一行
        this.grade = grade;
    }
    myGrade() {
        alert('I am at grade ' + this.grade);
    }
    hello() {
        console.log('Hello, ' + this.name + '!');
    } // 在该方法中，可以使用 super.hello() 调用父类中的 hello() 方法
}

const xiaoming = new PrimaryStudent('xiaoming', 5);
xiaoming.hello() // Hello, xiaoming!（在控制台输出，而不是弹出对话框）
```

一切都和 Java 看起来是一样的，但在 ES6 之前，只能用原型、原型链来进行继承（这个我也没有学很懂）。

### 原型

首先需要搞清楚两个属性： `prototype` 和 `__proto__`。

在 JavaScript 中，每个函数都有 `prototype` 属性，每个对象（Object）都有 `__proto__` 属性，对象的 `__proto__` 保存着该对象的构造函数的 `prototype` 属性：

```javascript
function Test() {}
console.log(Test.prototype); // {constructor: ƒ}

const test = new Test();
console.log(test.__proto__); // {constructor: ƒ}
console.log(test.__proto__ === Test.prototype); // true
```

`prototype` 和 `__proto__` 属性**都是对象**，因此每个函数的 `prototype` 属性中都有 `__proto__` 属性（因为 `prototype` 是对象，而对象有 `__proto__` 属性），由此可以得到一条原型链：

```javascript
console.log(Test.prototype.__proto__ === Object.prototype); //true
```

注意，原型链的顶层为 `Object.prototype`：

```javascript
console.log(Object.prototype.__proto__); // null
```

可以通过设置 `__proto__` 属性来设置原型：

```javascript
const Student = {
    name: 'Robot',
    height: 1.2,
    run: function () {
        console.log(this.name + ' is running');
    }
};

const xiaoming = {
    name: '小明'
};
xiaoming.__proto__ = Student;
xiaoming.run(); // 小明 is running
```

相当于指定了小明的父类为 `Student`，但不建议这样使用，我们可以采用`Object.create()` 方法，传入一个原型对象，并创建一个基于该原型的新对象：

```javascript
const Student = {
    name: 'Robot',
    height: 1.2,
    run: function () {
        console.log(this.name + ' is running');
    }
};

const xiaoming = Object.create(Student);
xiaoming.name = '小明';
xiaoming.run(); // 小明 is running
```

我们有四种方法创建对象：

+ Object.create(原型)

+ 直接使用 `{}`

+ 定义一个类，并通过 `new` 关键字创建对象

+ 不定义类，直接使用构造函数（为了区分普通函数和构造函数，按照约定，**构造函数首字母应当大写，而普通函数首字母应当小写**）：

	```javascript
	function Student(name) {
	    this.name = name;
	    this.hello = function () {
	        alert('Hello, ' + this.name + '!');
	    }
	}
	
	const xiaoming = new Student('小明');
	xiaoming.name; // 小明
	xiaoming.hello(); // Hello, 小明!
	```

	注意：如果不写 `new`，这就是一个普通函数，它返回 `undefined`。但是，如果写了 `new`，它就变成了一个构造函数，它绑定的 `this `指向新创建的对象，并默认返回 `this`，也就是说，不需要在最后写 `return this;`。同时，由于 `Student()` 是 `xiaoming` 的构造方法，有：

	```javascript
	console.log(xiaoming.__proto__ === Student.prototype) // true
	```

	此外，如果为原型添加属性，`xiaoming` 也能够继承该属性：

	```javascript
	Student.prototype.age = 18
	console.log(xiaoming.age) // 18
	```

	还可以继续往上：

	```javascript
	Student.prototype.__proto__.school = 'No.5 Middle School'
	console.log(xiaoming.school) // No.5 Middle School
	```

	原型链为：

	```javascript
	xiaoming ----> Student.prototype ----> Object.prototype ----> null
	```

	由于 `Student.prototype.__proto__` 其实就是 `Object.prototype`，因此也可以直接为 `Object.prototype` 增加属性，并且该属性依然能被 `xiaoming` 访问到：

	```javascript
	Object.prototype.skills = 'Chinese'
	console.log(xiaoming.skills) // Chinese
	```

	在 `Object.prototype` 中增加的属性，可以认为是 `Object` 类的静态成员（类似于静态成员）。

验证某个对象是否有某个属性，用 `in` 关键字，有某个自己的属性（不是继承得到），用 `hasOwnProperty()` 方法（在“对象”中曾经提及）。此外，还有一些关于 `Function` 和 `Object` 的有趣结论：

```javascript
console.log(Function.__proto__ === Function.prototype); // true
// 这里 Function 既是函数（任意函数的构造函数），又是对象

console.log(Object.__proto__ === Function.prototype); // true
console.log(Object.__proto__ === Function.__proto__); // true
```

## 错误处理

JavaScript 使用 `try ... catch ... finally` 处理错误：

```js
var r1, r2, s = null;
try {
    r1 = s.length; // 此处应产生错误
    r2 = 100; // 该语句不会执行
} catch (e) {
    console.log('出错了：' + e);
} finally {
    console.log('finally');
}
console.log('r1 = ' + r1); // r1应为undefined
console.log('r2 = ' + r2); // r2应为undefined
```

运行后可以发现，输出提示类似“出错了：TypeError: Cannot read property 'length' of null”。

执行流程：

1. 先执行 `try { ... }` 的代码；
2. 执行到出错的语句时，后续语句不再继续执行，转而执行 `catch (e) { ... }` 代码，其中 `e` 为 `ERROR` 对象；
3. 最后执行 `finally { ... }` 代码。

注意，`catch` 和 `finally` 可以不必都出现。也就是说，`try `语句一共有三种形式：

完整的 `try ... catch ... finally`：

```js
try {
    ...
} catch (e) {
    ...
} finally {
    ...
}
```

只有 `try ... catch`，没有 `finally`：

```js
try {
    ...
} catch (e) {
    ...
}
```

只有 `try ... finally`，没有 `catch`：

```js
try {
    ...
} finally {
    ...
}
```

### 错误类型

JavaScript 有一个标准的 `Error` 对象表示错误，还有从 `Error` 派生的 `TypeError`、`ReferenceError` 等错误对象。我们在处理错误时，可以通过 `catch(e)` 捕获的变量 `e` 访问错误对象：

```js
try {
    ...
} catch (e) {
    if (e instanceof TypeError) {
        alert('Type error!');
    } else if (e instanceof Error) {
        alert(e.message);
    } else {
        alert('Error: ' + e);
    }
}
```

使用变量 `e` 是一个习惯用法，也可以以其他变量名命名，如`catch(ex)`。

### 抛出错误

程序也可以主动抛出一个错误，让执行流程直接跳转到 `catch` 块。抛出错误使用 `throw` 语句。

例如，下面的代码让用户输入一个数字，程序接收到的实际上是一个字符串，然后用 `parseInt()` 转换为整数。当用户输入不合法的时候，我们就抛出错误：

```js
var r, n, s;
try {
    s = prompt('请输入一个数字');
    n = parseInt(s);
    if (isNaN(n)) {
        throw new Error('输入错误');
    }
    // 计算平方:
    r = n * n;
    console.log(n + ' * ' + n + ' = ' + r);
} catch (e) {
    console.log('出错了：' + e);
}
```

实际上，JavaScript 允许抛出任意对象，包括数字、字符串。但是，最好还是抛出一个 `Error` 对象。

最后，当我们用 `catch` 捕获错误时，一定要编写错误处理语句：

```js
var n = 0, s;
try {
    n = s.length;
} catch (e) {
    console.log(e);
}
console.log(n);
```

哪怕仅仅把错误打印出来，也不要什么也不干：

```js
var n = 0, s;
try {
    n = s.length;
} catch (e) {
}
console.log(n);
```

因为 catch 到错误却什么都不执行，就不知道程序执行过程中到底有没有发生错误。

### 错误传播

如果在一个函数内部发生了错误，它自身没有捕获，错误就会被抛到外层调用函数，如果外层函数也没有捕获，该错误会一直沿着函数调用链向上抛出，直到被 JavaScript 引擎捕获，代码终止执行。

所以，我们不必在每一个函数内部捕获错误，只需要在合适的地方统一捕获，例如：

```js
function main(s) {
    console.log('BEGIN main()');
    try {
        foo(s);
    } catch (e) {
        console.log('出错了：' + e);
    }
    console.log('END main()');
}

function foo(s) {
    console.log('BEGIN foo()');
    bar(s);
    console.log('END foo()');
}

function bar(s) {
    console.log('BEGIN bar()');
    console.log('length = ' + s.length);
    console.log('END bar()');
}

main(null);
```

运行后得到：

```js
BEGIN main()
BEGIN foo()
BEGIN bar()
出错了：TypeError: Cannot read property 'length' of null
END main()
```

### 异步错误处理

JavaScript 引擎是一个**事件驱动的执行引擎**，代码总是以**单线程**执行，而**回调函数的执行需要等到下一个满足条件的事件出现后，才会被执行**。

例如，`setTimeout()` 函数可以传入回调函数，并在指定若干毫秒后执行：

```js
function printTime() {
    console.log('It is time!');
}

setTimeout(printTime, 1000);
console.log('done');
```

**上面的代码会先打印 `done`，1 秒后才会打印 `It is time!`。**

**如果 `printTime()` 函数内部发生了错误，我们试图用 `try` 包裹 `setTimeout()` 是无效的**，原因就在于调用 `setTimeout()` 函数时，传入的 `printTime()` 函数并未立刻执行！紧接着，JavaScript 引擎会继续执行 `console.log('done');` 语句，而此时并没有错误发生。直到 1 秒后，执行 `printTime()` 函数时才发生错误，但此时除了在 `printTime()` 函数内部捕获错误外，外层代码并无法捕获。

所以，**涉及到异步代码，无法在调用时捕获，原因就是在捕获的当时，回调函数并未执行**。

类似的，当我们处理一个事件时，在绑定事件的代码处，无法捕获事件处理函数的错误。

例如，针对以下的表单：

```html
<form>
    <input id="x"> + <input id="y">
    <button id="calc" type="button">计算</button>
</form>
```

 我们用下面的代码给 `button` 绑定 `click` 事件：

```js
var $btn = $('#calc');

// 取消已绑定的事件:
$btn.off('click');
try {
    $btn.click(function () {
        var
            x = parseFloat($('#x').val()),
            y = parseFloat($('#y').val()),
            r;
        if (isNaN(x) || isNaN(y)) {
            throw new Error('输入有误');
        }
        r = x + y;
        alert('计算结果：' + r);
    });
} catch (e) {
    alert('输入有误！');
}
```

但是，用户输入错误时，处理函数并未捕获到错误。

应该把代码修改为：

```js
$btn.click(function () {
	try {
         var
            x = parseFloat($('#x').val()),
            y = parseFloat($('#y').val()),
            r;
        if (isNaN(x) || isNaN(y)) {
            throw new Error('输入有误');
        }
        r = x + y;
        alert('计算结果：' + r);
	} catch (e) {
    	alert('输入有误！');
	}
});
```



