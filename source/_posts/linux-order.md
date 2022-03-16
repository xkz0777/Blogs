---
title: Linux 常用指令
date: 2021-08-31 11:36:06
tags: Linux
categories: Linux
img: /medias/featureimages/linux_order.jpg
author: xkz
summary: Linux 常用指令介绍
---

# Linux 常用指令

## 开机、重启和用户登陆注销

### 关机、重启指令

```shell
$ shutdown -h now
```

立即关机

```shell
$ shutdown -h 1
```

1 分钟后关机

`halt` 也可关机, `reboot` 可以重启, `sync` 可以把内存数据同步到磁盘. 

建议在关机或重启前运行 `sync`(虽然目前系统大多会执行). 

### 用户登陆或注销

登陆时尽量少用 root 账号登陆, 避免操作失误, 普通用户登陆后可以使用 `su - 用户名` 来切换用户. 

在提示符下输入 `logout` 可以注销账户, 但是在图形运行级别无效, 只在运行级别 3 下有效. 

## 用户管理

### 添加用户

```bash
$ useradd 用户名
```

此时会在 `/home` 目录下创建一个用户名对应的文件夹, 为该用户的家目录, 登陆后默认进入该文件夹. 

同时, 会创建一个组, 组名与用户名相同, 该用户即为该组成员. 

```bash
$ useradd -d 指定目录 用户名
```

此时会在指定目录下为该用户创建家目录. 

```shell
$ useradd -g 组名 用户名
```

此时会在指定的组下创建用户. 

### 指定/修改密码

```shell
$ passwd 用户名
```

随后输入用户密码即可. 

### 删除用户

```shell
$ userdel 用户名
```

此时会删除该用户, 但是保留其家目录. 

```shell
$ userdel -r 用户名
```

此时会删除该用户及其家目录(通常两种方式选择前者). 

### 查询用户信息

```shell
$ id 用户名
```

### 切换用户

```shell
$ su - 用户名
```

注意: 

+ 从权限高的用户切换到权限低的用户, 不需要输入密码, 反之需要. 
+ 返回到原来用户使用 `exit` / `logout` 命令. 
+ 如果未切换用户, 使用 `exit` / `logout` 命令会注销当前账号. 

### 查看当前用户/登陆用户

```shell
$ whoami/who am i
```

## 组管理

### 新增组

```shell
$ groupadd 组名
```

### 删除组

```shell
$ groupdel 组名
```

### 修改用户的组

```shell
$ usermod -g 组名 用户名
```

### 修改用户登陆的初始目录

```bash
$ usermod -d 目录名 用户名
```

注意: 此时用户需要有进入到新目录的权限(即可执行 x 权限). 

## 用户和组相关文件

+ `/etc/passwd`

	用户的配置文件, 记录用户的各种信息. 

	每行的含义: 用户名:密码(x):用户标识号:组标识号:注释性描述:主目录:登陆 Shell

+ `/etc/shadow`

	密码的配置文件. 

	每行的含义: 登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志

+ `/etc/group`

	组的配置文件, 记录 Linux 包含的组信息. 

	每行含义: 组名:密码:组标识号:组内用户列表

## 帮助指令

### man

```shell
$ man 命令或配置文件
```

 可以获取命令的帮助信息, 如 `man ls`. 

man 显示的信息往往过于全面, 可以使用第三方工具 [tldr](https://github.com/tldr-pages/tldr) 查看社区维护的精简版文档.

首先需要安装 `tldr`:

```shell
$ apt install tldr
```

之后可以查看文档:

```shell
$ tldr tar

tar

Archiving utility.
Often combined with a compression method, such as gzip or bzip2.
More information: <https://www.gnu.org/software/tar>.

- [c]reate an archive and write it to a [f]ile:
    tar cf target.tar file1 file2 file3

- [c]reate a g[z]ipped archive and write it to a [f]ile:
    tar czf target.tar.gz file1 file2 file3

- [c]reate a g[z]ipped archive from a directory using relative paths:
    tar czf target.tar.gz --directory=path/to/directory .

- E[x]tract a (compressed) archive [f]ile into the current directory [v]erbosely:
    tar xvf source.tar[.gz|.bz2|.xz]

- E[x]tract a (compressed) archive [f]ile into the target directory:
    tar xf source.tar[.gz|.bz2|.xz] --directory=directory

- [c]reate a compressed archive and write it to a [f]ile, using [a]rchive suffix to determine the compression program:
    tar caf target.tar.xz file1 file2 file3

- Lis[t] the contents of a tar [f]ile [v]erbosely:
    tar tvf source.tar

- E[x]tract files matching a pattern from an archive [f]ile:
    tar xf source.tar --wildcards "*.html"
```

### help

```shell
$ help 命令
```

可以获得 shell 内置命令的帮助信息. 

通常, 在执行程序时使用 `-h` 或 `--help` 选项可以打印帮助信息, 以便了解有哪些可用的标记或选项. 

## 文件目录命令

### pwd

打印当前工作目录的绝对路径. 

### ls

查看当前目录的文件, Linux 中, 以 `.` 开头的文件为隐藏文件, 在 `ls` 中不可见. 

常用选项: `-a` 查看所有文件和目录, 包括隐藏的. `-l`: 以列表的方式显示信息, 其中信息格式如下: 

```shell
-rw-r--r--.  1 root root   84 8月  18 19:07 hello.cpp
```

第一个字符表示文件的类型: 

`-` 表示普通文件, `d` 表示 `dirtectory`, `l` 表示链接文件, `p` 表示管理文件, `b` 表示块设备文件(如硬盘), `c` 表示字符设备文件(在 `/dev` 下常见), `s` 表示套接字文件. 

之后每三个字符为一组, 分别表示文件所有者、所有者所在组以及其他人对文件的读(r)、写(w)、可执行(x)权限. 

对 rwx 权限: 

+ 作用于文件

	r 代表可读、查看. 

	w 代表可写、修改, 但**不代表可以删除该文件**, , 删除一个文件的前提是对该文件所在的**目录**有**写(w)**权限. 

	x 代表可执行. 

+ 作用于目录

	r 代表可读, 可以 `ls` 查看目录内容. 

	w 代表可写, 可以修改目录名, 在目录内创建、删除文件和目录. 

	x 代表可以进入该目录(可以 `cd` 进入该目录). 

rwx 权限可以用二进制表示, r = 4, w = 2, x = 1, 因此 rwx = 7

第二字段: 对于目录, 为子目录+文件数, 对于文件, 为硬链接个数. 

第三、四字段为文件所有者及所有者所在组. 

第五字段为文件大小(以字节为单位), 注意, 如果是目录, 则**表示**该目录符所占的大小(即**文件夹本身的大小**), 并**不表示该目录下所有文件的大小**, 如果希望以更适合人类阅读的单位显示, 可以加上参数 `-h`. 

第六字段为文件(目录)最近访问(修改)时间. 

第七字段为文件名. 

### cd

切换到指定目录. 路径可以使用绝对/相对路径, 特别地, `cd ~` 或 `cd` 可以回到家目录. 

### mkdir

用于创建目录, 如果需要创建多级目录, 例如 `./animal/tiger` , 则需要带 `-p` 选项. 

### rmdir

用于删除**空**目录. 

### touch

创建空文件. 

### cp

拷贝文件到指定目录, 用法: `cp [选项] source dest`. 

常用选项: `-r`: 递归复制整个文件夹. 

注意: 如果复制时有文件重复, 系统会逐一进行提示, 如果需要强制覆盖不提示, 可以使用 `\cp` 或带上选项 `-f`. 

### rm

删除文件或目录, 用法: `rm [选项] 要删除的文件或目录`. 

常用选项: `-r`: 递归删除整个文件夹. `-f`: 强制删除不提示. 

### mv

移动文件与目录(如果在同一目录下移动, 实际上是重命名), 移动目录会自动递归移动, 不用带参数. 

常用选项: `-b`: 当目标文件或目录存在时, 在执行覆盖前, 会为其创建一个备份. `-f`: 同名强制覆盖不提示. `-n`: 不要覆盖任何已存在的文件或目录. `-u`: 当源文件比目标文件新或者目标文件不存在时, 才执行移动操作. 

### ln

Linux 中的链接可以分为硬链接(hard link)和软链接(symbolic link). 

软链接也称为符号链接, 以路径的形式存在, 类似于 Windows 里的快捷方式, 主要存放了链接其他文件的路径, 软链接可以跨文件系统, 可以对不存在的目录进行链接, 也可以对目录进行链接. 

硬链接以文件副本的形式存在, 但不占用实际空间, 不允许给目录创建硬链接, 硬链接只有在同一个文件系统中才能创建. 

用法: `ln [选项] <原文件或目录> <目标文件或目录>`. 

常用选项: `-s`: 软链接. `-f`: 强制执行. `-b`: 删除、覆盖以前建立的链接. 

使用 rm 命令可以删除链接. 

注意: 使用 pwd 查看目录时, 看到的仍然是软链接所在目录. 

### history

查看已经执行过的历史命令. 

用法: 不带参数时查看所有的历史命令；`history n` 可以查看最近使用过的 n 条指令. `!n` 可以执行历史编号为 n 的指令. 

## 文件查看

### cat

查看文件内容. 

常用选项: `-n`: 显示行号. `-b`: 显示行号, 但空白行不编号. 

对于大文件, 宜采用 `less` 指令查看文件, 具有更高的效率. 

### more 和 less

more 是基于 vi 的文本过滤器, 以全屏幕的方式按页显示文本文件的内容. 

在 more 中的操作: 

`空格` 向下翻页, `Enter` 向下一行. 

`Ctrl+F` 向下滚动一屏, `Ctrl+B` 返回上一屏. 

`=` 输出当前行号, `:f` 输出文件名和当前行号, `q` 离开 more. 

less 命令用于分屏查看文件内容, 但它不会将一整个文件加载之后才显示, 在查看大文件时具有较高的效率. 

less 下的操作: 

`空格/pagedown` 向下翻页, `pageup` 向上翻页. 

`/(?)` 分别用于向下(向上)搜寻字串, n: 向下(上)查找, N: 向上(下)查找. 

`q` 离开 less. 

### echo

可以使用 echo 命令输出环境变量, 如 `echo $PATH`、`echo $HOSTNAME` 等. 

### head

用于显示文件的开头内容, 默认显示前十行, 用法: `head [选项] 文件`. 

常用选项: `-n x`: 查看文件的前 x 行内容. 

### tail

显示文件的尾部内容, 默认显示后十行. 

常用选项: `-n x`: 查看文件后 x 行内容, `-f` **实时追踪该文件的所有更新**. 

### 重定向

重定向输出: \> 为覆盖写(文件不存在会自动创建), \>> 为追加写(会自动添加换行符). 

同理, < 和 << 用于重定向输入. 

例如: `ll -a > fileInfo` 把目录下的文件信息写入 `fileInfo` 文件中；`cat file1 > file2` 把 `file1` 的内容覆盖到 `file2`. 

## 时间日期命令

### date

用法: 不带参数显示当前时间, 如果要格式化输出, 使用 `date "+%Y-%m-%d %H:%M:%S"` 格式. `date -s 字符串时间` 可以设置系统当前时间. 

### cal

查看日历, 不带参数显示本月日历, 显示 2020 年日历: `cal 2020`. 

## 搜索查找类

### find

用法: `find <指定目录> <指定条件> <指定动作> <文件或目录名>`

未指定目录默认为当前目录. 目录可以有多个, 目录之间要用空格分开. 

指定条件: 

+ 按名字查找: 

	```bash
	$ find <指定目录> -name <文件或目录名>
	```

	当参数为 `-iname` 时不区分大小写. 

+ 按拥有者/所有组查找: 

	```shell
	$ find <指定目录> -user/-group <文件或目录名>
	```

	把参数换成 -uid 或 -gid 为按 uid 和 gid 查找. 

+ 按文件大小查找

	```shell
	$ find <指定目录> -size [+/-]大小
	```

	例如: 

	```shell
	$ find / -size +200M
	```

	\+ 大于、- 小于、没有符号为等于, 单位有 k, M, G 等. 

+ 按文件类型查找

### locate

locate 可以根据实现建立的 locate 数据库快速定位给定的文件, 只要使用 `locate 搜索文件` 即可, 查找速度极快, 查找前需要使用 `updatedb` 更新数据库. 

### which

可以查看某个指令在哪个目录下. 

### grep

grep 用于过滤查找内容. 用法: `grep [-选项] 查找内容 源文件`. 

`-n` 选项显示行号, `-i` 忽略大小写, 常配合管道符号 |(pipe)使用. 

### 管道符号(pipe)

如 `cat hello.txt | grep "yes"`, 可以把前面的输出作为后面的输入. 

## 压缩和解压类

### `.gz` 文件

用 gzip 压缩, gunzip 解压. **只能用于压缩文件**. 

### `.zip` 文件

用 zip 压缩, unzip 解压. 

压缩时, 用法为 `zip [-选项] 压缩文件名.zip`, 如果压缩的为目录, 需要带 `-r` 选项. 

解压时, 用法为 `zip [-选项] 待解压文件`, 如果需要指定解压目录, 需要带 `-d 待解压目录` 选项. 

### `.tar.gz` 和 `.tar.bz2` 文件

用 tar 命令压缩和解压. 

对于 `.tar.gz` 文件: 

压缩时为 `tar -zcvf 文件/目录 文件/目录`. 其中不同的文件或目录用空格分隔即可. 

解压时用 `tar -zxvf 待解压文件 [-C 解压到目录]`. 默认解压到当前目录. 

其中 `-z` 表示使用 gzip 算法处理文件, `-c` 为压缩(注意小写与大写不同), `-x` 是解压, `-v` 显示具体信息, `-f` 使用指定存档文件文件.

对于 `.tar.bz2` 文件: 

只要把前面的 z 换成 j 即可. 

## 文件所有者、所有组、权限管理类

### 查看文件/目录所有者和所在组

使用 `ll` 或 `ls -l` 即可. 

### 修改文件/目录所有者

```bash
$ chown [选项] 用户名 文件/目录名
```

其中, 如果是目录, 可以使用 `-R` 选项, 使得对其下所有子文件和目录递归生效. 

### 修改文件/目录所在组

```bash
$ chgrp [选项] 组名 文件/目录名
```

也有 -R(recursive)递归选项. 

### 修改权限

使用 `chmod` 命令, 有两种方式: 

+ 使用 `+ - =` 变更权限: 

	u: 所有者、g: 所在组、o: 其他人、a: 所有人. 

	例如: 

	```bash
	$ chmod u=rwx,g=rx,o=x readme.txt
	```

	```bash
	$ chmod o+w readme.txt
	```

+ 通过数字变更权限, 此时类似于第一种方式中的 `=` 变更权限. 

	上面提到过, rwx 可以用二进制表示, rwx 的有无分别对应 1 和 0, 例如, rwx = 7, rx = 5, wx = 3. 

	上面的例一用数字方式可以写为: 

	```bash
	$ chmod 751 readme.txt
	```


## 下载类

### Wget

wget 支持 http 和 ftp 协议, 并且可以递归遍历, 非常适合用于下载文件.

用法: `wget [选项] [url]`

常用选项:

| 选项                       | 含义                                   |
| :------------------------- | :------------------------------------- |
| -i, --input-file=文件      | 下载本地或外部文件中的 URL             |
| -O, --output-document=文件 | 将输出写入文件                         |
| -b, --background           | 在后台运行 wget                        |
| -d, --debug                | 调试模式, 打印出 wget 运行时的调试信息 |

例如 oh-my-zsh 官网给出的下载方式为:

```shell
$ sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

`-` 表示标准输出, wget 会默认下载 install.sh 到当前文件夹, 重定向到标准输出后用 sh 执行

### cURL

| 选项 | 含义                                                      |
| :--- | :-------------------------------------------------------- |
| -o   | 把远程下载的数据保存到文件中, 需要指定文件名              |
| -O   | 把远程下载的数据保存到文件中, 直接使用 URL 中默认的文件名 |
| -I   | 只展示响应头内容                                          |

例如:

```shell
$ curl http://cn.bing.com # 将得到 bing 页面 html 源码, 可以通过 -o 或者重定向存到文件里.
```

## 进程管理

ubuntu 上常用的进程管理器为 htop, 可以通过 apt 安装.

每个进程有对应的**进程标识符(PID, Process Identifier)**.

### 父子进程

除了最开始的 0 号进程外, 其他进程一定由另一个进程通过 fork 产生, 显然产生进程的一方为**父进程**, 被产生的是**子进程**, 在 Linux 中, 父进程可以等待子进程, 接收子进程退出信号以及返回值.

| 进程属性 | 意义/目的                                                    |
| :------- | :----------------------------------------------------------- |
| PID      | Process ID, 标识进程的唯一性.                                |
| PPID     | Parent PID, 标识进程父子关系.                                |
| PGID     | Process Group ID, 标识共同完成一个任务的整体.                |
| TPGID    | 标识一组会话中处于前台（与用户交流）的进程（组）.            |
| SID      | Session ID, 标识一组会话, 传统意义上标识一次登录所做的任务的集合, 如果是与具体登录无关的进程, 其 SID 被重置. |
| USER/UID | 标识进程的权限.                                              |
| Priority | 标识进程的重要性, 值越大越得到优先处理(用于描述实时进程).    |
| Nice     | 普通进程的优先级标度, 越 "nice" 优先级越低.                  |
| State    | 标识进程的状态：能不能运行 (running / sleep), 能不能投入运行 (interruptible / uninterruptible), 让不让运行 (stop / trace), 程序还在不在 (zombie). |
