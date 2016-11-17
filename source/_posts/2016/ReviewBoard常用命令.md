---
layout: "post"
title: "ReviewBoard常用命令"
date: "2016-09-23 14:38"
categories:
- 代码工具
tags:
- reviewboard
- 原创
---

> 作为码农，review代码是每天必须要做的。
> 相互review代码，是找出bug,学习进步的好方法。
> [Code review doesn't have to be so hard -- ReviewBoard](https://www.reviewboard.org/)

> 本次只是介绍RBT（RevieBoard的客户端命令行工具）使用，服务器安装请自行扒官网

#### 依赖

git, python

#### 下载

[https://www.reviewboard.org/downloads/rbtools/](https://www.reviewboard.org/downloads/rbtools/)

#### 安装完成

启动命令行工具

```
➜  rbtTest git:(master) rbt -v
RBTools 0.8 alpha 0 (dev)
```
说明安装成功了

#### 使用

1.进入到项目目录执行 `setup-repo` ,创建 `.reviewboardrc`文件
`rbt setup-repo`
  * 提示输入 reviewBoard server的地址，根据你的服务器地址输入
  * 提示 是否使用当前的Git库  输入 yes
  * 提示 按照如下创建 .reviewboardrc文件么 输入yes
```
REVIEWBOARD_URL = "server"
REPOSITORY = "rbtTest"
BRANCH = "master"
LAND_DEST_BRANCH = "master"
```
2.登录

`rbt login`

3.提交审核
```
//所有的post审核都是在 git commit 之后的
//第一次提交
rbt post
```
输出
```
Review request #1052 posted.

http://server/r/1052/
http://server/r/1052/diff/
```

证明post提交成功，可以去控制台设置review的人和组，然后publish就能让别人看到了

```
//第二次修改针对第一次提交

`rbt post -r 1052`

输出
Review request #1052 posted.

http://server:8083/r/1052/
http://server:8083/r/1052/diff/

```
4.跟踪状态

`rbt status`

5.关闭审核

`rbt close --close-type=discarded 1052`

6.退出

`rbt logout`

7.帮助

`rbt help`
