---
title: Java 笔记1
date: 2021-09-26 18:55:42
tags: Java
categories: Java
img: /medias/featureimages/java1.jpg
author: xkz
top: true
summary: Java 笔记1
---

# Java 笔记1

## 前言

上学期因为要做图形化大作业临时学了 Java，但对 Java 的了解还非常肤浅，这学期决定重新复习一下 Java。

## Java 简介

### Java 特点

+ Java 是纯面向对象的编程语言（OOP），不存在类外的全局变量和全局函数。
+ Java 是安全的，无直接指针操作，且能够进行自动内存管理，程序员无须也无法控制无用内存的回收。
+ Java 有良好的跨平台性，数据类型的长度固定。
+ 不支持宏，不用多重继承。

### Java 三个版本

+ Java SE：Standard Edition
+ Java EE：Enterprise Edition
+ Java ME：Micro Edition

Java SE 是 Java 标准版，包含标准的 JVM 和标准库。

Java EE 是企业版，在 Java SE 的基础上加上了大量的 API 和库，以便方便开发 Web 应用、数据库、消息服务等，Java EE 的应用使用的虚拟机和 Java SE 完全相同。

Java ME 和 Java SE 不同，它是一个针对嵌入式设备的“瘦身版”，Java SE 的标准库无法在 Java ME 上使用，Java ME 的虚拟机也是“瘦身版”。

Java SE 是整个 Java 平台的核心，Java EE 是进一步学习 Web 应用所必须的。但 Java ME 从来没有真正流行起来，反而是 Android 开发成为了移动平台的标准之一。

### Java 程序的编译和运行

创建 `Hello.java` 文件，内容如下：

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("hello world");
    }
}
```

使用 javac 命令，将 `Hello.java` 文件编译为字节码（bytecode）文件 `Hello.class`，执行时，使用 `java Hello` 来执行（注意不要写成 `java Hello.class`。

其他命令：`jar` 用于打包，`javadoc` 生成文档，`javap` 反汇编，`jdb` Java 调试器。

~~如果用 IDE 就不用管命令行指令了。~~

### 一些术语

JDK：Java Development Kit，Java 开发工具包，开发 Java 软件需要安装，安装 JDK 后就不必再安装 JRE。

JVM：Java Virtual Machine，Java 虚拟机，读取并处理经编译过的字节码 class 文件。

JRE：Java Runtime Environment。JRE = JVM + API。

### Java 程序的类型

分为 Application 和 Applet 两种，前者是独立的程序，需要 JRE 才能运行，后者是嵌在 HTML 网页中的非独立的程序，由专门的 appletViewer 或 Web 浏览器（调用 Java 虚拟机）来运行。

### Java 程序的基本构成

+ package 语句（0 或 1 句），表明该 Java 文件在什么文件夹下。用于解决名字冲突的问题（类似于 C++ 中的命名空间），包名不同的类在 Java 虚拟机看来就是不同的类。
+ import 语句（0 或多句），导入的包名，例如 `import java.util.*`。
+ 类定义（其中**每个文件有且仅有一个 public 类，且该类与文件同名**，且 `main` 函数的写法固定，一定是 `public static void main(String[] args）`，Java 程序总是从 `main` 函数开始执行。

使用 package 时，编译和运行都需要带上额外参数，但大项目现在都使用 IDE 居多，这里不过多介绍。

## 输入与输出

### 命令行界面

#### 输入

这里只简单介绍使用 `java.util.Scanner` 类进行的输出，其他的 IO 比较复杂，留在后面专门介绍。

`java.util.Scanner` 仅在 JDK 1.5 以上支持，使用方法如下：

```java
import java.util.Scanner
class ScannerTest {
 	public static void main(String[] args) {
     	Scanner scanner = new Scanner(System.in);
     	int a = scanner.nextInt();
 	}
}
```

除了 `nextInt` 外，还有 `nextDouble`，`nextLine`，`next`（得到下一个单词）等方法。

注：由于 Java 是纯面向对象的语言，因此语句都要写在类的方法内，但下面为了方便，有时省略类和函数。

#### 输出

使用 `System.out.println()`，可以换行输出，`System.out.print()`、`System.out.printf()` 等也可以用于输出。

### 图形化界面

对于不同的界面元素，有不同的 IO 方法，例如对于 `JButton` 类有 `getText` 和 `setText` 方法。

## 变量和数据类型

Java 数据类型分两大类：基本数据类型（primitive type）和引用类型（reference type）

基本数据类型：

+ 数值型

	包含整数类型（byte，short，int，long）

	浮点类型（float，double）

+ 字符型（char）

+ 布尔型（boolean）

**基本类型变量存储在栈。**

引用类型：

常见的有类，接口，数组，String 等，在后面会分别介绍。

**引用类型变量存储在堆**，变量只是存储了一个指针，指向堆中该变量的存储位置。

在赋值时，`double d2 = d` 复制的是值，`Person p2 = p` 复制的是引用。

### 整型

byte、short、int、long 分别占 1、2、4、8 个字节。

用 `0b` 开头表示二进制，`0` 表示八进制 `0x` 表示十六进制。

整型常量默认为 int 类型，声明 long 类型，应在常量后加 `L`。

Java 中没有无符号数。

### 字符型

Java 字符采用 Unicode 编码，每个字符占两个字节，可以 Unicode 十六进制编码形式表示，例如 `char c = '\u0061';`，如果想查看某个字符的 Unicode 编码，只要直接将该字符赋值给整型变量并输出即可。

### 布尔型

**不可以用 0 或非 0 的整数代替 false 和 true**，因此 `if(a = 5)` 这样的语句在 Java 中不被允许。

### 浮点型

常量默认为 `double`，如果要使用 `float`，需要在常量后加 `f`。

## 运算符和表达式

Java 中，`&`, `|`, `^` 可以是逻辑运算符，也可以是位运算符，而 `&&` 和 `||` 是短路运算的逻辑运算符。在位运算右移中，`>>` 为有符号右移（算数右移），高位补原来的符号位，`>>>` 为无符号右移，高位补 0。

对于所有的 byte、short、char 运算数，参与算术运算前会先转换为 int，称为整型提升。

Java 中没有表达式语句，因此 `x + y;` 是不合法的。

## 流程控制

有 if、switch、for、while 等语句，Java 的循环比 C 有更多特性，例如：

1. for-each 循环

	用于遍历数组，格式为 `for (var : array)`。该循环不仅能遍历数组，还可以遍历所有可迭代（Iterable）类型

2. break 和 continue 加标记，即

	```java
	label:
	for(;;){
	    for(;;){
	        break label;
	    }
	}
	```

	类似于其他语言中的 goto 语句。

## 字符串

## 数组

### 声明

使用 `类型名[] 数组名` 的形式，如 `int[] a`，也可以写成 C 式数组 `int a[]`，但不如 `int[] a` 来得好。

此外，不能使用 `int[5] a` 或 `int a[5]` 在声明数组时指定数组长度，分配空间只能在声明后用 `new` 分配，例如：`int[] a = new int[5];`。

分配后，数组的元素会被隐式初始化，数值类型为 0，引用类型为 `null`。

在声明时也可以直接使用大括号初始化，此时不必分配内存，例如 `int[] a = {1, 2, 5};`。

对于多维数组，可以用 `int[] a = {{1, 2}, {3, 4}};`，如果分配内存，则必须从高维到低维分配（与 C 语言不同），例如：

```java
int[][] a = new int[3][];
a[0] = new int[2]; //数组每行的长度可以不同
a[1] = new int[3];
a[2] = new int[4];
```

### Java 数组特性

+ Java 数组是引用类型。因此如果将某个数组变量赋值给另一个变量，两个变量将指向同一片内存（同一个数组）。
+ 数组有 `length` 属性指明数组长度。
+ 数组的长度不可变。如果要改变数组长度，只能重新分配空间并改变数组变量的指向（这并没有改变原来数组的长度，且通过数组变量无法索引到原来的数组）。
+ 通过索引访问数组元素时，如果索引越界，将会报错。

### 数组操作

#### 数组复制

使用 `System.arraycopy(source, i, dest, j, length)`，把 `source` 中下标从 0 开始的 `length` 个元素复制到 `dest` 数组，从下标 j 的位置开始存储。

#### 数组排序

使用 `Arrays.sort(array)` 即可，对数组进行排序会改变数组本身的地址，不会得到排好序的复制。

## 面向对象

只介绍与 C++ 不同的部分。

### setter 和 getter

为了实现封装和隐藏，通常将字段用 private 修饰，并用 `setxxx()` 和 `getxxx()` 方法对类的属性进行存取。这样的做法有以下好处：

+ 外部类无法随意存取和修改类的字段。
+ 方法中可以对给定参数的合法性进行检查。

### 访问控制符

#### 修饰符

Java 中的成员除了 `private`、`protected`、`public` 三种访问控制符外，还有默认的访问控制符，四种修饰变量访问权限如下表：

|  修饰符   | 同一个类中 | 同一个包中 | 不同包中的子类 | 不同包中的非子类 |
| :-------: | :--------: | :--------: | :------------: | :--------------: |
|  private  |    yes     |            |                |                  |
|   默认    |    yes     |    yes     |                |                  |
| protected |    yes     |    yes     |      yes       |                  |
|  public   |    yes     |    yes     |      yes       |       yes        |

#### 类访问控制符

或者为 `public`，或者为默认，如果类用 public 修饰，可以被其他所有类访问，如果是默认访问控制符，只能被同包中的类访问。

#### 其他修饰符

`static`：静态的，类的。`static` 成员可以用类名直接访问。静态方法经常用于工具类。例如 `Arrays.sort()`、`Math.random()` 等。

`final`：不可变的，最终的。修饰类时表示该类不可被继承，修饰变量时表示变量是不可变的（类似 C++ 中的 `const`）。

`abstract`：抽象的。用于声明抽象类和抽象方法，后面会详述。

### 继承

继承使用 `extends` 关键字，如果类声明时没有使用 `extends`，则默认为 `java.lang.Object` 的子类，因此所有的类都是直接或间接继承 `java.lang.Object` 得到的。

使用 `super` 访问父类的域和方法，在子类构造方法中，如果父类没有默认的构造方法，应该在第一句中使用 `super` 调用父类的构造方法（否则编译器会自动加上 `super();`，即自动调用父类不带参数的构造方法，但父类没有这样的默认构造方法，会编译失败）。

### 向上转型和向下转型

子类对象可以被赋值给父类对象，称为向上转型（Upcasting）。

例如：

```java
class Person {};
class Student extends Person {};
Person p = new Student();
```

和向上转型相反，如果把一个父类类型强制转型为子类类型，就是向下转型（Downcasting）。

例如：

```java
Person p1 = new Student(); // upcasting, ok
Person p2 = new Person();
Student s1 = (Student) p1; // ok
Student s2 = (Student) p2; // runtime error! ClassCastException!
```

把 `p2` 转型为 `Student` 会失败，因为 `p2` 的实际类型是 `Person`，不能把父类变为子类，因为子类功能比父类多，多的功能无法凭空变出来。

因此，向下转型很可能会失败。失败的时候，Java 虚拟机会报 `ClassCastException`。

为了避免向下转型出错，Java 提供了 `instanceof` 操作符，可以先判断一个实例究竟是不是某种类型：

```java
Person p = new Person();
System.out.println(p instanceof Person); // true
System.out.println(p instanceof Student); // false

Student s = new Student();
System.out.println(s instanceof Person); // true
System.out.println(s instanceof Student); // true

Student n = null;
System.out.println(n instanceof Student); // false
```

利用`instanceof`，在向下转型前可以先判断：

```java
Person p = new Student();
if (p instanceof Student) {
    // 只有判断成功才会向下转型:
    Student s = (Student) p; // 一定会成功
}
```

从 Java 14 开始，判断 `instanceof` 后，可以直接转型为指定变量，避免再次强制转型。

例如：上面代码可以改作：

```java
if (p instanceof Student s){
    //对s的操作
}
```

### 重载和重写

重载（Overload），为类中不同的方法拥有相同的名字，但参数类型不同。

注意：方法名相同，方法参数相同，但方法返回值不同，是不同的方法，**但无法进行重载**，因为会引发**二义性**，因此出现这种情况时编译器会报错。

重写（Override），为子类重新定义与父类同名的方法，实现对父类方法的重写（覆盖）。

Override 和 Overload 不同的是，如果方法签名不同，就是 Overload，Overload 方法是一个**新方法**。如果方法签名相同，并且返回值也相同，就是 Override。

### 多态

Java 中没有虚函数的概念，所有非 `final`、`static`、`private` 的方法都可以认为是虚方法，并会自动地进行动态绑定。所谓动态绑定，是指程序在运行时根据具体对象的类型而选择调用哪个方法。

`final` 方法不能被子类重写（Override），因此不是虚方法。

`static` 方法因为与实例类型无关，因此不是虚方法。

`private` 方法子类看不见，也不是虚方法。

多态存在的三个必要条件：

+ 继承
+ 重写
+ 父类引用指向子类对象

也可以采用接口或抽象类的方式来实现多态。

### 内部类、局部类和匿名类

#### 内部类

##### 内部类的定义

把一个类的定义写在另一个类内部，该类便成为内部类，内部类不能与外部类同名。

##### 内部类的使用

在封装内部类的类的内部使用内部类，与普通类的使用方式相同。

在其他地方使用时，类名前要加上外部类的名字，在用 `new` 创建实例时，也要在 `new` 前面加上外部内对象名：`外部对象名.new 内部类名(参数)`。

内部类中可以直接访问外部类的字段和方法，如果有与外部类同名的字段或方法，可以使用 `外部类名.this.字段及方法` 来访问外部类的同名字段和方法。

#### 局部类

在方法中定义的类称为方法中的内部类，或局部类。

#### 匿名类

匿名类是一种特殊的内部类，在定义类的同时就生成该对象的一个实例，仅使用一次。例如：

```java
import javax.swing.*;
import java.awt.event.*;

JButton btn = new JButton();
btn.addActionListener(new ActionListener() {
    public void actionPerformed(ActionEvent event) {
        System.out.println("hello world");
    }
});
```

#### lambda 表达式

Java8 中增加了 lambda 表达式，其基本写法为 `(参数)->{语句}`。

在 Java 中，lambda 表达式实际上是 **匿名类的一个实例**。

例如，在多线程（后面会讲到）中，创建 `Runnable` 对象时，如果不用 lambda 表达式，需要使用：

```java
Runnable dolt = new Runnable() {
    public void run() {
        System.out.println("hello");
    }
};
new Thread(dolt).start();
```

而用 lambda 表达式可以简写为：

```java
new Thread(()->System.out.println("hello")).start();
```

可以看出，**lambda 表达式是接口或者是接口函数的简写**。

积分时，也可以用 lambda 表达式简化：

接口定义为 `interface Fun{ double fun( double x); }`

```java
double d = Integral(x->Math.sin(x), 0, Math.PI, 1e-5);
```

在按钮事件处理（匿名类的例子）中，采用 lambda 表达式可以简化为：

```java
btn.addActionListener(e->{
    System.out.println("hello world");
})
```

由于 lambda 表达式只能表示一个函数，因此**能写成 lambda 的接口要求包含且最多只能有一个抽象函数**。

这样的接口可以（但不强求）用注记 `@FuntionalInterface` 来表示，称为函数式接口。

## 抽象类和接口

### 抽象类

用 `abstract` 修饰的类称为抽象类，抽象类不能被实例化。

### 抽象方法

用 `abstract` 修饰的方法称为抽象方法，抽象方法只用声明而不需实现，例如

```java
void abstactFunction();
```

**抽象类**中可以包含抽象方法，也**可以不包含抽象方法**，但**包含抽象方法的类一定是抽象类**，抽象类可以被继承，子类中的抽象方法被实现后该类便不是抽象类。

### 接口

接口（Interface），是**常量和抽象方法的集合**，里面的所有方法都默认是 `public abstract`，常量具有 `public`、`static`、`final `的属性，以 `interface` 来声明。**一个类通过实现接口的方式，从而来继承接口的抽象方法。**实现接口使用 `implements` 关键字。

通常接口以 `able` 或 `ible` 结尾，表明实现的接口可以完成一定的行为，例如：

```java
interface Flyable {
    String name = "Flyable";
    void takeof();
    void fly();
    void land();
}
```

**接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。**

除非实现接口的类是抽象类，否则该类要**定义接口中的所有方法**。

接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。

与一个类只能有一个父类不同，一个类**可以实现多个接口**。

## Java语言机制

介绍 Java 中的一些机制和细节。

### 字段变量和局部变量

字段变量是对象的一部分，存储在堆中，会自动被赋予初值，但**局部变量需要显式赋值**，没有赋值无法通过编译，例如：

```java
int b;
System.out.println(b); //编译错误
```

字段变量可以用 `public`、`private`、`static`、`final` 修饰，但局部变量只能用 `final` 修饰。

### 变量的传递和返回

传参和返回时，如果变量是引用类型，则会传入/返回该对象的引用而不是复制。

### 不定长参数

例如：

```java
class Group {
    private String[] names;

    public void setNames(String... names) {
        this.names = names;
    }
}
```

不定长参数用省略号（`...`）表示，并且必须是最后一个参数。

在调用时，传入参数的个数不限，例如：

```java
Group g;
g.setNames(Alice, Bob);
```

在内部，Java 会把传入的所有参数打包为一个数组，因此也可以把可变参数改写为 `String[]` 类型：

```java
class Group {
    private String[] names;

    public void setNames(String[] names) {
        this.names = names;
    }
}
```

但是，调用方需要自己先构造 `String[]`，比较麻烦。例如：另一个问题是，调用方可以传入 `null`（即不传入任何参数）而可变参数可以保证无法传入 `null`，因为传入 0 个参数时，接收到的实际值是一个空数组而不是 `null`。

### 基本类型的包装类

基本类型的包装类将基本类型（primitive type）包装成引用类型（Object），如 `int`->`java.lang.Integer`（可简写为 `Integer`）、`boolean`->`Boolean`、`char`->`Character` 等。

如：`Integer I = new Integer(10);`。

同时提供了装箱和拆箱功能，例如：

```java
Integer I = 10;
int i = I;
```

实际编译时当成如下处理：

```java
Integer I = Integer.valueof(10);
int i = I.intValue();
```

所有的包装类型都是不变类，**即创建包装类型对象后，该对象就是不变的**。

此外，对两个 `Integer` 实例进行比较要注意：不能用 `==` 比较，因为 `Integer` 是引用类型，**必须使用 `equals()` 比较**。

同时，`Integer I = new Integer(10);` 和 `Integer I = Integer.valueof(10)`，两个方法是不一样的，**前者一定会创建并返回一个新的对象**，但对于后者，由于 `Integer` 是不变类，因此编译器为了节省空间，在多次调用时，可能会返回同一个对象：

```java
Integer x = 17;
Integer y = 17;
System.out.println(x == y); // true
```

像 `Integer.valueof()` 这样不通过 `new`，而是用一个静态方法来对外提供自身实例的方法，称为`静态工厂方法(Static factory method)`。在一个类既有构造方法，又有静态工厂方法时，应该优先选用静态工厂方法来创建新的对象。

包装类还提供了其他方法，例如 `Integer.parseInt()` 和 `Integer.toString(num, base = 10)`，后者可以把整数转为指定进制的字符串（Java 是没有默认参数的，这里只是简化函数表示）。

