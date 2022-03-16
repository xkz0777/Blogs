---
title: Shell 编程
date: 2021-08-31 12:26:40
tags: Shell
Categories: Linux
img: /medias/featureimages/linux_shell.jpg
author: xkz
summary: Shell 编程
---

# Shell 编程

## Shell 是什么

Shell 是一个命令行解释器，为用户提供了一个向 Linux 内核发送请求以便运行程序的界面系统级程序. 

Shell 是用 C 语言编写的程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务. 

Ken Thompson 的 sh 是第一种 Unix Shell，Windows Explorer 是一个典型的图形界面 Shell. 

Linux 下的 Shell 种类很多，但大多数的 Linux 系统默认的 Shell 都是 Bash，也就是 Bourne Again Shell. 

## Shell 脚本

用 Bash 执行的 Shell 脚本第一行一定是

```bash
#!/bin/bash
```

表明该脚本需要用 Bash 执行，Bash 解释器的路径为 `/bin/bash`（注意前面的 `/`）. 

### 第一个 Shell 脚本

命名为 `test.sh`，内容如下: 

```bash
#!/bin/bash
echo "hello world"
```

### 运行 Shell 脚本

有两种方式: 

+ 给 Shell 脚本可执行权限，然后使用 `./Shell 脚本名` 即可运行，如 `./test.sh`. 

	注意，一定要写成 `./test.sh`，而不是 `test.sh`，运行其它二进制的程序也一样，直接写 `test.sh`，Linux 系统会去 `PATH` 里寻找有没有叫 `test.sh` 的，而只有 `/bin`, `/sbin`, `/usr/bin`，`/usr/sbin` 等在 `PATH` 里，你的当前目录通常不在 `PATH` 里，所以写成 `test.sh` 是会找不到命令的，要用 `./test.sh` 告诉系统说，就在当前目录找. 

+ `sh test.sh` 此时不需要可执行权限，也可以使用绝对路径. 

## Shell 语法

### 变量

在 Shell 中，有系统变量和用户自定义变量，系统变量包括 HOME（存储当前用户家目录）、PWD（当前脚本所在路径（通常只到文件夹））、SHELL（为执行该脚本的 Shell 解释器的路径）、USER（用户名）、PATH（环境变量）等，还有一些预定义变量（在后面会详述）. 

使用 set 命令可以显示当前 Shell 中的所有变量. 

#### 定义变量

使用 `变量名=值` 的格式即可定义变量，变量名的规则如下: 

+ 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头. 
+ 中间不能有空格，可以使用下划线 `_`. 
+ 不能使用标点符号. 
+ 不能使用 Bash 里的关键字（可用 help 命令查看保留关键字）. 

例如: 

```bash
var=100
```

即可声明一个变量. 

注意: 

+ 定义时，变量名前不用 $ 符. 

+ 等号左右不能有任何空格！

+ 值可以是数字、字符串，也可以是命令的返回值，有两种格式: 

	```bash
	var=`date`
	```

	或

	```bash
	var=$(date)
	```

除了显式地直接赋值，还可以用语句给变量赋值，如: 

```bash
for file in `ls /etc`
或
for file in $(ls /etc)
```

#### 使用变量

使用时，如果需要取值，前面加 \$ 符，如果作左值，不需要加 $ 符. 

例如: 

```bash
#!/bin/bash
var=100
echo $var
sum=0
for(( i=1;i<=$var;i++ ))
do
    sum=$[$sum+$i]
done
echo "1到100的总和为$sum"
```

运行后得到: 

```
100
1到100的总和为5050
```

取值时，可以为变量名加上花括号，以便解释器识别变量的边界，例如: 

```bash
for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Script"
done
```

#### 删除/撤销变量

使用 unset 命令. 

例如: 

```bash
#!/bin/bash
var=100
echo $var
unset var
echo $var
```

执行后得到: 

```
100
#这里是空行
```

#### 只读变量

可以用 readonly 声明某个变量为只读变量，其值不可被修改，例如

```bash
readonly B=100
```

或

```bash
B=100
readonly B
```

两种方式都可以把 B 声明为只读变量. 

注意: 只读变量不可被 unset. 

#### 设置环境变量

`export` 命令可以显示当前系统定义的所有环境变量，`echo $PATH` 命令输出当前的 PATH 环境变量的值. 

如果要设置环境变量，只要 `vim /etc/profile`，然后使用 `export` 命令，例如

```shell
export JAVA_HOME=/usr/local/jdk1.8.0_301
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```

在修改该配置文件后，使用 `source` 命令刷新该配置文件才能使得配置生效: `source /etc/profile`. 

#### 位置参数变量

当我们在命令行执行 shell 脚本时，可以带上参数，参数之间用空格分隔，例如: 

```shell
sh test.sh 100 200
```

或者

```shell
./test.sh 100 200
```

有多种方式访问位置参数变量: 

1. `${n}`，n 为数字，访问第 n 个参数变量，其中 n 为 0 - 9 时可以不用括号. 

	注意，命令本身也作为一个参数，例如上面两个例子中， `$0` 分别为 test.sh 和 ./test.sh. 

2. `$#`，代表命令行中所有参数的个数（不包含 `$0`，例如上面两个例子，`$#` 都是 2. 

3. `$*` 和 `$@` 都代表命令行所有的参数，区别为，`$*` 将输入的参数作为整体，`$@` 则可以通过 for 循环分别得到所有参数，这个之后再 for 循环处会提到. 

#### 预定义变量

1. `$$` 表示当前进程的进程号（PID）. 
2. `$!` 表示后台运行的最后一个进程的进程号（PID）
3. `$?` 表示最后一次执行的命令的返回状态，如果这个变量的值为 0，说明上一个命令正常执行；如果非 0（具体是哪个数由命令决定），说明上一个命令非正常执行. 

例如: 

```shell
#!/bin/bash
echo "当前执行的进程id=$$"
#下面以后台的方式运行一个脚本，并获取它的进程号
/root/test.sh &
echo "最后一个以后台方式运行的进程id=$!"
echo "执行的结果是$?"
```

### 注释

#### 单行注释

用 `#` 号，`#` 后面的部分会被当做注释

#### 多行注释

```shell
:<<!
# 注释内容块
!
```

注意 `:<<!` 和 `!` 应该各自独占一行. 

也可以采用下面的方式: 

```shell
if false; then
# 注释内容块
fi
```

（这严格意义上已经不算是注释了）

### 四则运算

通常采用 $[运算式的方式]（还有其他方式，但是比较复杂，不建议采用）. 

例如: 计算 （2+3）*4 的值: 

```shell
echo $[(2+3)*4]
```

也可以先存在变量里再输出: 

```shell
var=$[(2+3)*4]
echo $var
```

两种方式都可以得到 20. 

### 条件判断

基本语法为: 

```shell
if [ condition ]
then
	代码块
elif [ condition ]
then
	代码块
else
	代码块
fi
```

可以有多个 elif. 

其中，条件的格式为 `[ condition ]`，例如 `[ "ok" = "ok" ]`，condition 和旁边的括号之间**一定要有空格**. 

如果 condition 为一个字符串，那么非空表示 true，空表示 false，例如 `[ xkz ]` 为 true，而 `[ ]` 为 false. 

常用判断条件: 

`=`: 字符串相等（不是两个等号）

数字比较: 

`-lt`: 小于（less than）

`-le`: 小于等于（less than or equal to）

`-eq`: 等于（equal to）

`-gt`: 大于（greater than）

`-ge`: 大于等于（greater than or equal to）

`-ne`: 不等于（not equal to）

例如: 

```shell
if [ 23 -gt 22 ]
then
	echo "大于"
fi
```

按照文件权限进行判断: 

`-r`: 可读

`-w`: 可写

`-x`: 可执行

例如: 

```shell
if [ -x /root/test.sh ]
then
    echo "executable"
fi
```

按照文件类型: 

`-f`: 文件存在并且是一个常规的文件

`-e`: 文件存在

`-d`: 文件存在并且是一个目录

例如: 

```shell
if [ -e /root/test.sh ]
then
    echo "/root/test.sh exists"
fi
```

### 循环

#### for 循环

基本语法1: 

```shell
for 变量 in 值1 值2 值3
do
	代码块
done
```

例如: 

```shell
for i in $@
do
    echo $i
done
```

此时输入几个额外参数，输出就有几行. 如果把 `$@` 换成 `$*`，则只会输出一行. 

这里第一行**也可以简写为 `for i`**. 

该循环其他常见用法: 

```shell
for i in /root/*
for i in /etc/*.conf
for i in $(seq -w 10) #等宽的01、02...10，去掉-w参数变为1、2...10
for i in {1..10} #1、2...10(注意是两个点)
for i in $(ls) #把ls的输出作为列表来遍历
for i in $(< file) #会读取file，并把file的内容分为一个个单词
```

基本语法2: 

```shell
for (( 初始值;循环控制条件;变量变化 ))
do
	代码块
done
```

例如: 

```shell
#!/bin/bash
var=100
echo $var
sum=0
for(( i=1;i<=$var;i++ ))
do
    sum=$[$sum+$i]
done
echo "1到100的总和为$sum"
```

注: 这里括号与中间的内容之间最好要有空格，但不是强制要求. 

#### while 循环

基本语法: 

```shell
while [ condition ]
do
	代码块
done
```

### read 读取控制台输入

基本语法: 

```shell
read [选项] [参数]
```

选项: `-p`: 指定读取值时的提示符. `-t`: 指定读取值时等待的时间（秒），如果没有在指定的时间内输入，就不再等待. 

参数: 指定读取值时的变量名. 

例如: 读取控制台在 10s 内输入一个 num 值: 

```shell
read -p "请输入一个数num=" -t 10 num
echo "你输入的num=$num"
```

### 函数

调用方式: 

```shell
functionName [参数1] [参数2] ...
```

以 `basename` 和 `dirname` 为例: 

```shell
basename [pathname] [suffix]
```

会去掉路径除了文件名以外的部分，如果有后缀，还会去掉后缀，例如: 

```shell
basename /root/test.sh .sh #得到test
```

`dirname` 则用于获取文件的路径部分，例如: 

```shell
dirname /root/test.sh #得到/root
```

#### 自定义函数

基本语法: 

```shell
function functionName(){
	代码块
	[return ...]
}
```

例如: 

```shell
function getSum(){
    sum=$[$n1+$n2]
    echo $sum
}

read -p "请输入一个数n1=" n1
read -p "请输入一个数n2=" n2

getSum
```

该程序可以从控制台读入两个数并返回和. 

注意: 所有函数在使用前必须定义. 

#### 函数参数

调用函数时可以向其传递参数. 在函数体内部，通过 `${n}` 的形式来获取参数的值. 因此上面的程序也可以修改为: 

```shell
#!/bin/bash
function getSum(){
    sum=$[$1+$2]
    echo $sum
}

read -p "请输入一个数n1=" n1
read -p "请输入一个数n2=" n2

getSum $n1 $n2
```

#### 函数返回值

这里的函数返回值非常奇怪，在调用后通过 `$?` 来获得. 例如: 

```shell
#!/bin/bash
function getSum(){
    sum=$[$1+$2]
    return $sum
}

read -p "请输入一个数n1=" n1
read -p "请输入一个数n2=" n2

getSum $n1 $n2
echo $?
```

注意: `$?` 仅对其上一条指令负责，一旦函数返回后其返回值没有立即保存入参数，那么其返回值将不再能通过 `$?` 获得. 

