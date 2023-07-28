---
title: CMake 简单入门
date: 2023-02-09 12:18:11
tags: 
 - CMake
---

## 前言

由于目前许多的C/C++项目都是用的CMake进行构建（e.g. Qt、ROS、STM32），了解并掌握CMake的使用方法已经变得越来越重要啦！

本文可以为大家提供一个CMake的快速入门教程~ 同时，也可作为大家忘记如何使用是的一个reminder！

## 环境配置


咱的推荐配置如下~

```
An operating system: Linux/Windows/macOS/Unix
Development toolkit: GCC/Clang/MSVC
CMake: Latest Version
make: legacy build system
ninja: Recommended to replace the make command. Not necessarily needed.
```

上述环境请大家根据所使用的系统进行安装！

一个配置好的环境参考如下：


```
PS D:\> ninja --version
1.11.1
PS D:\> gcc --version
gcc.exe (tdm64-1) 10.3.0
Copyright (c) 2020 Free Software Foundation, Inc.
本程序是自由软件；请参看源代码的版权声明。本软件没有任何担保；
包括没有适销性和某一专用目的下的适用性担保。
PS D:\> cmake --version
cmake version 3.25.2

CMake suite maintained and supported by Kitware (kitware.com/cmake).
PS D:\> cl
用于 x64 的 Microsoft (R) C/C++ 优化编译器 19.29.30147 版
版权所有(C) Microsoft Corporation。保留所有权利。

用法: cl [ 选项... ] 文件名... [ /link 链接选项... ]
```

## Step 1：构建最小项目

接下来的教程将以命令行为主~

最基本的项目是将一个源代码文件生成可执行文件。对于这么简单的项目，只需要一个三行的 CMakeLists.txt 文件即可，这是本篇教程的起点。在目录中创建一个 CMakeLists.txt 文件，如下所示：

```CMake
# Set the minimum version of CMake that can be used
# To find the cmake version run
# $ cmake --version
cmake_minimum_required(VERSION 3.5)

# Set the project name
project(hello_cmake)#

# add executable files to the project
add_executable(${PROJECT_NAME} main.cpp)
```

在上述文件中，`cmake_minimum_required` 指定使用 CMake 的最低版本号，`project` 指定项目名称，`add_executable` 用来生成可执行文件，需要指定生成可执行文件的名称和相关源文件。`PROJECT_NAME` 是CMake内置的变量，其内容就是咱之前设定的项目名称~

其中，`main.cpp` 文件位于和 `CMakeLists.txt` 同一目录下，内容如下~

```C++
# include <iostream>

int main(int argc, char **argv[]) {
    std::cout << "Hello World!" << std::endl;
    return 0;
}
```

这里咱实现了简单的Hello World程序~

### 构建、编译和运行

现在，咱们就可以构建、编译和运行这个小项目啦！就是先运行 cmake 命令来构建项目，然后使用你选择的编译工具进行编译。

使用如下指令进行构建：

```shell
mkdir build 
cd build
cmake -G Ninja ..
ninja
```
输出如下：

```shell
PS D:\Projects\CMake-tutorials\build> cmake -G Ninja ..
-- The C compiler identification is GNU 10.3.0
-- The CXX compiler identification is GNU 10.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: D:/opt/TDM-GCC-64/bin/gcc.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: D:/opt/TDM-GCC-64/bin/c++.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: D:/Projects/CMake-tutorials/build
PS D:\Projects\CMake-tutorials\build> ninja
[1/2] Building CXX object CMakeFiles/hello_cmake.dir/main.cpp.obj
D:/Projects/CMake-tutorials/main.cpp:3:5: warning: second argument of 'int main(int, char***)' should be 'char **' [-Wmain]
    3 | int main(int argc, char **argv[]) {
      |     ^~~~
[2/2] Linking CXX executable hello_cmake.exe
```

此处，咱们使用了 `ninja` 进行构建，如果你没有安装，建议大家安装一个~

如果使用的是 `make` 进行构建，则运行下面的命令：

Unix/macOS/Linux

```shell
mkdir build 
cd build
cmake -G "Unix Makefiles" ..
make
```

Windows

```shell
mkdir build 
cd build
cmake -G "MinGW Makefiles" ..
mingw32-make
```

或者你使用Visual Studio来编译~

```shell
mkdir build
cd build
cmake -G "Visual Studio 16 2019" ..
msbuild -consoleloggerparameters:Verbosity=minimal ALL_BUILD.vcxproj
```

输出如下：

```shell
PS D:\Projects\CMake-tutorials\build> cmake -G "Visual Studio 16 2019" ..
-- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.22621.
-- The C compiler identification is MSVC 19.29.30147.0
-- The CXX compiler identification is MSVC 19.29.30147.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: D:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: D:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: D:/Projects/CMake-tutorials/build
PS D:\Projects\CMake-tutorials\build> msbuild -consoleloggerparameters:Verbosity=minimal ALL_BUILD.vcxproj
用于 .NET Framework 的 Microsoft (R) 生成引擎版本 16.11.2+f32259642
版权所有(C) Microsoft Corporation。保留所有权利。

  Checking Build System
  Building Custom Rule D:/Projects/CMake-tutorials/CMakeLists.txt
  main.cpp
  hello_cmake.vcxproj -> D:\Projects\CMake-tutorials\build\Debug\hello_cmake.exe
  Building Custom Rule D:/Projects/CMake-tutorials/CMakeLists.txt
  ```

  现在，让我们来了解一下发生了什么~~

  ```
  cmake -G Ninja ..
  ```
  此时在 build 目录下会生成 build.ninja 文件，然后调用编译器来实际编译和链接项目：

  ```
  ninja
  ```

  或者执行

  ```
  cmake --build .
  ```

`--build` 指定编译生成的文件存放目录，其中就包括可执行文件， `. ` 表示存放到当前目录，

在 `build` 目录下生成了一个 `hello_cmake.exe` 可执行文件，试着执行它：

```shell
PS D:\Projects\CMake-tutorials\build> hello_cmake.exe
Hello World!
```

现在，咱的第一个CMake程序就完成啦~

此时，我们的工作目录应该类似:

```
D:.
│  CMakeLists.txt
│  main.cpp
│
└─build
```

### 外部构建与内部构建
这里创建了一个 `build` 目录存放编译产物，可以避免编译产物与代码文件混在一起，这种叫做外部构建。

还有一种内部构建，即直接在项目根目录下进行构建系统与编译，这时构建和编译命令就更改为：

```shell
cmake -G Ninja .
cmake --build .
```
内部构建会使得项目文件很混乱，一般直接用外部构建即可。

## Step 2：优化 CMakeLists.txt 文件

现在，我们已经拥有了一个最小的CMake项目啦~

可是，细心的你或许已经发现：

- 咱有多个源文件怎么办啊？
- 咱用的外部库怎么添加呢？
- 咱想手动指定编译器

要想解决上述问题，我们需要优化 `CMakeLists.txt` 文件！

### set 与 PROJECT_NAME

`set` 指令用于在CMake文件中指定变量，设置好变量后，可以在之后用 `${变量名}` 多次使用它！

比如之前我们设置的 `PROJECT_NAME` 就是一个变量~

e.g. 我们可以用一个变量表示多个源文件：

```
set(SRC_LIST a.cpp b.cpp c.cpp)
add_executable(${PROJECT_NAME} ${SRC_LIST})
```

于是原来的 CMakeLists.txt 文件就可以变成如下所示：

```
cmake_minimum_required(VERSION 3.5)

# set the project name
project(hello_cmake)

SET(SRC_LIST main.cpp)

# add the executable
add_executable(${PROJECT_NAME} ${SRC_LIST})
```

这样看起来就很简洁。但是一个个输入文件的位置还是比较繁琐，之后咱会介绍一个更简洁的方案~

### 添加版本号和配置头文件

有时候我们需要在工程中调用我们定义的版本号，但是一个一个的修改未免显得过于麻烦。我们可以在 CMakeLists.txt 为可执行文件和项目提供一个版本号。

首先，我们来修改 `CMakeLists.txt` 文件，使用 `project` 命令设置项目名称和版本号。

```
cmake_minimum_required(VERSION 3.5)

# Set the project name
project(hello_cmake VERSION 1.0)
```

然后，配置头文件来将版本号传递给源代码：

```
configure_file(TutorialConfig.h.in TutorialConfig.h)
```

由于 TutorialConfig.h 文件会被自动写入 build 目录，因此必须将该目录添加到搜索头文件的路径列表中。将以下行添加到 CMakeLists.txt 文件的末尾：

```
target_include_directories(${PROJECT_NAME} PUBLIC
                           ${PROJECT_BINARY_DIR}
                           )
```

`PROJECT_BINARY_DIR` 表示当前工程的二进制路径，即编译产物会存放到该路径，此时`PROJECT_BINARY_DIR` 就是 `build` 所在路径。

然后创建 `TutorialConfig.h.in` 文件，包含以下内容：

```C++
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @hello_cmake_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @hello_cmake_VERSION_MINOR@
```

其中，`hello_cmake` 是大家配置的项目名称，至于常量名称可以自行修改~

当使用 CMake 构建项目后，会在 build 中生成一个 TutorialConfig.h 文件，内容如下：

```
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR 1
#define Tutorial_VERSION_MINOR 0
```

下一步在 `main.cpp` 包含头文件 `TutorialConfig.h`，最后通过以下代码打印出可执行文件的名称和版本号。

```C++
if (argc < 2) {
      // report version
      std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "."
                << Tutorial_VERSION_MINOR << std::endl;
      std::cout << "Usage: " << argv[0] << " number" << std::endl;
      return 1;
    }
```

输出类似如下：

```
PS D:\Projects\CMake-tutorials\build> ."D:/Projects/CMake-tutorials/build/hello_cmake.exe"
Hello World!
0x218c3217ba0 Version 1.0
Usage: 0x218c3217ba0 number
```

### 指定 C++ 标准

不同版本的编译器，默认的C++标准是不同哒~ 所以我们需要手动设定版本来保证兼容性！

比如咱想使用C++11 的 `auto` 关键字和 `for` 循环。

```
#include <iostream>
#include <vector>
int main(void)
{
    std::vector<int> arr = { 1, 2, 3 };
    // ...
    for(auto n : arr)  //使用基于范围的for循环
    {
        std::cout << n << std::endl;
    }
    return 0;
}
```

在 CMake 中支持特定 C++标准的最简单方法是使用 `CMAKE_CXX_STANDARD` 标准变量。在 `CMakeLists.txt` 中设置 `CMAKE_CXX_STANDARD` 为11，`CMAKE_CXX_STANDARD_REQUIRED` 设置为`True`。确保在 `add_executable` 命令之前添加 `CMAKE_CXX_STANDARD_REQUIRED` 命令。

```
cmake_minimum_required(VERSION 3.5)

# Set the project name
project(hello_cmake VERSION 1.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
```

需要注意的是，如果你的gcc编译器版本够高，也可以不用指定 C++ 版本为 11。从 GCC 6.1 开始，当不指定任何版本 C++ 标准时，默认版本是 C++ 14，如果你想用 C++17 的语言，还是需要指定的。

修改完成后，需要对代码进行重新编译 cmake --build .，此时可以不用进行项目构建。


## Step 3：添加库

现在我们将向项目中添加一个库，这个库包含计算数字平方根的实现，可执行文件使用这个库，而不是编译器提供的标准平方根函数。

我们把库放在名为 MathFunctions 的子目录中。此目录包含头文件 MathFunctions.h 和源文件 mysqrt.cpp。源文件有一个名为 mysqrt 的函数，它提供了与编译器的 sqrt 函数类似的功能，MathFunctions.h 则是该函数的声明。

创建 `MathFunctions.h`:

```
double mysqrt(double);
```

创建 `mysqrt.cpp`:

```
#include "MathFunctions.h"

double mysqrt(double num) {
    double i = 0;
    double small;
    while (i * i < num)
    i += 0.01;
    small = i - 1;
    return  (i*i - num > num - small * small ? small : i);
}
```

在 MathFunctions 目录下创建一个 CMakeLists.txt 文件，并添加以下一行：

```
# MathFunctions/CMakeLists.txt
add_library(MathFunctions mysqrt.cpp)
```

表示添加一个叫 MathFunctions 的库文件。

修改一下之前的 `main.cpp` 来使用我们新建的库

```C++
# include "TutorialConfig.h"
# include <cmath>
# include <iostream>
# include <string>
# include "MathFunctions.h"

int main(int argc, char* argv[]) {
    if (argc < 2) {
        std::cout << "Usage: " << argv[0] << " number" << std::endl;
        return 1;
    }

    // convert input to double
    const double inputValue = std::stod(argv[1]);

    // calculate square root
    const double outputValue = mysqrt(inputValue);
    std::cout << "The square root of " << inputValue
              << " is " << outputValue
              << std::endl;
    return 0;
}
```

修改 根目录下载的 `CMakeLists.txt` ，添加如下内容：

```
# add the MathFunctions library
add_subdirectory(MathFunctions)

# add the executable
add_executable(${PROJECT_NAME} tutorial.cpp)

target_link_libraries(${PROJECT_NAME} PUBLIC MathFunctions)

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(${PROJECT_NAME} PUBLIC
                           ${PROJECT_BINARY_DIR}
                           ${PROJECT_SOURCE_DIR}/MathFunctions
                           )
```

此时，项目应该可以正常编译使用啦~

```
PS D:\Projects\CMake-tutorials\build> ."D:/Projects/CMake-tutorials/build/hello_cmake.exe" 123
The square root of 123 is 11.1
```

## Step 4：将库设置为可选项
现在将 MathFunctions 库设为可选的，虽然对于本教程来说，没有必要这样做，但对于较大的项目来说，这种情况很常见。因为不是所有的计算机都会安装项目推荐的所有依赖项~

第一步是向顶级 CMakeLists.txt 文件添加一个选项。

```
option(USE_MYMATH "Use tutorial provided math implementation" ON)
```

`option` 表示提供用户可以选择的选项。命令格式为：`option(<variable> "description [initial value])` 。

`USE_MYMATH` 的默认值 ON，用户可以更改这个值。此设置将存储在缓存中，所以不需要在每次构建项目时设置该值。

修改咱的 `CMakeLists.txt` 如下：

```
# Set the minimum version of CMake that can be used
# To find the cmake version run
# $ cmake --version
cmake_minimum_required(VERSION 3.5)

# Set the project name
project(hello_cmake VERSION 1.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

option(USE_MYMATH "Use tutorial provided math implementation" ON)

if(USE_MYMATH)
  add_subdirectory(MathFunctions)
  list(APPEND EXTRA_LIBS MathFunctions)
  list(APPEND EXTRA_INCLUDES ${PROJECT_SOURCE_DIR}/MathFunctions)
endif()

configure_file(TutorialConfig.h.in TutorialConfig.h)

# add executable files to the project
add_executable(${PROJECT_NAME} main.cpp)
target_include_directories(${PROJECT_NAME} PUBLIC
                           ${PROJECT_BINARY_DIR}
                           ${EXTRA_INCLUDES}
                           )

target_link_libraries(${PROJECT_NAME} PUBLIC ${EXTRA_LIBS})
```

在 `if` 块中，有 `add_subdirectory` 命令和 `list` 命令，`APPEND` 表示将元素 `MathFunctions` 追加到列表`EXTRA_LIBS` 中，将元素 `${PROJECT_SOURCE_DIR}/MathFunctions` 追加到列表 `EXTRA_INCLUDES` 中。`EXTRA_LIBS` 存储动态/静态链接库，`EXTRA_INCLUDES` 存储头文件。

接下来对源代码的进行修改。首先，在 `main.cpp` 中包含 MathFunctions.h 头文件：

```
#ifdef USE_MYMATH
    #include "MathFunctions.h"
#endif
```

然后，还在 `main.cpp` 中，使用 `USE_MYMATH` 选择使用哪个平方根函数：

```
#ifdef USE_MYMATH
  const double outputValue = mysqrt(inputValue);
#else
  const double outputValue = sqrt(inputValue);
#endif
```

因为源代码使用了 `USE_MYMATH` 宏，可以用下面的行添加到 `TutorialConfig.h.in` 文档中：

```
// TutorialConfig.h.in
#cmakedefine USE_MYMATH
```

我们再使用 `cmake` 命令构建项目，并运行生成的可执行文件。

```
PS > cmake -G Ninja ..
-- The C compiler identification is GNU 10.3.0
-- The CXX compiler identification is GNU 10.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: D:/opt/TDM-GCC-64/bin/gcc.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: D:/opt/TDM-GCC-64/bin/c++.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: D:/Projects/CMake-tutorials/build
PS > cmake --build . 
[4/4] Linking CXX executable hello_cmake.exe
PS > ./hello_cmake.exe 8
The square root of 8 is 2.8285
```

此时默认调用 `mysqrt` 函数，也可以在构建项目时指定 `USE_MYMATH` 的值为 `OFF`：

```
> cmake -DUSE_MYMATH=OFF ..
> cmake --build .
```
此时会调用自带的 `sqrt` 函数。

## Step 5：添加库的使用要求

使用要求会对库或可执行程序的链接、头文件包含命令行提供了更好的控制，也使 CMake 中目标的传递目标属性更加可控。利用使用要求的主要命令是：

- `target_compile_definitions()`
- `target_compile_options()`
- `target_include_directories()`
- `target_link_libraries()`

现在重构一下 Step4 中的代码，使用更加现代的 CMake 方法来包含 `MathFunctions` 库的头文件。

首先声明，链接 `MathFunctions` 库的任何可执行文件/库文件都需要包含 `MathFunctions` 目录作为头文件路径，而 `MathFunctions` 本身不需要包含，这被称为 `INTERFACE` 使用要求。

`INTERFACE` 是指使用者需要、但开发者不需要的那些东西。在 `MathFunctions/CMakeLists.txt` 最后添加：

```
# MathFunctions/CMakeLists.txt
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )
```

`CMAKE_CURRENT_SOURCE_DIR` 表示 `MathFunctions` 库所在目录。

现在我们已经为 `MathFunctions` 指定了使用要求 `INTERFACE` ，那么可以从顶级 `CMakeLists.txt` 中删除`EXTRA_INCLUDES` 变量的相关使用：

```
if(USE_MYMATH)
  add_subdirectory(MathFunctions)
  list(APPEND EXTRA_LIBS MathFunctions)
  list(APPEND EXTRA_INCLUDES ${PROJECT_SOURCE_DIR}/MathFunctions)   # 删除此行
endif()

...

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(${PROJECT_NAME} PUBLIC
                           ${PROJECT_BINARY_DIR}
                           ${EXTRA_INCLUDES}   # 删除此行
                           )
```

现在只要是链接了 `MathFunctions` 库，就会自动包含 `MathFunctions` 所在目录的头文件，简洁而优雅。

这里补充两个知识点：

1、使用要求除了 `INTERFACE`，还有 `PRIVATE` 和 `PUBLIC`。`INTERFACE`表示使用者需要开发者不需要，PRIVATE表示使用者不需要开发者需要，PUBLIC 表示使用者和开发者都需要。

2、这里使用 `add_library` 命令生成的 `MathFunctions` 库其实是 __静态链接库__ 。动态库和静态库的区别是：静态库在 __链接阶段__ 会被链接到最终目标中（比如可执行程序），缺点是同一个静态库如果被不同的程序引用，那么内存中会存在这个静态库函数的多份拷贝。动态库在链接阶段不会被拷贝最终目标中，程序在 __运行阶段__ 才会加载这个动态库。所以多个程序就算引用了同一个动态库，内存中也只存在一份动态库函数的拷贝。

## Step 6：build 目录介绍

在文本中，我都是创建了一个 build 用来存放 cmake 构建和编译的产物，这里简单说下里面有些什么东西。

```
build/
    CMakeCache.txt
    CMakeFiles/
    cmake_install.cmake
    build.ninja
    hello_cmake.exe
    TutorialConfig.h
    MathFunctions/
```

其中 `build.ninja` 是 `CMake` 根据顶级 `CMakeLists.txt` 生成的构建文件，通过该文件可以对整个项目进行编译。

`hello_cmake.exe` 就是生成的可执行文件，通过该文件运行程序。

`TutorialConfig.h` 是用于配置信息的头文件，是 `CMake` 根据 `TutorialConfig.h.in` 文件自动生成的。

还有个 `MathFunctions` 文件夹：

```
MathFunctions/
    CMakeFiles/
    cmake_install.cmake
    libMathFunctions.a
```

`libMathFunctions.a` 是 `MathFunctions` 静态链接库，可执行文件会通过这个库调用 `mysqrt` 函数。

P.S. 用过 Linux 的家人们是不是觉得很熟悉呀~

## Step 7：生成动态链接库

动态链接库，在Windows下扩展名为 `.dll`；Linux下为 `.so`，macOS下为 `.dylib`。

### CMake 生成动态库简单实例

修改 `MathFunctions/CMakeLists.txt`：

```
# MathFunctions/CMakeLists.txt
add_library(MathFunctions SHARED mysqrt.cpp)

target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )
```

`SHARED` 表明生成动态链接库。

编译工程，得到的输出如下：

```
[main] Building folder: CMake-tutorials 
[build] Starting build
[proc] Executing command: "D:\Program Files\CMake\bin\cmake.EXE" --build d:/Projects/CMake-tutorials/build --config Debug --target all --
[build] [2/4  25% :: 0.083] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cpp.obj
[build] [3/4  50% :: 0.239] Linking CXX shared library MathFunctions\libMathFunctions.dll
[build] [3/4  75% :: 0.445] Building CXX object CMakeFiles/hello_cmake.dir/main.cpp.obj
[build] [4/4 100% :: 1.045] Linking CXX executable hello_cmake.exe
[build] Build finished with exit code 0
```

此时，我们发现，生成了 `libMathFunctions.dll` 这个动态链接库~~

需要注意的是，生成的动态链接库必须在程序所在的目录或者是系统搜索目录中，否则程序将无法运行！

### CMake 同时构建静态库与动态库

有上面的例子可以看出，使用 ADD_LIBRARY 指令就可以同时构建静态和动态库：

```
ADD_LIBRARY(MathFunctions SHARED mysqrt.cpp)
ADD_LIBRARY(MathFunctions STATIC mysqrt.cpp)
```

但是如果使用这种方式，只会构建一个动态库，不会构建出静态库，虽然静态库的后缀是 `.a`，此时我们可以修改静态库的名字，这样是可以同时构建动态库和静态库：

```
ADD_LIBRARY(MathFunctions SHARED mysqrt.cpp)
ADD_LIBRARY(MathFunctions_static STATIC mysqrt.cpp)
```

但是我们往往 __希望他们的名字是相同的，只是后缀不同__ 而已，此时可以使用 `SET_TARGET_PROPERTIES` 指令（该指令详细可见下文 CMake 语法），修改`CMakeLists.txt`文件：

```
# MathFunctions/CMakeLists.txt
add_library(MathFunctions_static STATIC mysqrt.cpp)

# 对MathFunctions_static的重名为MathFunctions
SET_TARGET_PROPERTIES(MathFunctions_static PROPERTIES  OUTPUT_NAME "MathFunctions")
# cmake 在构建一个新的target 时，会尝试清理掉其他使用这个名字的库，如果没有清理还是会只会构建一个动态库，不会构建出静态库
SET_TARGET_PROPERTIES(MathFunctions_static PROPERTIES CLEAN_DIRECT_OUTPUT 1)

add_library(MathFunctions SHARED mysqrt.cpp)

target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )
```

此时再来编译：

```
[main] Building folder: CMake-tutorials 
[build] Starting build
[proc] Executing command: "D:\Program Files\CMake\bin\cmake.EXE" --build d:/Projects/CMake-tutorials/build --config Debug --target all --
[build] [3/6  16% :: 0.101] Building CXX object MathFunctions/CMakeFiles/MathFunctions_static.dir/mysqrt.cpp.obj
[build] [4/6  33% :: 0.120] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cpp.obj
[build] [5/6  50% :: 0.245] Linking CXX static library MathFunctions\libMathFunctions.a
[build] [5/6  66% :: 0.310] Linking CXX shared library MathFunctions\libMathFunctions.dll
[build] [5/6  83% :: 0.554] Building CXX object CMakeFiles/hello_cmake.dir/main.cpp.obj
[build] [6/6 100% :: 1.144] Linking CXX executable hello_cmake.exe
[build] Build finished with exit code 0
```

我们就会发现同时生成了静态库和动态库了~~

#### 修改动态库版本号

同时我们还可以修改动态库的版本号

```
// Linux/macOS 一般动态库都有一个版本号的关联
libhello.so.1.2
libhello.so ->libhello.so.1
libhello.so.1->libhello.so.1.2
```

修改 `CMakeLists.txt` ，重新构建看看结果：

```
$ cmake ..
-- The C compiler identification is GNU 11.3.0
-- The CXX compiler identification is GNU 11.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /cygdrive/d/Projects/CMake-tutorials/build
$ make
[ 16%] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cpp.o
[ 33%] Linking CXX shared library cygMathFunctions-1.dll
[ 33%] Built target MathFunctions
[ 50%] Building CXX object CMakeFiles/hello_cmake.dir/main.cpp.o
[ 66%] Linking CXX executable hello_cmake.exe
[ 66%] Built target hello_cmake
[ 83%] Building CXX object MathFunctions/CMakeFiles/MathFunctions_static.dir/mysqrt.cpp.o
[100%] Linking CXX static library libMathFunctions.a
[100%] Built target MathFunctions_static
$ ls
CMakeFiles  Makefile  cmake_install.cmake  cygMathFunctions-1.dll  libMathFunctions.a  libMathFunctions.dll.a
```

以上代码在Cygwin环境中编译。可以发现生成的库文件成功的带上了版本号~

### 安装共享库和头文件

本例中我们将 `MathFunctions` 的共享库安装到 `<prefix>/lib` 目录；将 `MathFunctions.h` 安装到 `<prefix>/include/MathFunctions` 目录，这样共享库才能够被调用。

修改 `CMakeLists.txt` 文件：

```
...

# 文件放到该目录下
INSTALL(FILES MathFunctions.h DESTINATION include/MathFunctions)

# 二进制，静态库，动态库安装都用TARGETS
# ARCHIVE 特指静态库，LIBRARY 特指动态库，RUNTIME 特指可执行目标二进制。
INSTALL(TARGETS MathFunctions MathFunctions_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib RUNTIME DESTINATION lib)
```

此时，编译安装输出如下（Cygwin环境）：

```
$ cmake -DCMAKE_INSTALL_PREFIX=/usr ..
-- Configuring done
-- Generating done
-- Build files have been written to: /cygdrive/d/Projects/CMake-tutorials/build
$ cmake --build .
Consolidate compiler generated dependencies of target MathFunctions
[ 16%] Linking CXX shared library cygMathFunctions.dll
[ 33%] Built target MathFunctions
Consolidate compiler generated dependencies of target hello_cmake
[ 50%] Linking CXX executable hello_cmake.exe
[ 66%] Built target hello_cmake
Consolidate compiler generated dependencies of target MathFunctions_static
[100%] Built target MathFunctions_static
$ cmake --install .
-- Install configuration: ""
-- Installing: /usr/include/MathFunctions/MathFunctions.h
-- Installing: /usr/lib/libMathFunctions.dll.a
-- Installing: /usr/lib/cygMathFunctions.dll
-- Installing: /usr/lib/libMathFunctions.a
```
测试一下运行情况：

```
$ export PATH=$PATH:/usr/lib
$ ./hello_cmake.exe 123
The square root of 123 is 11.0906
```

成功啦~

### 使用外部动态库和头文件

`CMakeLists.txt` 配置如下：

```
INCLUDE_DIRECTORIES(/usr/include/MathFunctions)
ADD_EXECUTABLE(${PROJECT_NAME} main.cpp) 
TARGET_LINK_LIBRARIES(hello cygMathFunctions.dll)
```

### 使用外部静态库

配置如下：

```
INCLUDE_DIRECTORIES(/usr/include/MathFunctions)
ADD_EXECUTABLE(${PROJECT_NAME} main.cpp) 
TARGET_LINK_LIBRARIES(main libMathFunctions.a)
```

## Step 7：CMake常用的命令或函数：

-   定义项目：  
    
    `project(myProject C CXX)`：该命令会影响 `PROJECT_SOURCE_DIR`、`PROJECT_BINARY_DIR`、`PROJECT_NAME`等变量。另外要注意的是，对于多个project嵌套的情况，`CMAKE_PROJECT_NAME`是当前CMakeLists.txt文件回溯至最顶层CMakeLists.txt文件中所在位置之前所定义的最后一个project的名字。  
    
    `cmake_minimum_required(VERSION 3.0)`：出进行编译所需要的CMake最低版本，如果不指定的话系统会自己指定一个，但是也会扔出一个`warning`。
    
-   搜索源文件：  
    
    `file(<GLOB|GLOB_RECURSE> <variable> <pattern>)`：按照正则表达式搜索路径下的文件，比如：`file(GLOB SRC_LIST "./src/*.cpp")`。  
    
    `aux_source_directory(<dir> <variable>)`：搜索文件内所有的源文件。
    
-   添加编译目标：  
    
    `add_library(mylib [STATIC|SHARED] ${SRC_LIST})`  
   
    `add_executable(myexe ${SRC_LIST})`
    
-   添加头文件目录：  
    
    `include_directories(<items>)`：为该位置之后的target链接头文件目录（不推荐）。  
    
    `target_include_directories(<target> <PUBLIC|INTERFACE|PRIVATE]> <items>)`：为特定的目标链接头文件目录。
    
-   添加依赖库：  
    
    `link_libraries(<items>)`：为该位置之后的target链接依赖库。  
    
    `target_link_libraries(<target> <items>)`：为特定的目标链接依赖库。  
    这里，常见的依赖库可能是以下几种情况：
    
        1.  在此次编译的工程里添加的目标，给出目标名；
        2.  外部库，给出路径和库文件全名；
        3.  外部库，通过`find_package()`等命令搜索到的。
    
    对于`find_package(XXX)`，该命令本身并不直接去进行搜索，而是通过特定路径下的FindXXX.cmake或XXXConfig.cmake文件来定位头文件和库文件的位置，分别被称为Module模式和Config模式。该命令会定义一个`XXX_FOUND`变量，如果成功找到，该变量为真，同时会定义`XXX_INCLUDE_DIR`和`XXX_LIBRARIES`两个变量，用于link和include。  
      
    `add_dependencies`(<target> \[<target-dependency>\]...)：
    
    使顶层 `<target>` 依赖于其他顶层目标，以确保它们在 `<target>` 之前构建。顶级目标是由 [add\_executable()](https://runebook.dev/zh-CN/docs/cmake/command/add_executable#command:add_executable " add_executable() ") ， [add\_library()](https://runebook.dev/zh-CN/docs/cmake/command/add_library#command:add_library " add_library() ") 或 [add\_custom\_target()](https://runebook.dev/zh-CN/docs/cmake/command/add_custom_target#command:add_custom_target " add_custom_target() ") 命令之一创建的目标（但不是由CMake生成的目标，如 `install` ）。
    
-   添加子目录：  
    
    `add_subdirectories(<dir>)`：子目录中要有CMakeLists.txt文件，否则会报错。
    
-   包含其他cmake文件：  
    
    `include(./path/to/tool.cmake)`  
    
    或`set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ./path/to)`，随后`include(tool)`。  
    该命令相当于将tool.cmake的内容直接包含进来。
    
-   定义变量：  
   
    `set(<variable> <value>... [PARENT_SCOPE])`  
    
    `set(<variable> <value>... CACHE <type> <docstring> [FORCE])`  
    
    其中`CACHE`会将变量定义在缓存文件`CMakeCache.txt`里，可以在下次编译的时候读取。
    
-   作用域：  
    
    `add_subdirectories(<dir>)`会创建一个子作用域，里面可以使用父作用域里定义的变量，但里面定义的变量在父作用域不可见，同样，在子作用域修改父作用域里的变量不会影响父作用域。`function()`同样会产生一个子作用域。若想让子作用域里的定义或者修改在父作用域可见，需要使用`PARENT_SCOPE`标记。  
    
    相对地，`macro()`和`include()`不会产生子作用域。
    
-   选项：  
    
    `add_option(MY_OPTION <ON|OFF>)`：会定义一个选项。在使用`cmake`命令时，可以通过`-D`改变选项的值。比如`cmake .. -DMY_OPTION=ON`。
    
-   编译选项：  
    
    `add_compile_options(-std=c++11)`  
    
    如果想要指定具体的编译器的选项，可以使用`make_cxx_flags()`或`cmake_c_flags()`。
    
-   与源文件的交互：  
   
    `configure_file(XXX.in XXX.XX)`会读入一个文件，处理后输入到新的位置。一方面，会替换掉`#XXX`或者`@XXX@`定义的内容。另一方面，会将文件里的`#cmakedefine VAR …`替换为`#define VAR …`或者`/* #undef VAR */`。
    
-   字符串操作、循环、判断、文件/变量存在判断等  
    
    这些命令同样有用，请参考网络资料。

参考文献：

[1] https://zhuanlan.zhihu.com/p/500002865

[2] https://blog.csdn.net/weixin_45004203/article/details/125256367

[3] https://blog.csdn.net/LearnLHC/article/details/125515650


