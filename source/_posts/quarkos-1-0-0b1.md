---
title: QuarkOS_1.0.0beta1 发行说明
date: 2022-02-11 12:14:25
tags: QuarkOS
---
# QuarkOS v1.0.0 beta1 发行说明
## 说明
这是QuarkOS的第一个发行版，基于Deepin v23与Debian bullseye制作。

内涵如下软件：
* 星火应用商店
* 微信
* 网易云音乐
* Chromium

## 已知问题
 1. 在efi平台上安装需要使用ventoy制作启动盘，并且在安装成功后需要额外配置grub,此操作需要网络连接：
 ```bash
 $ for i in dev proc sys; do mount --rbind /$i /mnt/$i; done
 $ cp /etc/resolv.conf /mnt/etc/resolv.conf
 $ chroot /mnt /bin/bash
 $(chroot) apt purge grub-pc grub-efi grub-efi-amd64
 $(chroot) rm -r /boot/grub
 $(chroot) apt install grub-efi-amd64
 $(chroot) exit
 $ for i in dev/pts dev/ proc sys; do umount -lf /mnt/$i; done
 $ rm /mnt/etc/resolv.conf
```
## 下载链接
* [官方下载]() 密码：quarkos