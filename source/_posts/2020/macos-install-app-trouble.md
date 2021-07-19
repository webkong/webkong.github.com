---
layout: post
title: MacOS10.15应用安装问题
author: webkong
categories:
  - DevOps
tags:
  - MacOS
date: 2020-02-05 21:47:06
---

程序安装之后，运行程序提示：

is damaged and can’t be opened. You should move it to the Trash.

<!-- more -->

首先切换到，允许所有开发者

```shell
sudo spctl --master-disable
```

![](../../images/2020/macos_install.jpg)

然后打开终端运行：

```shell
xattr -rc /Applications/你的应用.app
```

例如 XMind ZEN

```shell
xattr -rc /Applications/XMind\ ZEN.app

```

应用就可以打开了
