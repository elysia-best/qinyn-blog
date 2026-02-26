---
title: 在 LoongArch64 上编译 Blender 的折腾记录
date: 2026-02-27 00:54:02
tags:
  - LoongArch64
  - Blender
---

最近在折腾 LoongArch64，发现 Blender 在这个架构上还没有官方支持，于是决定自己编译一个。本文记录了我在编译过程中遇到的一些问题以及解决方法。

目前这个完整的构建流程已经搞定了，折腾记录先咕咕一下，放一下相关链接：

- https://opensrc.qinyn.eu.org/lanzou/%E9%BE%99%E6%9E%B6%E6%9E%84%E8%B5%84%E6%BA%90/%E8%BD%AF%E4%BB%B6/Blender
- https://github.com/elysia-best/blender-libs-builder
- https://projects.blender.org/Yuanzhen-Gan/lib-linux_loongarch64
- https://developer.blender.org/docs/handbook/release_process/build/rocky_8/#setting-up-the-rocky-8-environment
