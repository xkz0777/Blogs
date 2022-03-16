---
title: 程序设计II上机：逆逆波兰式
date: 2021-07-20 00:22:44
tags: ['C/C++','递归','栈']
author: xkz
categories: 'C/C++'
img: /medias/featureimages/sj2.jpg
summary: 程序设计II上机题解之逆逆波兰式
---

## 逆逆波兰式

### 题目描述

仔细学习老师关于“递归”的ppt之后，你学会了如何将一个逆波兰式转换回原本的表达式；只是，有些时候，你会输出大量多余的括号。这次，我们仍旧要将一个逆波兰式转换回一个正常的表达式—— **但不能输出任何多余的括号** 。

“有用的括号”定义如下：括号在和不在会**导致运算顺序发生变化**的，那括号就是有用的，不属于多于括号；

如果某对括号不属于“有用括号”，认为其为多余括号，需要删去， 参考题目中的样例。

### 输入描述

输入有且只有一行，行末回车符前只会出现 “+”，“-”，“*”，“/” 和数字 “0” 到 “9” ；数字**至少出现一次**，每一个数字代表一个单独的数值（原表达式和逆波兰式中的数都是一位数）。

### 输出描述

输出包括一行，为输入逆波兰式对应的原表达式，但不能有任何**多余的括号**！

### 样例输入

```
123++
```

### 样例输出
```
1+(2+3)
```

该例中括号是有用的，因为如果去掉括号，尽管计算结果没变，但是计算顺序会发生变化。

### 样例输入
```
12+3+
```

### 样例输出
```
1+2+3
```

不需要写成 `(1+2)`，因为括号在和不在的计算顺序是一致的。

### 吐槽

逆逆波兰式这个名字取得实在非常难听，并且我感觉存在歧义。

### 解题思路

#### 可以带多余括号时

要解决此问题，首先需要学会把逆波兰式（后缀表达式）转换为等价的中缀表达式（可能带多余括号），之后再考虑如何避免带上多余的括号。

题目的本意希望我们用递归解决问题，但是~~我觉得栈比较好用~~，所以这里两种做法都介绍一下。

##### 使用递归

当我们获取一个逆波兰式的输入时，根据逆波兰式的**定义**，该输入可以递归地往下划分：根据最后一位操作符 op ，表达式的值 = exp1 op exp2，其中 exp1 和 exp2 也都是表达式，而递归的终点则是当表达式为某个**数**时，它的返回值就是本身，不必再继续向下递归。

因此我们要做的事就是，获取一个字符串，并用递归函数处理它，最后输出我们想要的表达式，在这个过程中**重点是如何判断 exp1和 exp2 如何分隔**，也即从后往前看时，什么时候 exp2 完结。

注意到从后往前看时，如果某个表达式还未完结，只有两种可能，第一种可能是**遇到了加减乘除中的符号** ，使得它又能继续递归地被划分为其他表达式。第二种可能则是尽管遇到了数字，但是由于每个 op 之后要跟着两个表达式，这个数字可能属于表达式的第一部分，因此没有完结。但是不论如何，由于表达式中的运算符都是**双目**，因此当**数字数目 - 运算符数目 = 1**，表达式就结束了。

基础版（未去掉多余括号）代码如下：

```cpp
void solve(string s) {
    int l = s.length();
    if (l == 1) {
        cout << s;
        return;
    }
    int cnt = 0;  //cnt表示当前读到的数字个数-运算符个数
    for (int i = l - 2; i >= 0; --i) {
        if (isNumber(s[i])) {
            cnt++;
        }
        else {
            cnt--;
        }
        if (cnt == 1) {  //该表达式结束，这时应该截取[0,i)段作为exp1，[i,s.length()-1)段，作为exp2
            cout << "(";
            solve(s.substr(0, i));
            cout << ")";
            cout << s[l - 1];
            cout << "(";
            solve(s.substr(i, l - i - 1));
            cout << ")";
            break;
        }
    }
}
```
**去括号环节：**

我们本来是无脑加入括号，自然会导致括号的重复，而按照题述，对于 exp1 op exp2 形式的表达式，exp1 如果需要加括号，必须是 exp1 中的运算符 op1 的优先级**严格低于** op （因为从左到右计算），exp2 如果需要加括号，则是 op2 优先级**小于等于** op

这里我们在递归过程中加入判断，变成如下版本

```cpp
#include <iostream>
#include <string>
using namespace std;

bool isNumber(char c) {
    return c >= '0' && c <= '9';
}

bool isOp(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/';
}

int priority(char op) {  //运算符优先级
    if (op == '+' || op == '-') {
        return 0;
    }
    else {
        return 1;
    }
}

void solve(string s) {
    int l = s.length();
    if (l == 1) {
        cout << s;
        return;
    }
    int cnt = 0;  //cnt表示当前读到的数字个数-运算符个数
    for (int i = l - 2; i >= 0; --i) {
        if (isNumber(s[i])) {
            cnt++;
        }
        else {
            cnt--;
        }
        if (cnt == 1) {  //该表达式结束，这时应该截取[0,i)段作为exp1，[i,s.length()-1)段，作为exp2
            string s1 = s.substr(0, i);
            int l1 = s1.length();
            string s2 = s.substr(i, l - i - 1);
            int l2 = s2.length();
            if (isOp(s1[l1 - 1]) && priority(s1[l1 - 1]) < priority(s[l - 1])) {
                cout << "(";
                solve(s1);
                cout << ")";
            }
            else{
                solve(s1);
            }
            cout << s[l - 1];
            if (isOp(s2[l2 - 1]) && priority(s2[l2 - 1]) <= priority(s[l - 1])) {
                cout << "(";
                solve(s2);
                cout << ")";
            }
            else {
                solve(s2);
            }
            return;
        }
    }
}

int main() {
    string s;
    getline(cin, s);
    solve(s);
}
```

##### 使用栈

用栈来解决此题的关键是确定栈中存放的元素是什么，如果元素是一个个字符，其实会带来麻烦，因为不好添加括号，因此我们**把一个个表达式作为元素**。

思路：给到一个表达式exp，读进来时，**一个个字符读，单个数作为表达式输入**（正好是递归的相反过程，我们从最小的表达式出发，一步步合成为最终所得的表达式），存进ans栈中，**遇到运算符则从栈顶弹出两个表达式进行运算，得到的表达式再入栈。**

由于我们需要解决括号问题，不免涉及到表达式的优先级问题，因此我将表达式设为 `std::pair<std::string,int> `类型，其中int表示表达式的优先级，优先级约定如下：单个数字的优先级最高（这样可保证在添加括号时，不会出现单个数字被括号包围），为 3，乘除为 2，加减为 1。是否添加括号的规则与“递归”中的一致，这里不再赘述。

题目的描述中说不可使用栈，因还需手写一个栈。

### AC代码

```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
#include <string>
using namespace std;

int opCode(char c) {  //返回运算符c的优先级，+-为1，乘除为2，纯数字为3（最先算）
    if (c == '+' || c == '-') {
        return 1;
    }
    else {
        return 2;
    }
}

bool isNumber(char c) {//判断字符c是否是数字
    return c >= '0' && c <= '9';
}

template <typename arg>
class Stack {
public:
    arg expression[1000];//栈数据
    int top;
    Stack() {//构造函数
        top = -1;
    }
    bool empty() {//是否为空
        return top == -1;
    }
    arg pop() {//弹出栈顶
        return expression[top--];
    }
    void push(const arg& s) {//入栈
        expression[++top] = s;
    }
};

int main() {
    Stack<pair<string, int>> ans;  //第二个参数
    //表示该表达式中运算符的优先级，+-为1，乘除为2，纯数字为3
    string exp;//原始输入
    cin >> exp;
    pair<string, int> c1, c2;
    for (int i = 0; i < exp.length(); ++i) {
        if (isNumber(exp[i])) {//数字直接入栈
            c1.first = exp[i];
            c1.second = 3;
            ans.push(c1);
        }
        else {
            c2 = ans.pop();
            c1 = ans.pop();
            int n1, n2, n3;
            n1 = c1.second;
            n2 = c2.second;
            n3 = opCode(exp[i]);
            if (n1 < n3) {
                c1.first = "(" + c1.first + ")";
            }//加括号，下同
            if (n2 <= n3) {
                c2.first = "(" + c2.first + ")";
            }
            ans.push(make_pair(c1.first + exp[i] + c2.first, n3));
        }
    }
    cout << ans.pop().first;
}
```

### 补充

本题还有其他解法，如使用“表达式树”来解决此题。首先介绍一下表达式树：按照表达式的递归定义，表达式树可以如下来建：对于表达式 exp（可以写成 exp1 op exp2），父节点是运算符 op，左右子树分别为 exp1 和 exp2，而 exp1 和 exp2 又可以按照这个规则继续递归划分下去，划分的终点是单个数字（和递归做法很类似）。

对于表达式树，我们**分别进行对树的前、中、后序遍历，便可以得到前缀、中缀、后缀表达式**。在这里我们只需要实现中序遍历，并加上括号即可。事实上，我们只要能够建起这颗树，之后加括号的规则其实和上面是一样的。建树的大题思路如下：

以 `123++ `为例，建树的过程应该是从小树到大树：从左到右读取输入，每次遇到一个数字，就是一棵最小的树，**遇到运算符，表明我们可以对两棵树进行合并**，如遇到 `+` ，我们便取前面的两棵树（分别为 2 和 3）建成一棵小树 `2 + 3` ，之后又遇到 `+` 就取出前面的两棵小树，并成我们最后需要的大树。由于这里显然是后入先出的，初始时需要建一个栈（还是离不开我们的老朋友），栈里每个元素都是一个指针指向某个结点结点，如 `123++`，`123 `全部入栈，读到“+”后，从栈顶弹出两个结点，作为“+”的子节点，之后把“+”入栈，重复此操作，直到把序列读完。还需要编写节点 `TreeNode` 类。

下面是AC代码，请有兴趣的读者自己研究（其实说白了就是老懒狗了）。

```cpp
#include <iostream>
#include <string>
using namespace std;
//思路：建一棵表达式数，中序遍历输出

bool isNumber(char);

bool higherRank(char op1, char op2, int tag) {
    //比较op1和op2的优先级，tag为1是比左子树，tag为2比右子树
    //op1是父节点的，op2是子节点的，返回值为true表明要加括号
    if (tag == 1) {  //此时父节点需要严格大于左子树
        return (op1 == '*' || op1 == '/') && (op2 == '+' || op2 == '-');
    }
    else {
        if ((op1 == '+' || op1 == '-') && (op2 == '*' || op2 == '/')) {
            return false;
        }
        return true;
    }
}

class TreeNode {
public:
    char data;
    TreeNode *left, *right;  //子节点
    TreeNode(char c = 0, TreeNode* l = nullptr, TreeNode* r = nullptr)
        : data(c), left(l), right(r) {
    }
    void inOrderTraversal(TreeNode* bt) {
        if (!bt) {
            return;
        }
        if (bt->left) {
            if (!isNumber(bt->left->data) && higherRank(bt->data, bt->left->data, 1)) {
                cout << "(";
                inOrderTraversal(bt->left);
                cout << ")";
            }
            else {
                inOrderTraversal(bt->left);
            }
        }
        cout << bt->data;
        if (bt->right) {
            if (!isNumber(bt->right->data) && higherRank(bt->data, bt->right->data, 2)) {
                cout << "(";
                inOrderTraversal(bt->right);
                cout << ")";
            }
            else {
                inOrderTraversal(bt->right);
            }
        }
    }
};

template <typename arg>
class Stack {
public:
    arg expression[1000];  //栈数据
    int top;
    Stack() {  //构造函数
        top = -1;
    }
    bool empty() {  //是否为空
        return top == -1;
    }
    arg pop() {  //弹出栈顶
        return expression[top--];
    }
    void push(const arg& s) {  //入栈
        expression[++top] = s;
    }
};

bool isNumber(char c) {  //判断字符c是否是数字
    return c >= '0' && c <= '9';
}

int main() {
    Stack<TreeNode*> s;
    string exp;
    cin >> exp;
    TreeNode *t1, *t2, *t;
    for (int i = 0; i < exp.length(); ++i) {
        if (isNumber(exp[i])) {
            s.push(new TreeNode(exp[i]));
        }
        else {
            t2 = s.pop();
            t1 = s.pop();
            t = new TreeNode(exp[i], t1, t2);
            s.push(t);
        }
    }
    t = s.pop();
    t->inOrderTraversal(t);
}
```

