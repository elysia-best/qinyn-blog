---
title: 在 Debian 上安装数位板驱动
date: 2024-12-23 16:51:40
tags:
  - Debian
  - Drawing
  - Driver
---

**注意** Wacom不适用本教程，请参考[官方文档](https://linuxwacom.github.io/)。

众所周知在Linux下基本少没有高漫，绘王等数位板驱动的踪迹，偶然发现了一个名为`DIGImend`的项目，他为部分常见的数位板提供了驱动支持，本文将介绍如何在 Debian 上安装 `DIGImend` 驱动。

## 支持的型号

`DIGImend` 支持多种型号的数位板，具体的支持情况可以参考[官方文档](https://digimend.github.io/drivers/digimend/tablets/) 和 [这个文件的内容](https://github.com/DIGImend/digimend-kernel-drivers/blob/master/xorg.conf)。

**注意** 如果没有发现你手中的板子的型号也不要着急，部分板子用的驱动其实是同一个，只要设备的USB ID匹配就行，接下来我们来查看设备的USB ID到底是什么吧~

## 查看设备ID

目前主线的Linux内核都可以识别出我的高漫SN540了。

大家可 **在终端中** 以用如下的方式查看手中设备的USB ID：

a) 输入 `lsusb` 并回车，你可能会看到有类似下面这一行

```bash
Bus 002 Device 013: ID 256c:0064 GAOMON Gaomon Tablet
```

这就代表你的设备ID是 `256c:0064`，其中 `256c` 是厂商ID，`0064` 是产品ID。 刚好，这个ID在项目配置文件的支持范围内，所以我们就可以高兴地继续配置啦~

b)  检查是否能正常获取设备数据。在终端中执行 `sudo usbhid-dump -es -m 256c:0064`，把`256c:0064` 替换为你的设备ID，拿笔在板子上随便画几笔，如果终端中出现了滚动的输出，那就说明设备可以被内核识别啦！

## 安装驱动

这是本文的重点部分。`DIGImend` 的驱动有两种安装方式，一种是从仓库的源码编译，另一种是直接从仓库下载预编译好的deb包。本文会介绍两种安装方式。

### 从源码编译

#### 安装依赖

a) 安装内核头文件

```bash
sudo apt-get install -y "linux-headers-$(uname -r)"
```

b) 安装DKMS

```bash
sudo apt-get install -y dkms
```

#### 克隆仓库

```bash
git clone https://github.com/DIGImend/digimend-kernel-drivers.git
```

#### 进入仓库目录编译安装

```bash
cd digimend-kernel-drivers
sudo make dkms_install
```
如此就会把驱动安装到内核中了，由于dkms的特性，在你更换内核版本的时候，dkms会自动帮你编译安装新内核的驱动。

### 从仓库下载deb包安装

同样需要大家安装上述的依赖包，不过接下来的步骤就简单多了。

从项目的 [Release 页面](https://github.com/DIGImend/digimend-kernel-drivers/releases) 下载最新的deb包，并使用 `apt` 安装。

```bash
curl https://github.com/DIGImend/digimend-kernel-drivers/releases/download/v13/digimend-dkms_13_all.deb -o digimend-dkms_13_all.deb

sudo apt-get install ./digimend-dkms_13_all.deb
```

等待deb包完成安装即可。

## 配置驱动

安装完驱动后，我们还需要配置驱动才能正常使用数位板。

#### 安装 `Xserver-xorg-input-wacom`

```bash
sudo apt-get install -y Xserver-xorg-input-wacom
```
#### 安装 `kde-config-tablet`

如果你用的是 KDE 桌面环境，还需要安装 `kde-config-tablet`。

```bash
sudo apt-get install -y kde-config-tablet
```

#### 修改 `xorg.conf`

如果在安装驱动部分你使用的是deb包安装方式，那么驱动会自动帮你生成 `xorg.conf` 文件，你只需要修改一下 `/usr/share/X11/xorg.conf.d/50-digimend.conf` 里面的内容即可。在`Identifier "Huion tablets with Wacom driver"`这一节下方添加你的设备ID，比如 `MatchUSBID "5543:006e|256c:006e|256c:006d|256c:0064|256c:006f"`。当然这里已经有了我的高漫SN540的设备ID，所以就不用再添加啦~。


如果你用的是源码编译安装方式，那么你需要手动创建 `xorg.conf` 文件。

在终端中输入 `sudo nano /etc/X11/50-digimend.conf` 并回车，在文件末尾添加如下内容（注意使用tab）：

```bash
Section "InputClass"
	Identifier "Tablet"
	MatchUSBID "256c:0064"
	MatchDevicePath "/dev/input/event*"
	Driver "wacom"
EndSection
```

将`256c:0064` 替换为你的设备ID，保存并退出。


## 数位板校准

**注意** 我的电脑有两块屏幕，在安装驱动后数位板区域被映射到了二块屏幕上（可能是因由于X11显示的特性），所以需要手动关闭一个显示器才行。

打开KDE系统设置，你会看到一个数位板设置，点开它就能执行关于数位板的设置了。

类似下图：

<div align=center><img src="image.png"></div>

## 调整数位板映射方向

**以下内容我没有测试过，请自行测试**，选自@duter2016的博客

以上安装完驱动后，你在Xournal++中使用数位板时，你会发现数位板中书写文字，文字是扁的，比较难看，原因是M5在Linux上的默认映射是竖屏的，而笔记本屏幕是横屏的，写上的字自然是扁的！

只需要把高漫M5数位板由竖屏映射更改为横屏映射即可！

如下修改方法参考了如下wiki内容：

 - [xsetwacom](https://github.com/linuxwacom/xf86-input-wacom/wiki/xsetwacom)
 - [Rotation](https://github.com/linuxwacom/xf86-input-wacom/wiki/Rotation)

（1）首先，查找终端中使用的“设备名称”:

```
xinput list
``` 

会输出含有如下信息的内容：

```
⎜   ↳ GAOMON Gaomon Tablet stylus             	id=15	[slave  pointer  (2)]
⎜   ↳ GAOMON Gaomon Tablet eraser             	id=16	[slave  pointer  (2)]
``` 

（2）数位板驱动程序支持90度角的旋转。旋转可以在运行时应用(例如通过 `xsetwacom`)。对于 `Rotate` 参数，只需选择您喜欢旋转的一个输入工具方向。这四个有效的设置是:

- `none`: 数位板不在软件中旋转，而是使用其默认方向。
- `half`: 数位板旋转180度(上下颠倒)。
- `cw`: 数位板顺时针旋转90度。
- `ccw`: 数位板逆时针旋转90度。

**请注意**，旋转是一个数位板范围的选择。如果你旋转一个输入工具，与同一个数位板相关的所有其他工具都要旋转到相同的方向。

因此，我们在终端运行如下命令，即可把M5旋转为横向：

```
xsetwacom set "GAOMON Gaomon Tablet stylus" rotate ccw

xsetwacom set "GAOMON Gaomon Tablet eraser" rotate ccw
``` 

虽然 `xsetwacom` 设置命令一旦输入终端就会应用，但是它们不会在重新启动后持续，（重启电脑后，数位板又再次变为竖屏映射）。为此，您可以使用这些命令创建一个可执行脚本，并将其添加到启动应用程序中。

（3）创建横屏映射脚本

如果我们每次重启系统后，都要输入命令来改变映射方向，记命令有点麻烦，我们直接建立一个sh脚本就能简化过程了！

建立一个sh文件`Tablet_PC_Rotation.sh`，脚本内容如下：

```bash
#!/bin/sh
# 将高漫 M5 数位板由竖屏改为横屏映射 CCW
# '''none''': the tablet is not rotated in software and uses its natural rotation.
# '''half''': the tablet is rotated by 180 degrees (upside-down)
# '''cw''': the tablet is rotated 90 degrees clockwise
# '''ccw''': the tablet is rotated 90 degrees counter-clockwise

# 更改为横屏start
xsetwacom set "GAOMON Gaomon Tablet stylus" rotate ccw
xsetwacom set "GAOMON Gaomon Tablet eraser" rotate ccw
# 更改为横屏end

# 更改为竖屏start
# xsetwacom set "GAOMON Gaomon Tablet stylus" rotate none
# xsetwacom set "GAOMON Gaomon Tablet eraser" rotate none
# 更改为竖屏end

#任意键
get_char()
{
    SAVEDSTTY=`stty -g`
    stty -echo
    stty cbreak
    dd if=/dev/tty bs=1 count=1 2> /dev/null
    stty -raw
    stty echo
    stty $SAVEDSTTY
}
#任意键

#任意键退出 开始
echo "已将高漫 M5 数位板由竖屏映射改为横屏映射！"
echo ""
echo "【若想将高漫 M5 数位板恢复为竖屏映射，仅需热插拔一次数位板即可！】"
echo ""
echo "每热插拔一次数位板，改为横屏映射需执行一次本脚本！"
echo ""
# echo "组合键 CTRL+C 终止运行脚本命令! ..."
echo "按任意键退出对话框..."
char=`get_char`
#任意键退出 结束
``` 

然后，建立一个执行以上脚本的`.desktop`启动快捷方式，命名为`GaomonM5Rotation.desktop`，内容如下:

```bash
#!/usr/bin/env xdg-open

[Desktop Entry]
Encoding=UTF-8
Name=GaomonM5Rotation
Name[zh_CN]=高漫M5横屏
Exec=sh /home/用户名/opt/Gaomon/Tablet_PC_Rotation.sh 
Type=Application
Terminal=true
Comment[zh_CN]=将高漫M5由竖屏改为横屏
Icon=/home/用户名/opt/icons/Gaomon.png
Name[zh_CN]=GaomonM5Rotation.desktop
Categories=Office;
``` 

图标`Gaomon.png`，自己下载一个图片文件吧。

然后把以上三个文件`Tablet_PC_Rotation.sh`、`GaomonM5Rotation.desktop`、`Gaomon.png`都放到`/home/<用户名>/opt/Gaomon/`目录下，然后，将`GaomonM5Rotation.desktop`加入开始菜单就可以了。

**注意：**

- 若想将高漫 M5 数位板恢复为竖屏映射，仅需热插拔一次数位板即可！
- 每热插拔一次数位板，改为横屏映射需执行一次本脚本！

## 使用

随便你在哪里使用啦~

用原生应用、Wine应用都是可以的啦~

Krita：

<div align=center><img src="57a3130f-c790-4b39-82e7-a929a86eb55d.png"></div>

SAI2 in Wine：

<div align=center><img src="4edd3a8f-aa54-419f-b6fa-7d5786e57f0a.png"></div>

## 参考链接

- [Linux下使用高漫M5数位板的方法](https://duter2016.github.io/2022/10/21/Linux%E4%B8%8B%E4%BD%BF%E7%94%A8%E9%AB%98%E6%BC%ABM5%E6%95%B0%E4%BD%8D%E6%9D%BF%E7%9A%84%E6%96%B9%E6%B3%95/)
- [DIGImend kernel drivers tablet support](https://digimend.github.io/drivers/digimend/tablets/)
