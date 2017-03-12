---
title: Linux常用命令笔记（二：变量）
date: 2017-03-12 18:30:00
tags: Linux
categories: Linux
---

## 变量

### 基本使用

Bash 里，每个变量的值都是字符串。变量名是区分大小写的。要输出变量值，可以用下面方法中的任意一个：

```
var="some test string."

# 方法①
# 美元符号 $ 用于提取变量值
echo $var

# 方法②
echo ${var}

# 方法③
echo "$var"

# 方法④
# 这种语法，非常像 ES6 里面的模板字符串
echo "${var}"
```

上面说到变量值都是字符串，那么如果想知道字符串的长度，可以通过 `#` 符号来获取：

```
var="some test string."
echo $#var  # 输出 17
```

### 环境变量

** 环境变量是未在当前进程中定义，而从父进程中继承而来的变量。**

用 `env` 命令，可以查看当前系统中、所有与该终端相关的环境变量。

### 常见环境变量

** SHELL **

识别当前所用的是哪种 shell：

```
echo $0  # 输出 -zsh

# 或者
echo $SHELL  # 输出 /bin/zsh
```

我使用的是 oh my zsh。如果用的是默认的 bash shell，应该会输出 `/bin/bash`。

** UID、GID **

当前登录用户的ID，以及其所属分组，分别可以通过 UID、GID 来查看。root 用户的 UID/GID 都是 0。因此可以通过这个来判断当前用户是否为超级用户。

```
if [ $UID -ne 0 ]; then
    echo Not a root user. Plear run as root.
else
    echo Root user.
fi
```

上面的脚本，`-ne` 表示“不等于”的意思（not equal to）。而 `[`、`]` 及其中间的内容，属于条件表达式（Conditional expressions）的一种。条件表达式可以用来测试文件属性，或者对字符串、算术进行比较。


## 获取程序的返回值

假设我们有这样一个 C++ 程序：

```
#include<iostream>
using namespace std;
int main()
{
   cout<<"Hello world!"<<endl;
   return 0;
}
```

使用 g++ 编译后执行。想要获得该程序的返回值（也就是0），可以这样：

```
# 假设编译后得到的可执行文件为 a.out
./a.out

echo $?  # 输出 0
```

