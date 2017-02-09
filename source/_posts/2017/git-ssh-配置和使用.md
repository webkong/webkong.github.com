---
layout: "post"
title: git-ssh 配置和使用
date: 2017-01-20 17:17:55
categories:
- 版本控制
tags:
- git
- 原创
---

> 记录一下git的ssh配置,备忘。

1.配置用户和邮箱
```
$ git config --global user.name "webkong"
$ git config --global user.email "webkong@webkong.cn"
```
2.生成密钥

```
$ ssh-keygen -t rsa -C "webkong@webkong.cn"  [-f id_rsa_github]

* 可以在后面带生成的文件名， 在配置多git账号的时候使用,以区分不同的密钥文件。

然后连续敲3个回车，确认默认设置。

就会在 ~/ 用户目录下生成.ssh文件包含 id_rsa 和 id_rsa.pub两个文件。
```
id_rsa.pub 就是公钥文件，将内容复制出来，添加到私人Git服务器或者是github上。


ssh就配置好了。

可以使用 ssh-agent 密钥管理器，将私钥交给ssh-agent来管理，使用

```
ssh-agent -s
ssh-add ~/.ssh/id_rsa
```
添加。


3.多git账号配置

```
Host *.github.com
HostName github.com
IdentityFile ~/.ssh/id_rsa_github
User webkong
Host dev
HostName 192.169.1.1
IdentityFile ~/.ssh/id_rsa_dev
User wangsw
```
