---
title: 数据结构——线性表
date: 2021-08-13 15:26:06
tags: 数据结构
author: xkz
categories: 数据结构
mathjax: true
summary: 数据结构——线性表
img: /medias/featureimages/sjjg2.jpg
---

# 线性表

## 多项式的表示

### 数组各分量对应多项式各项

例如：$f(x)=4x^5-3x^2+1$表示成

| 数组下标i |  0   |  1   |  2   |  3   |  4   |  5   |
| :-------: | :--: | :--: | :--: | :--: | :--: | :--: |
|   a[i]    |  1   |  0   |  -3  |  0   |  0   |  4   |

缺点：空白项多时非常浪费空间，例如$f(x)=x^{10086}+1$

### 结构数组顺序存储非零项

每个非零项存储系数和指数，把一个多项式看作二元组$(a_i,i)$的集合。

如何进行多项式相加：从头开始，比较两个多项式当前对应项的指数，可以做到$O(m+n)$时间的相加。

缺点：增删项不方便，并且无法在$O(1)$时间内访问某个指数项。

### 链表结构存储非零项

链表中每个节点存储多项式中一个非零项，包括两个数据域——系数和指数，以及一个指针域。

优点：相较于结构数组，可以实现增删项。

## 线性表

线性表（Linear List）：由同类型数据元素构成有序序列的线性结构。

### 线性表的抽象数据类型描述

类型名称：线性表

数据对象集：线性表是$n(\ge 0)$个元素构成的有序序列$(a_1,a_2,...,a_n)$

操作集：

```cpp
ElementType findKth(int k, List l)//根据位序 k，返回相应元素
int find(ElementType x, List l)//在线性表 l 中查找 x 的第一次出现位置
bool insert(ElementType x, int i, List l)//在位序 i 前插入一个新元素 x，返回插入是否成功
bool delete(int i, List l)//删除位序 i 处的元素，返回删除是否成功
int length(List l)//返回线性表 l 的长度
```

### 线性表的实现

#### 数组存储

```cpp
#include <iostream>
template <typename ElementType>
class List {
    ElementType* data;
    int last;
    int maxSize;

public:
    List(int maxSize)
        : maxSize(maxSize) {
        data = new ElementType[maxSize];
        last = -1;
    }

    int find(ElementType x) {
        int i = 0;
        for (int i = 0; i <= last; ++i) {
            if (data[i] == x) {
                return i;
            }
        }
        return = 1;
    }

    bool insert(ElementType x, int i) {
        if (last == maxSize - 1) {
            std::cout << "表满";
            return false;
        }
        else if (i < 0 || i > last + 1) {
            std::cout << "位置不合法";
            return false;
        }
        for (int j = last; j > i; --j) {
            data[j + 1] = data[j];
        }
        data[i] = x;
        return true;
    }

    bool remove(int i) {
        if (i < 0 || i > last) {
            std::cout << "位置i的元素不存在";
            return false;
        }
        for (int j = i; j < last;++j){
            data[j] = data[j + 1];
        }
        last--;
    }
    
    ElementType findKth(int k) {
        if (k < 0 || k > last) {
            std::cout << "位次不合法";
            return -10086;  //自定义错误标记
        }
        return data[k];
    }
    
    int length() {
        return last + 1;
    }
    
    ~List(){
        delete[] data;
    }
};
```

注：上面的代码中，位次和位置都是从0开始计算。

#### 链表存储

这里用 `int` 代替 `ElementType`，因为如果要使用模板，需要对每个出现的 `ListNode` 都进行实例化，有点麻烦，有兴趣的读者可以自行尝试。

```cpp
#include <iostream>
class ListNode {
public:
    int data;
    ListNode* next;
    
    ListNode(int x)
        : data(x) {
        next = nullptr;
    }
    
    ListNode() {
        next = nullptr;
    }
};
class List {
    ListNode* head;  //这里头指针只作为头指针，不存储任何数据

public:
    List() {
        head = new ListNode;
    }

    int length() {
        ListNode* p = head->next;
        int l = 0;
        while (p) {
            p = p->next;
            l++;
        }
        return l;
    }

    ListNode* findKth(int k) {
        ListNode* p = head;
        int i = -1;
        while (p) {
            p = p->next;
            i++;
            if (i == k) {
                return p;
            }
        }
        return nullptr;
    }

    ListNode* find(int x) {
        ListNode* p = head->next;
        while (p && p->data != x) {
            p = p->next;
        }
        return p;
    }

    bool insert(int x, int i) {  //插入在第i个位置，i从0开始
        if (head->next == nullptr) {
            ListNode* ptrL = new ListNode(x);
            ptrL->next = head->next;
            head->next = ptrL;
            return true;
        }
        ListNode* p = findKth(i - 1);
        if (p == nullptr) {
            std::cout << "参数i错";
            return false;
        }
        ListNode* ptrL = new ListNode(x);
        ptrL->next = p->next;
        p->next = ptrL;
        return true;
    }

    bool remove(int i) {
        ListNode* p = findKth(i - 1);
        if (p) {
            ListNode* tmp = p->next;
            p->next = tmp->next;
            delete tmp;
            return true;
        }
        else {
            std::cout << "第i个节点不存在";
            return false;
        }
    }

	~List() {
        ListNode *p = head->next, *q = head;
        while (true) {
            delete q;
            if (p == nullptr) {
                break;
            }
            q = p;
            p = p->next;
        }
    }
};
```

## 广义表

广义表是线性表的推广，即其数据域可以是一个元素，也可以是一个指针指向另一条链。

例如：对于二元多项式$f(x,y)=9x^{12}y^2+4x^{12}+15x^8y^3-x^8y+3x^2$，可以先看成关于$x$的多项式，每一项的系数又是关于$y$的多项式，即可以表示为如下形式

<img src="\1.png" style="zoom: 40%;" />

```cpp
class GeneralizedList{
    int tag;//标志域，0表示节点是单元素，1表示节点是广义表
    union{
        int data;
        GeneralizedList* subList;
    } uRegion;
    //用Union的好处是，不会有节点同时需要这两个域，既可以节省内存，又可以避免某个节点同时对两个域赋值，便于debug
    GeneralizedList* next;
};
```

## 多重链表

多重链表：链表中的节点可能同时隶属于多个链。

多重链表中节点的指针域会有多个，例如前面的广义表包含了 `subList` 和 `next` 两个指针域，但包含两个指针域的链表不一定是多重链表，例如双向链表。

多重链表可以用来存储树、图这样相对复杂的数据结构。

例如：使用多重链表中的十字链表来存储**稀疏**矩阵：

+ 只存储矩阵的非零项

+ 节点的数据域：行坐标 `row`、列坐标 `colomn`、数值 `value`。

+ 节点的指针域：行指针（或称为向右指针）`right`，把同行串起来。列指针（或称为向下指针）`down`，把同列串起来。

+ 节点的标识域：`tag`，用来标识是头节点还是非零项节点（头节点没有数据域）

## 栈

后入先出（Last In First Out）的数据结构，只在栈顶一端作插入和删除。

### 栈的抽象数据类型描述

类型名称：栈（Stack）

数据对象集：一个有 0 个或多个元素的有穷线性表

操作集

+ bool isFull()：判断栈是否已满
+ bool push(ElementType x)：把元素 x 压入栈中，返回是否成功
+ bool isEmpty()：判断栈是否为空
+ ElementType pop()：删除并返回栈顶元素

### 栈的实现

通常都使用数组实现：

```cpp
template <typename ElementType>
class Stack {
    int maxSize;
    int top = -1;
    ElementType* data;

public:
    Stack(int maxSize)
        : maxSize(maxSize) {
        data = new ElementType[maxSize];
    }

    bool isFull() {
        return top == maxSize - 1;
    }

    bool isEmpty() {
        return top == -1;
    }

    bool push(ElementType x) {
        if (isFull()) {
            return false;
        }
        data[++top] = x;
        return true;
    }

    ElementType pop() {
        return data[top--];
    }
    
    ~Stack() {
        delete[] data;
    }
};
```

如果实在想使用链表，应该注意栈顶要在链表头一端，这样才能实现在$O(1)$时间内的插入删除。又或者可以直接存储链表的表头和表尾两个指针，此时只要固定一个指针不动，操作另一个指针即可。

### 一个数组实现两个栈

数组的两头作为两个栈的栈底，当两个栈顶指针相遇时，表示两个栈都满了。在 `push()` 和 `pop()` 时，可以额外加入参数 `tag` 来标识是对哪个栈的操作。

### 栈的应用：表达式求值

#### 后缀表达式求值

此时只需要从左到右读取输入，遇到数字入栈，遇到运算符时取出栈顶的两个数进行运算，并将结果入栈，即可得到答案，直接放代码：

```cpp
#include <iostream>
using namespace std;
template <typename ElementType>
class Stack {
    int maxSize;
    int top = -1;
    ElementType* data;

public:
    Stack(int maxSize)
        : maxSize(maxSize) {
        data = new ElementType[maxSize];
    }

    bool isFull() {
        return top == maxSize - 1;
    }

    bool isEmpty() {
        return top == -1;
    }

    bool push(ElementType x) {
        if (isFull()) {
            return false;
        }
        data[++top] = x;
        return true;
    }

    ElementType pop() {
        return data[top--];
    }
};

int main() {
    Stack<int> s(100);
    char c;
    cin.get(c);
    int a, b;
    while (c != '\n') { //以换行符作为结束符，下同
        if (c >= '0' && c <= '9') {
            s.push(c - '0');
        }
        else {  //pop()时注意顺序
            b = s.pop();
            a = s.pop();
            switch (c) {
            case '+':
                s.push(a + b);
                break;
            case '-':
                s.push(a - b);
                break;
            case '*':
                s.push(a * b);
                break;
            default:
                s.push(a / b);
                break;
            }
        }
        cin.get(c);
    }
    cout << s.pop();
}
```

#### 中缀表达式求值

可以将中缀转成后缀，例如：对于表达式 $a*(b+c)/d$，转换成后缀表达式应为 $abc+*d/$，可以发现，运算数的相对位置是没有变化的，因此读入运算数时**直接输出**（如果是还要继续求值，则可以入栈），而读到运算符时，由于可能有括号存在，此时我们需要比较该运算符与上一个运算符的优先级，也即需要有**另一个栈存储运算符**。具体的规则如下：

+ 运算数：直接输出

+ 左括号：入栈

+ 右括号：将栈顶的运算符弹出并输出，直到遇到左括号（出栈，但是不输出）

+ 运算符：

	若优先级高于栈顶运算符，压栈

	若优先级小于等于栈顶运算符：不断把栈顶运算符弹出并输出，直到栈顶运算符优先级低于该运算符为止，然后将该运算符压栈

+ 处理完后，把栈中所有运算符全部输出。

转换部分代码：

```cpp
#include <iostream>
using namespace std;
template <typename ElementType>
class Stack {
    int maxSize;
    int top = -1;
    ElementType* data;

public:
    Stack(int maxSize)
        : maxSize(maxSize) {
        data = new ElementType[maxSize];
    }

    bool isFull() {
        return top == maxSize - 1;
    }

    bool isEmpty() {
        return top == -1;
    }

    bool push(ElementType x) {
        if (isFull()) {
            return false;
        }
        data[++top] = x;
        return true;
    }

    ElementType pop() {
        return data[top--];
    }
};

bool isNumber(char c) {
    return c >= '0' && c <= '9';
}

bool isOperator(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/';
}

bool opRank(char op) {
    if (op == '+' || op == '-') {
        return 0;
    }
    else {
        return 1;
    }
}

int main() {
    Stack<char> s(100);
    char c, lastc = 0;
    cin.get(c);
    bool flag = false;
    while (c != '\n') {
        if (isNumber(c)) {
            if (!flag) {
                flag = true;
            }
            else {
                if (!isNumber(lastc)) {
                    cout << " ";
                }
            }
            cout << c;
        }
        else if (c == '(') {
            s.push(c);
        }
        else if (isOperator(c)) {
            if (s.isEmpty()) {
                s.push(c);
            }
            else {
                char op = s.pop();
                if (op == '(') {
                    s.push(op);
                    s.push(c);
                }
                else {
                    if (opRank(c) > opRank(op)) {
                        s.push(op);
                        s.push(c);
                    }
                    else {
                        while (op != '(' && opRank(c) <= opRank(op)) {
                            cout << " " << op;
                            if (s.isEmpty()) {
                                break;
                            }
                            op = s.pop();
                        }
                        if (op == '(') {
                            s.push(op);
                        }
                        s.push(c);
                    }
                }
            }
        }
        else {
            char op = s.pop();
            while (op != '(') {
                cout << " " << op;
                op = s.pop();
            }
        }
        lastc = c;
        cin.get(c);
    }
    while (!s.isEmpty()) {
        cout << " " << s.pop();
    }
}
```

注：该代码只能处理正整数的情况。

之后只要把输出流转成字符串，便可以再利用后缀求值部分的代码来计算中缀表达式的值，这里略过。

### 栈的其他应用：

+ 函数调用及递归实现
+ 深度优先搜索
+ 回溯算法

注：关于栈的上机题逆逆波兰式的题解见 [此文](https://xkz0777.github.io/2021/07/20/sj2/)。

## 队列

先入先出（First In First Out）的数据结构，只能在队首删除，队尾插入。

### 队列的抽象数据类型描述

类型名称：队列（Queue）

操作对象集：一个有 0 个或多个元素的有穷线性表

操作集：

+ bool isFull()：判断队列是否已满
+ bool push(ElementType x)：把元素 x 插入队列中，返回插入是否成功
+ bool isEmpty()：判断队列是否为空
+ ElementType pop()：弹出队首元素并返回

### 队列的实现

#### 数组存储

我们使用“环形”数组来存储元素，避免经过多次进出队列后，数组仍然有空间，但是 `front` 和 `rear` 已经超出下标范围，此时注意 `front` 和 `rear` 的差值只有 `maxSize` 种可能，但是数据的摆放有 `maxSize+1` 种可能，因此此时数组空间应该只用 `maxSize-1` 位，留出一个空位，那么为空的判断是 `front==rear`，满的判断是 `(rear + 1) % maxSize == front`。`front` 规定为**（队首元素下标 - 1）mod maxSize**，`rear` 规定为**队尾元素下标**，则队列的长度应该为 `(front-rear) % maxSize`。

代码：

```cpp
template <typename ElementType>
class Queue {
    ElementType* data;
    int maxSize;
    int front;
    int rear;

public:
    Queue(int maxSize)
        : maxSize(maxSize) {
        data = new ElementType[maxSize];
        front = rear = 0;
    }

    ~Queue() {
        delete[] data;
    }

    bool isFull() {
        return (rear + 1) % maxSize == front;
    }

    bool isEmpty() {
        return rear == front;
    }

    bool push(ElementType x) {
        if (isFull()) {
            return false;
        }
        rear = (rear + 1) % maxSize;
        data[rear] = x;
        return true;
    }

    ElementType pop() {
        // 队列为空应该报错
        front = (front + 1) % maxSize;
        return data[front];
    }
};
```

#### 链表存储

如果使用单向链表，此时由于链表表尾不能访问前一个节点，因此不适合进行删除操作，只能作队尾，而链表表头则用来作队头。

代码：

```cpp
class QNode {
public:
    int data;
    QNode* next;

    QNode(int data = 0)
        : data(data) {
        next = nullptr;
    }
};

class Queue {
    QNode* front;  //指向链表头指针（不存储内容）
    QNode* rear;   //指向链表末尾

public:
    Queue() {
        front = new QNode;
        rear = nullptr;
    }

    ~Queue() {
        QNode* node = front;
        QNode* nextNode = front->next;
        while (true) {
            if (node == nullptr) {
                break;
            }
            nextNode = node->next;
            delete node;
            node = nextNode;
        }
    }

    bool isEmpty() {
        return rear == nullptr;
    }

    void push(int x) {  //一定成功
        QNode* node = new QNode(x);
        if (rear == nullptr) {
            rear = node;
            front->next = rear;
        }
        else {
            rear->next = node;
            rear = node;
        }
    }

    int pop() {
        QNode* node = front->next;
        front->next = node->next;
        int x = node->data;
        delete node;
        return x;
    }
};
```

## 题解：一元多项式的加法和乘法

### 题目描述

设计函数分别求两个一元多项式的乘积与和。

### 输入格式

输入分 2 行，每行分别先给出多项式非零项的个数，再以指数递降方式输入一个多项式非零项系数和指数（绝对值均为不超过 1000 的整数）。数字间以空格分隔。

### 输出格式

输出分 2 行，分别以指数递降方式输出乘积多项式以及和多项式非零项的系数和指数。数字间以空格分隔，但结尾不能有多余空格。零多项式应输出 `0 0`。

### 输入样例

```in
4 3 4 -5 2  6 1  -2 0
3 5 20  -7 4  3 1//结尾无空格和空行
```

### 输出样例

```out
15 24 -25 22 30 21 -10 20 -21 8 35 6 -33 5 14 4 -15 3 18 2 -6 1
5 20 -4 4 -5 2 9 1 -2 0//结尾无空格和空行
```

### 解题思路

可以使用链表或结构数组表示多项式。

#### 加法

使用两个指针分别指向两个多项式，按照次数向后迭代，把得到的结果加在结果多项式上（因此需要写一个 `append()` 方法）。

#### 乘法

采用两层循环：用一个多项式去乘另一个多项式的每一项（类似于列竖式乘法）。每乘完一项得到一个多项式，用多项式的 `add()` 方法（之前在加法部分写好了）加到结果多项式中即可。

#### 注意事项

首先是输入时，如果输入了零多项式，其项数是 0 而不是1，输入只有一行 `0`，而不是 `1 0 0`。其次，在输出时，不能输出多余的空格，且末尾没有空行，最后，对于链表结构，记得释放存储空间（写析构函数），并且访问每个指针之前都要先判断其不为空，否则会报错。

链表代码：

```cpp
#include <iostream>
using namespace std;

class PolyNode {
public:
    int coef;   //系数
    int expon;  //指数
    PolyNode* next;
    PolyNode(int coef = 0, int expon = 0)
        : coef(coef), expon(expon) {
        next = nullptr;
    }
};

class Polynomial {
public:
    PolyNode* head = nullptr;  //头节点，不存储任何数据
    PolyNode* tail = nullptr;  //尾节点，指向最后一项（指数最小）

    Polynomial() {  //无参构造函数
        head = new PolyNode;
    }

    void append(PolyNode* node) {  //添加项
        if (tail == nullptr) {
            tail = node;
            head->next = tail;
        }
        else {
            tail->next = node;
            tail = node;
        }
    }

    Polynomial(int num) {  //直接读取多项式，参数为项数
        head = new PolyNode;
        int co, ex;
        while (num--) {
            cin >> co >> ex;
            append(new PolyNode(co, ex));
        }
    }

    ~Polynomial() {  //析构
        PolyNode* node = head;
        PolyNode* nextNode;
        while (true) {
            if (node == nullptr) {
                break;
            }
            nextNode = node->next;
            delete node;
            node = nextNode;
        }
    }

    // Polynomial(const Polynomial& poly) {  //复制构造函数
    //     head = poly.head;
    //     tail = poly.tail;
    //     PolyNode* node = head;                 //防止head被修改，复制一下
    //     PolyNode* polyNode = poly.head->next;  //指向需要复制的多项式
    //     while (polyNode != tail) {
    //         node->next = polyNode;
    //         node = node->next;
    //         polyNode = polyNode->next;
    //     }
    // }

    Polynomial* add(Polynomial* poly) {  //this和poly相加，返回结果多项式
        Polynomial* result = new Polynomial();
        PolyNode* poly1 = head->next;
        PolyNode* poly2 = poly->head->next;
        while (poly1 && poly2) {  //两个多项式都不空，两个指针分别向后迭代，比较系数
            if (poly1->expon > poly2->expon) {
                result->append(new PolyNode(poly1->coef, poly1->expon));
                poly1 = poly1->next;
            }
            else if (poly2->expon > poly1->expon) {
                result->append(new PolyNode(poly2->coef, poly2->expon));
                poly2 = poly2->next;
            }
            else {
                int c = poly1->coef + poly2->coef;
                if (c != 0) {
                    result->append(new PolyNode(c, poly1->expon));
                }
                poly1 = poly1->next;
                poly2 = poly2->next;
            }
        }
        //处理尾项，挨个接在结果多项式上，虽然有两个循环，但是只会进入一个
        while (poly1) {
            result->append(new PolyNode(poly1->coef, poly1->expon));
            poly1 = poly1->next;
        }
        while (poly2) {
            result->append(new PolyNode(poly2->coef, poly2->expon));
            poly2 = poly2->next;
        }
        return result;
    }

    Polynomial* mul(Polynomial* poly) {  //this和poly相乘，返回结果多项式
        Polynomial* result = new Polynomial();
        PolyNode* poly1 = head->next;
        if (poly1 == nullptr || poly->head->next == nullptr) {
            //如果不是两个都非0，直接返回0多项式
            result->append(new PolyNode());
            return result;
        }
        while (poly1) {
            //让poly1的每一项与poly2相乘，每次乘完得到sum，再把sum加到result里
            Polynomial* sum = new Polynomial();
            PolyNode* poly2 = poly->head->next;
            while (poly2) {
                sum->append(new PolyNode(poly1->coef * poly2->coef, poly1->expon + poly2->expon));
                poly2 = poly2->next;
            }
            Polynomial* tmp = result->add(sum);
            delete sum;
            delete result;
            result = tmp;
            poly1 = poly1->next;
        }
        return result;
    }

    void show() {  //输出该多项式，注意只要输出非零项
        PolyNode* node = head->next;
        bool isZero = true;
        PolyNode* tmp = node;
        while (node) {  //0多项式特判
            if (node->coef != 0) {
                isZero = false;
                break;
            }
            node = node->next;
        }
        if (isZero) {
            cout << "0 0";
            return;
        }
        node = tmp;

        while (node) {
            cout << node->coef << " " << node->expon;
            if (node->next != nullptr) {
                cout << " ";  //防止结尾多输出空格
            }
            node = node->next;
        }
    }
};

int main() {
    int num;
    cin >> num;
    Polynomial* poly1 = new Polynomial(num);
    cin >> num;
    Polynomial* poly2 = new Polynomial(num);
    Polynomial* product = poly1->mul(poly2);
    product->show();
    Polynomial* sum = poly1->add(poly2);
    cout << endl;
    sum->show();
}
```

数组代码：

```cpp
#include <iostream>
using namespace std;

class PolyNode {
public:
    int coef, expon;
    PolyNode(int coef = 0, int expon = 0)
        : coef(coef), expon(expon) {}
};

class Polynomial {
public:
    PolyNode data[1005];
    int tail = -1;  //最后一个元素的位置（闭区间）
    Polynomial() {}

    Polynomial(int) {  //加占位符区别上面的构造函数
        int num;
        cin >> num;
        for (int i = 0; i < num; ++i) {
            cin >> data[i].coef >> data[i].expon;
        }
        tail = num - 1;
    }

    void show() {
        if (tail == -1) {
            cout << "0 0";
            return;
        }
        for (int i = 0; i < tail; ++i) {
            cout << data[i].coef << " " << data[i].expon << " ";
        }
        cout << data[tail].coef << " " << data[tail].expon;
    }

    void append(PolyNode node) {
        data[++tail] = node;
    }

    Polynomial add(Polynomial& poly) {
        int newTail = -1;              //新多项式的尾
        if (poly.data[0].coef == 0) {  //和0相加，直接返回自己
            return *this;
        }
        Polynomial result = Polynomial();
        int i = 0, j = 0;
        while (i <= tail && j <= poly.tail) {
            if (data[i].expon > poly.data[j].expon) {
                newTail++;
                result.append(data[i]);
                i++;
            }
            else if (data[i].expon < poly.data[j].expon) {
                newTail++;
                result.append(poly.data[j]);
                j++;
            }
            else {
                int c = data[i].coef + poly.data[j].coef;
                if (c != 0) {
                    PolyNode node = PolyNode(c, data[i].expon);
                    result.append(node);
                    newTail++;
                }
                i++, j++;
            }
        }
        for (; i <= tail; ++i) {
            result.append(data[i]);
            newTail++;
        }
        for (; j <= poly.tail; ++j) {
            result.append(poly.data[j]);
            newTail++;
        }
        result.tail = newTail;
        return result;
    }

    Polynomial mul(Polynomial& poly) {
        Polynomial result = Polynomial();
        for (int i = 0; i <= tail; ++i) {
            Polynomial sum = Polynomial();
            int c = data[i].coef;
            int e = data[i].expon;
            for (int j = 0; j <= poly.tail; ++j) {
                PolyNode node = PolyNode(c * poly.data[j].coef, e + poly.data[j].expon);
                sum.append(node);
            }
            result = result.add(sum);
        }
        return result;
    }
};

int main() {
    Polynomial p1 = Polynomial(1);
    Polynomial p2 = Polynomial(1);

    Polynomial product = p1.mul(p2);
    product.show();
    cout << endl;
    Polynomial sum = p1.add(p2);
    sum.show();
}
```

注：乘法也可以采用一项一项插入的方式来进行，但是此时需要找到插入位置，不能复用已经有的代码，比较麻烦。
