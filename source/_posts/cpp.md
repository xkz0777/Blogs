---
title: C++复习提纲
date: 2021-07-19 23:22:30
tags: ['Programe design','C/C++']
img: /medias/featureimages/cpp.jpg
categories: 'C/C++'
top: false
author: xkz
summary: 程序设计 II 期末复习时做的简单 C++ 知识提纲
---

# C++ 复习提纲

## 引用

+ 定义引用**必须初始化**，初始化后**不能改变引用的指向**。

+ 可以用引用初始化另一个引用。

+ 可以把引用的地址赋值给指针。

## const

+ 修饰变量：值不可被修改（比 define 好，具有类型，便于检查）

+ 修饰指针：

```cpp
const int *p = &a;//p 指向的内容不可被修改
int * const p = &a;//指针指向的内存地址不可改变，但内容可以改变
const int * const p = &a;//指针指向的内容和地址都不可以改变
```

+ 修饰函数参数：参数在函数中不可被修改（一般是 const 引用）

+ 修饰类的成员函数：常成员函数，只有该函数可以访问对象的 const 成员

```cpp
const int f();//返回值为 const
int f();
int f() const;//不可修改当前对象的成员变量（如果需要修改的内容可以设为 mutable ），也不可调用非 const 成员函数
```

其中第二种**不可与 static 关键字一同使用**，且与非 const 修饰的函数（第二个）算**重载**

## 动态内存分配

+ new 和 delete 从**堆**中动态分配/删除存储块

+ delete 数组时数组名前加[]

+ new 自动决定对象大小且返回对应的指针，还会**调用构造函数**，delete 会**调用析构函数**，这些都是 malloc 和 free 不能的

## 函数参数

+ 缺省参数一定在最后。

+ 占位符参数，没有标识符，调用时必须为其提供一个值。

```cpp
void f(int x,int){}
int main(){
	f(1,1);
}
```

## 重载

+ 函数名、返回值类型相同，参数不同（类型/个数/次序/const 或引用修饰）。

+ 注意避免二义性。

## 构造函数

+ 一定是 public，没有返回值，可以重载，仅在对象生成时会被调用。

+ 常常配合初始化列表使用：

```cpp
class Complex{
    private:
    	double real, imag;
    public:
    	Complex(double r, double i):real(r), imag(i){}
    	Complex(const Complex &c);
};
```

+ 注意，成员实际的初始化顺序**与声明顺序相同，与初始化列表中出现的顺序无关**，因此应该按照声明的顺序来写初始化列表以避免歧义。

### 复制构造函数

参数为**另一个同类对象的引用**（通常还要 const 修饰），仅在下列三种状况下起作用

```cpp
Complex::Complex(const Complex &c){
		real = c.real;
		imag = c.imag;
	}
Complex c1;
```

```cpp
//1.用一个对象去初始化同类的其他对象
Complex c2(c1);
Complex c2 = c1;
```

上面与

```cpp
Complex c2;
c2 = c1;
```

不同，第二种c2 = c1是赋值语句，不导致复制构造函数被调用

```cpp
//2.函数的参数是某类对象
void f(Complex c){}
f(c1);
```

```cpp
//3.函数的返回值是某类对象
Complex f(){
	Complex c1;
	return c1;
}
```

### 类型转换构造函数

分显式（explicit），隐式。

## 析构函数

+ 类名前加~
+ 一定是 public
+ **不带参数**
+ **没有返回值**
+ **不可重载**（一个类最多只能有一个析构函数）。

+ 构造和析构顺序：一般**先构造的后析构**（类似栈）。

## this 指针

C++ 函数编译后的第一个参数，指向成员函数所作用的对象。

this 指针并不在对象的存储空间中，只在函数调用时存在。

## 友元

分友元函数，友元类。

+ 友元函数可以访问其友元类中的私有成员。

+ 友元类中的成员函数可以访问其友元类中的私有成员。

+ 友元关系不能传递和继承。

## 访问控制

+ private：只能在成员函数/友元函数中访问（其他对象的成员函数也可），**不可被派生类的成员函数访问**。在类中缺省的认为成员都是私有的（隐藏机制），struct 则缺省的认为是共有的。

+ public：任何地方访问。

+ protected：可以被成员函数/友元函数访问，还可以被派生类的成员函数访问。

## 运算符重载

实质是函数重载，把含运算符的表达式转换成对运算符函数的调用，操作数转换成函数的参数

两种形式：重载为普通函数/重载为成员函数

语法：返回值类型 operator 运算符（参数）{}

+ 重载为普通函数：**参数个数为运算符目数**

```cpp
Complex operator+(const Complex &a, const Complex &b){
	return Complex(a.real + b.real, a.imag + b.imag);
}
int main(){
    Complex a(4, 4), b(1, 1), c;
    c = a + b;//等价于c = operator+(a, b)
}
//还应该在Complex中把operator+设为友元
class Complex{
    ....
    friend Complex operator+(const Complex &a, const Complex &b);
}
```

+ 重载为成员函数：**参数个数为运算符目数-1**

```cpp
Complex Complex::operator-(const Complex &c){
	return Complex(real - c.real, imag - c.imag);
}
int main(){
    Complex a(4, 4), b(1, 1), c;
    c = a - b;//等价于c = a.operator-(b)
}
```

+ 重载“=”运算符：使得自己编写的 string 类的赋值是**浅拷贝**。应该尽量保持原来运算符的性质，返回值为 string&，因为 cpp 中等号的返回值是左端表达式返回值的引用（可以实现链式赋值），类似的，成员函数返回 *this 可以实现链式的函数调用。

+ 重载流运算符

```cpp
#include <iostream>
using namespace std;
class Complex {
    double real, imag;

public:
    Complex(double r = 0, double i = 0)
        : real(r), imag(i) {}
    friend Complex operator+(const Complex &a, const Complex &b);
    friend ostream &operator<<(ostream &o, const Complex &c);
};

ostream &operator<<(ostream &o, const Complex &c) {
    return o << c.real << " " << c.imag;
}

Complex operator+(const Complex &a, const Complex &b) {
    return Complex(a.real + b.real, a.imag + b.imag);
}

int main() {
    Complex a(4, 4), b(1, 1), c;
    c = a + b;
    cout << c;
}
```

+ 重载类型转换运算符（不能与类型转换构造函数一起写，不然会导致二义性）

```cpp
class Complex {
    double real, imag;

public:
    Complex(double r = 0, double i = 0)
        : real(r), imag(i) {}
    operator double(){
    	return real;
    }
};

int main(){
    Complex c(1, 0);
    cout << (double)c;
}
```

+ 重载自增自减运算符（占位符来区分前置后置）

## 类与类的关系

+ 继承（“是”的关系）

+ 复合（封闭类）（“有”的关系）

都要注意构造函数/析构函数的调用

+ 继承时先调用父类的构造函数（一级一级网上），再调用子类的构造函数。

+ 封闭类先调用成员对象的构造函数，再调用封闭类的构造函数。

析构函数的调用顺序与构造函数相反。

## 公有继承

```cpp
class Student{};
class Undergraduate: public Student{};
```

### 重写（override）

一般在派生类中不与基类定义同名变量，但会修改基类的方法

```cpp
class Student{
    string name;
    int age;
public:
    void printInfo(){
        cout << name << " " << age;
    }
};
class Undergraduate: public Student{
    string id;//学号
public:
    void printInfo(){
        Student::printInfo();
        cout << id;
    }
};
```

### 赋值规则

+ 派生类对象可以赋值给基类对象

+ 派生类对象可以初始化基类引用

+ 派生类对象地址可以赋值给基类指针

## 虚函数和多态

### 虚函数

```cpp
virtual int f();
```

**virtual** 关键字修饰的成员函数是虚函数。

没有函数体的虚函数称为纯虚函数，含有纯虚函数的类称为抽象类。抽象类只能作为基类派生新类，**可以定义抽象类指针和引用，不能创造抽象类的对象**。派生类的子类只有当实现父类中的**所有**抽象函数才能称为非抽象类。

### 多态

通过基类引用/基类指针调用函数时，如果调用的是**基类和派生类中的同名虚函数**，引用/指针**指向什么**类的对象，就**调用什么**类的虚函数。例：

```cpp
#include <cmath>
#include <iostream>
using namespace std;
class Shape {
public:
    virtual double area() = 0;  //都是纯虚函数
    virtual double printInfo() = 0;
};

class Rectangle : public Shape {
    int w, h;

public:
    Rectangle(int w, int h)
        : w(w), h(h) {}
    double area() {  //父类中是虚函数，子类自动也是虚函数
        return w * h;
    }
    double printInfo() {
        cout << "Rectangle:" << area() << endl;
    }
};

class Triangle : public Shape {
    double a, b, c;

public:
    Triangle(double a, double b, double c)
        : a(a), b(b), c(c) {}
    double area() {
        double p = (a + b + c) / 2;
        return sqrt(p * (p - a) * (p - b) * (p - c));
    }
    double printInfo() {
        cout << "Triangle:" << area() << endl;
    }
};

class Circle : public Shape {
    double r;
    const double PI = 3.1415926;

public:
    Circle(double r)
        : r(r) {}
    double area() {
        return PI * r * r;
    }
    double printInfo(){
        cout << "Circle:" << area() << endl;
    }
};

int main() {
    Shape *s[3];
    s[0] = new Rectangle(2, 2);
    s[1] = new Triangle(3, 4, 5);
    s[2] = new Circle(2);
    for (int i = 0; i < 3;++i){
        s[i]->printInfo();
    }
}
```

运行结果：

```cpp
Rectangle:4
Triangle:6
Circle:12.5664
```

## 模板

### 类模板

+ 定义：

```cpp
template <class T1, class T2.....>
	class 类模板名{
		...
	}
```

其中参数里的 class 也可以改为 typename，下同。

同时，参数名字可以任取，不一定要叫 T（只是习惯上通常取成 T）。

+ 例如，用模板实现栈：

```cpp
template <class T>
class Stack{
	T *top;
	int size;
	int maxSize;
public:
	Stack(int s)
        : maxSize(s), top(0) {
        data = new T[maxSize];
    }
    ~Stack() {
        delete[] T;
    }
    bool push(T a) {
        if (top == maxSize) {
            return false;
        }
        else {
            T[top++] = a;
        }
    }
    T pop() {
        return T[--top];  //应该有安全检查，这里略去
    }
};
```

实例化得到对象：

```cpp
Stack<int> s(20);
Stack<char> s(10);
```

### 函数模板

```cpp
template <class T1, class T2....>
返回值类型 函数名(函数参数表){
	...
}
```

例：求两个数的较大值：

```cpp
template <class T>
T max(T a, T b){
	return a > b ? a: b;
}
```

+ 函数模板不需要程序员实例化，由编译器完成。

+ 需要注意让参数与模板完全匹配，因为编译器不会提供任何形式的参数转换，如不能调用

```cpp
int a;
char c;
int b = max(a, c);
```

+ 函数模板也可以重载。

### 模板的派生和友元

比较复杂，略过。

## 其他

* 内联函数（宏的改进）。

+ 在类外面写成员函数：要加上作用域。

```cpp
class Person(){
	private:
		string name;
	public:
		void getName();
}
void Person::getName(){}
```

+ 构造函数和析构函数都不负责为对象分配或回收内存。

+ 静态成员函数：static 修饰，没有 this 指针，可以通过类名直接访问，其中不能访问非静态成员变量（不知道属于哪个对象），也不能调用非静态成员函数。

+ 静态成员变量：类似静态成员函数。。

+ 面向对象基本特征：抽象、封装、继承、多态。
+ 面向对象四个基本原理：抽象、封装、模块性、层次。







