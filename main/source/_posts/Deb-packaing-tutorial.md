---
title: Deb 打包——从入门到入魂（一）
date: 2024-09-15 22:18:57
tags:
  - Debian
  - Deb
---

# Debian 软件包的简单介绍

“Debian“软件包”，或称作 Debian 档案文件，包含了与特定的程序套件或一组关联的程序有关的可执行文件、库和文档。正常情况下，Debian 档案文件的文件名以 .deb 结尾。” ——《第 7 章 Debian 软件包管理系统基础》

简单来讲，Debian 软件包就是一个软件的压缩包，只不过里面附带了很多其他的信息（如版本、依赖等等）来让系统能方便地处理他。

## Debian 软件包的类型

Debian 软件包有两种类型：

 - 二进制包，包含了可执行文件、配置文件、man/info 页面、版权信息，以及其他文档。这些软件包使用一种 Debian 特有的存储格式进行分发（参见第 7.2 节 “Debian 二进制软件包的格式是什么？”）；它们的扩展名通常是“.deb”。二进制包可以使用 Debian 的 `dpkg` 工具进行解包（也许会通过 `aptitude` 等前端进行调用）；更多细节请阅读手册页。

 - 源码包，包含了一个 `.dsc` 文件描述该源码包（包括下述文件的文件名），一个 `.orig.tar.gz` 文件，使用经过 gzip 压缩的 tar 档案保存未经修改的源代码，通常还包括一个 `.diff.gz` 文件，保存了 Debian 对源代码的修改。`dpkg-source` 工具可以打包和解包 Debian 源码包。

 通常我们安装的都是二进制包。在Debian系统中，二进制包通常都是用源码包构建出来的。一个源码包可以构建出多个二进制包。

 ## Debian 软件包的结构

Deb 包是一个使用 `ar` 压缩的压缩包。里面主要包含了 `control.tar.gz`和`data.tar`。`control.tar.gz` 包含了软件包的元数据，如软件包的名称、版本、依赖等等。这些信息使得软件包管理器能够识别并正确安装软件。`data.tar` 就是软件包的内容啦！包管理器会把里面的文件解压到系统的相应位置。

## Debian 解压后的文件结构

当你手动解压（没错！就是好玩）一个deb包后，目录结构是这样的：
```
.
├── DEBIAN
│   ├── control
│   └── postinst
├── usr
│   ├── bin
│   │   └── hello
│   └── share
│       └── man
│           └── man1
│               └── hello.1.gz
└── etc
    └── hello.conf
```

大家很快就发现，这不就是把软件解压了嘛！只不过，有个特殊的文件夹——`DEBIAN`。这个文件夹内存放的是一些特殊的文件，如`control`、`postinst`等等。这些文件会在软件包安装时被用到。下面会对这些文件进行介绍。

### `control` 文件

简短地说，Debian 软件包 hello 的控制文件样例如下：

```text
Package: hello
Version: 2.9-2+deb8u1
Architecture: amd64
Maintainer: Santiago Vila <sanvila@debian.org>
Installed-Size: 145
Depends: libc6 (>= 2.14)
Conflicts: hello-traditional
Breaks: hello-debhelper (<< 2.9)
Replaces: hello-debhelper (<< 2.9), hello-traditional
Section: devel
Priority: optional
Homepage: https://www.gnu.org/software/hello/
Description: example package based on GNU hello
 ```

 `Package` 字段给出了软件包的名称。这个名字可以传递给软件包管理工具，以操作该软件包，且它通常与 Debian 档案文件的文件名的第一部分相似，但不一定完全相同。

`Version` 字段给出了上游开发者的版本号以及（在最后一部分）Debian 软件包的修订级别。

`Architecture` 字段指明了该二进制软件包编译时针对的处理器。

`Depends` 字段给出了安装该软件包前必须安装的软件包列表。

`Installed-Size`指出了该软件包安装后会消耗多少磁盘空间。设置该字段的目的是给软件包安装前端使用，以判断是否有足够的磁盘空间安装该程序。

`Section` 行给出了该 Debian 软件包在 Debian 档案站点所存储的“分区”。

`Priority` 指出了该软件包在安装时有多重要，这使得半智能的软件，如 apt 或 aptitude，能够将软件包分为各种类别，例如，可选择安装的软件包。参见第 7.7 节 “什么是必备、必需、重要、标准、可选和额外软件包？”。

`Maintainer` 字段给出了当前负责维护该软件包的人的电子邮件地址。

`Description` 字段给出了该软件包功能的简短介绍。

### `preinst`、`postinst`、`prerm`，和 `postrm` 脚本

这些文件是在软件包安装或卸载前后自动执行的可执行脚本。这些文件和名为 `control` 的文件都属于 Debian 档案文件的“控制”部分。

`preinst`
 - 该脚本在所属的软件包从它的 Debian 档案文件（“.deb”）中被解压之前执行。许多“preinst”脚本会停止和正在升级的软件包有关的服务，直至安装或升级完成（即在“postinst”脚本成功执行之后）。

`postinst`
 - 该脚本在 foo 从它的 Debian 档案文件（“.deb”）中被解压之后执行，通常用于完成软件包 foo 所必需的配置。“postinst”脚本常常请求用户输入，且/或在用户接受默认值的时候警告他们，应记得在需要时回来重新配置软件包。许多“postinst”脚本会在新软件包的安装或升级完成之后执行必要的命令，以启动或重新启动服务。

`prerm`
 - 该脚本通常停止与软件包有关联的服务。它在删除与软件包相关的文件之前执行。

`postrm`
 - 该脚本通常修改与 foo 相关的链接或其他文件，且/或删除该软件包创建的文件。


看了这么多，相信大家也了解了基本的deb包格式：一个基本的deb包只需要一个control文件和需要被打包的目录就好！下面就来为大家介绍这第一种原始的打包方式。

# 方法一：手动打包

## Step1：建立目录结构

我们的程序是要放到一个系统中的各个部分去的。

其实这个并没有那么严格，不一定全都要挤到/usr/lib里边去，放到/home等都没有太大的问题——看你需求。

比如如下的结构：

```shell
├── debpack
│   ├── DEBIAN（这个目录要添加control文件（无后缀名），可选添加postinst等）
│   └── usr
│       ├── lib
│       │   └── debpacktest（预定要安装到系统中的文件）
│       │       ├── mainform.py
│       │       └── main.py
│       └── share
│           ├── applications（在这里添加xxx.desktop，使启动器中能够加载出你的应用，没有就不需要）
│           └── icons（你的图标，没有就不需要）

```

上述结构会将 main.py 安装到 `/usr/lib/debpacktest` 目录下。

大家可能也发现了：其实就是把目录底下的文件结构原封不动地搬到系统根目录下了！

## Step2：添加control文件

进入到到DEBIAN，在命令行输入（当然，你用gedit也行）

```shell
$ nano control
```

输入以下内容：

```text 
Package: hello # 软件包的报名
Version: 2.9-2+deb8u1 # 软件包的版本
Architecture: amd64 # 软件包运行的架构
Maintainer: Santiago Vila <sanvila@debian.org> # 维护者姓名邮箱
Installed-Size: 145 # 安装后的大小
Depends: libc6 (>= 2.14) # 软件包的依赖，使用英文逗号分隔，没有无需此行
Conflicts: hello-traditional # 和哪些软件包冲突，使用英文逗号分隔，没有无需此行
Breaks: hello-debhelper (<< 2.9) # 列出这个包会破坏哪些包，包管理会尝试修复被破坏的包。
Replaces: hello-debhelper (<< 2.9), hello-traditional # 指出此包替代了哪些包
Section: devel # 软件包的分区
Priority: optional # 软件包的优先级，一般都用 optional
Homepage: https://www.gnu.org/software/hello/ # 软件包的官网
Description: example package based on GNU hello # 软件包的简介
```

记得不要把注释写进去了~！按照实际情况进行修改。

## Step3：打包

到这里，一个基本的deb软件包就大功告成了。

我们返回到 `debpack` 的上级目录。执行如下命令：

```shell
fakeroot dpkg -b debpack hello_2.9-2+deb8u1_amd64.deb
```

`fakeroot` 用于模拟root的文件所有权，保证安装文件的权限。如果提示没有，可以用 `sudo apt install fakeroot` 进行安装。

## Step4：查看打包结果

使用 `dpkg -c hello_2.9-2+deb8u1_amd64.deb` 可以查看deb包中的内容。

可以看到，这个方法十分复杂。通常我们的程序比较庞大，会拥有许多依赖。此时再像上面这样手动打包就显得十分不便。在后续的文章中将为大家介绍更方便的打包方式。
