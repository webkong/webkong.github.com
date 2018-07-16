---
layout: "post"
title: "ubuntu18.04 install grub error"
date: "2018-05-04 11:14"
author: "webkong"
categories:
- Ubuntu
tags:
- grub
---

场景1： 单Ubuntu系统安装， 整个磁盘已经格式化， 安装到最后的时候，会出现无法安装引导到/target/,导致安装失败
场景2： 安装成功，但是重启进入 grub rescue
场景3： 安装成功，重启进去黑屏光标界面，无法正常重启

解决经历

1. 使用 boot-repair 进行引导修复，未成功
2. 在grub rescue 模式情况下手动set

```
grub rescue>ls
grub rescue>ls (hd0,gpt2)/boot/grub
grub rescue>set root=(hd0,gpt2)
grub rescue>set prefix=(hd0,gpt2)/boot/grub
grub rescue>insmod /boot/grub/normal.mod
```
报错，仍未成功

3. 尝试重新安装，选择抹掉整个磁盘自动安装，重启仍然失败

4. 尝试手动分区

```
/ 10G
/tmp 10G
/home 400G
/boot 200M
SWAP 4G
```
自动安装后仍然失败
grub-efi-amd64-signed package failed to install into /target/
5.
