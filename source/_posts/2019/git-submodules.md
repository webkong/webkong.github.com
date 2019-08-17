---
layout: "post"
title: "git子模块的管理和使用"
date: "2019-08-17"
author: "webkong"
categories:
- Git
---

> 备忘速查
>git子模块的管理和使用


## 添加子模块

```
git submodule add https://github.com/webkong/baby.git baby
```
<!-- more -->
## 查看子模块

```
git submodule
```

## 更新子模块

### 更新项目内子模块到最新版本

```
 git submodule update
```

### 更新子模块为远程项目的最新版本

```
$ git submodule update --remote
```

## 克隆包含子模块的项目

克隆包含子模块的项目有二种方法：一种是先克隆父项目，再更新子模块；另一种是直接递归克隆整个项目。

### 克隆父项目

```
git clone https://github.com/webkong/baby.git baby
```

#### 查看子模块

```
$ git submodule
 -e33f854d3f51f5ebd771a68da05ad0371a3c0570 baby
```

子模块前面有一个-，说明子模块文件还未检入（空文件夹）。

#### 初始化子模块

```
$ git submodule init
Submodule 'baby' (https://github.com/webkong/baby.git) registered for path 'baby'
```

初始化模块只需在克隆父项目后运行一次。

#### 更新子模块

```
$ git submodule update
Cloning into 'baby'...
remote: Counting objects: 151, done.
remote: Compressing objects: 100% (80/80), done.
remote: Total 151 (delta 18), reused 0 (delta 0), pack-reused 70
Receiving objects: 100% (151/151), 1.34 MiB | 569.00 KiB/s, done.
Resolving deltas: 100% (36/36), done.
Checking connectivity... done.
Submodule path 'baby': checked out 'e33f854d3f51f5ebd771a68da05ad0371a3c0570'
```

### 递归克隆整个项目

```
git clone https://github.com/webkong/baby.git baby --recursive
```

递归克隆整个项目，子模块已经同时更新了，一步到位。

## 修改子模块

在子模块中修改文件后，直接提交到远程项目分支。

```
$ git add .
$ git ci -m "commit"
$ git push origin HEAD:master
```

## 删除子模块

删除子模块比较麻烦，需要手动删除相关的文件，否则在添加子模块时有可能出现错误
同样以删除 baby 文件夹为例

### 删除子模块文件夹

```
$ git rm --cached baby
$ rm -rf baby
```

### 删除.gitmodules 文件中相关子模块信息

```
[submodule "baby"]
  path = baby
  url = https://github.com/webkong/baby.git
```

### 删除.git/config 中的相关子模块信息

```
[submodule "baby"]
  url = https://github.com/webkong/baby.git

```

### 删除.git 文件夹中的相关子模块文件

```
$ rm -rf .git/modules/baby
```
