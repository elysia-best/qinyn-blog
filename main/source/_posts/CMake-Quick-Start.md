---
title: CMake简单入门
date: 2024-09-15 20:59:22
tags:
  - CMake
---

# 简介

CMake是一个十分好用的构建系统，它可以帮助大家构建许多的C/C++应用程序。然而，CMake的配置方式十分复杂，网上的资料又令人眼花缭乱。这些困难经常让同学们望而却步。所以，本文将帮助大家轻松了解CMake的使用～

通过这个速通版本，大家会学会如何快速的使用CMake来编译C/C++工程。想要详细学习的同学，可以继续阅读本章剩余小节。

# 基础知识回顾

在开始学习`CMake`之前，大家需要知道以下名词的含义
1. 绝对路径
2. 相对路径
3. 当前目录
4. 父目录
5. 子目录
如果你很确定这些名称的含义，可以跳过这一小节的回顾啦。

### 绝对路径和相对路径

1. 绝对路径是从**硬盘或者根目录**开始。
	1. 在`Windows`当中，绝对路径的起始为硬盘盘符，例子如下：
		1. `C:/Windows/User/Desktop`
	2. 在`Linux`和`Unix`系统中，绝对路径的起始为根目录`/`，例子如下：
		1. `/home/username/Desktop`
### 当前目录、父目录、子目录

1. 当前目录指的是终端或文件所处的位置。
	1. 对于终端，使用`pwd`命令可以直接显示出当前目录的绝对路径。
	2. 对于文件或文件夹，其所处的目录称为该文件的当前目录。
2. 父目录是当前目录的上一级目录。
3. 子目录是当前目录的下一级目录。
# CMake基础

## 最基础的CMakeLists.txt模板

下面是一个最简单的CMakeLists.txt模板，使用它可以编译一个最简单的C++程序。

```CMake
cmake_minimum_required(VERSION 3.5)

# Set the project name
project(hello_cmake)

# add executable files to the project
add_executable(${PROJECT_NAME} main.cpp main.h)
```

接下来，我们会一起来了解这些内容的含义，以及如何增加内容来满足我们的需求～

  

## 注释

与其他的编程语言一样，在CMake中同样可以使用注释。CMake中的注释符号是`#`。这个符号适用于一行文字的注释，下面是一个简单例子：

```CMake
#This is a useless comment
#同样可以写中文
```

## 变量

CMake同样支持定义和使用变量！不过语法比较奇怪，需要大家慢慢来熟悉～ (´▽｀)

### 变量的定义

在CMake当中，使用如下的语法来定义一个变量：

```CMake
set(Name Value)
```

其中，`Name`是变量的名称，`Value`是变量的值。变量的值可以是字符串、数字或者是布尔值（True/False）等等。

例如，

```CMake
# 定义一个布尔变量
set(MyBool True)

# 定义一个字符串 "SomeString"
set(MyString SomeString)
# 也可以带上引号
set(MyString2 "SomeString")

# 定义一些数字
set(MyNumber 123)
```

### 变量的访问

在CMake当中，使用下面的格式访问变量：

```CMake
${Name}
```

其中，`Name`是变量名。

一些使用的例子如下：

```CMake
message(${MyString}) # 打印变量的值
```

我们注意到，`${Name}`这个语句整体，其实就代表了这个变量的值了

  

## 添加源文件（.c/.cpp)

### 基本方法

在上面的最简单的例子当中，我们可以看到，

```CMake
add_executable(${PROJECT_NAME} main.cpp)
```

这个语句是最简单的写法，它将当前目录（根目录）下的`main.cpp`文件添加到了编译目标里面。同时指定了生成的可执行文件（executable）名称为变量`${PROJECT_NAME}`的值。在上面的例子中，这个值就是`hello_cmake`。

### 高级方法

如果只有几个源文件，那么非常好解决！直接像上面的`main.cpp`一样手动添加进来就好~

可是，如果我们有许许多多的文件需要添加，手动修改的方式未免效率低下，同时也难以维护。因此，我们使用一种新的方法——通配符匹配！

这种方法的语法如下：

```CMake
file(GLOB_RECURSE SOURCES *.cpp *.c)
```

上面这句话的意思是，在根目录下递归查找后缀是`.cpp`和`.c`的文件，并将结果保存在名为`SOURCES`的变量中。注意，这个变量的值是`list`类型的~

于是乎，我们用这个方法将项目所有目录下的源文件都添加进去：

```CMake
file(GLOB_RECURSE SOURCES *.cpp *.c)
add_executable(${PROJECT_NAME} ${SOURCES})
```

这样就完成啦！

## 添加头文件（*.h/*.hpp）

### 基本方法

与添加源文件是一样的，头文件一样可以使用`add_executable`命令：

```CMake
add_executable(${PROJECT_NAME} main.cpp main.h)
```

这样我们就将根目录里的`main.h`添加进去了。

> 需注意，include的时候还是按照路径引用哦！如果需要添加包含目录，用下面的 include_directories就好啦

### 高级方法

方法与上面添加源文件一样

```CMake
file(GLOB_RECURSE HEADERS *.h *.hpp)
add_executable(${PROJECT_NAME} main.cpp ${HEADERS})
```

就不再过多解释了。

### 添加一个目录

大多数情况下，头文件都是放在了一个目录下面的，在这种情况下，我们只用包含这个目录就好：

```CMake
include_directories(DirName)
```

其中，`DirName`是目录相对于根目录的地址。

比如，在项目的根目录下面有一个名为`Inc`的文件夹，其中有一些头文件：

```CMake
include_directories(Inc)
```

如果有子目录的话，用法是一样的：

```CMake
# 包含了 ./include/myapp 的文件夹
include_directories(include/myapp)
```

## 完整版基本配置

现在，大家已经学会了基本的CMake操作了，下面将会给出一个带注释的完整版本：

```CMake
# 设置最低兼容的CMake版本
cmake_minimum_required(VERSION 3.18.2)
# 设置工程名称，储存在PROJECT_NAME变量中
project(hello_cmake)

# 包含了 ./include/myapp 的文件夹
include_directories(include/myapp)

# 查找根目录下的所有源文件
file(GLOB_RECURSE SOURCES *.cpp *.c)
# 添加可执行文件
add_executable(${PROJECT_NAME} ${SOURCES})
```