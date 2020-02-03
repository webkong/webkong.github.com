---
layout: "post"
title: "git的删除操作"
date: "2014-09-23 11:24"
categories:
  - Git
tags:
  - git
  - 原创
---

> 记录一下 git 的删除回滚类操作

**_本地库操作_**

> 场景： 已经 commit 但是没有 push

```
git log
//查看上次提交的commit-id (hash值)

git reset --soft commit-id
//撤销commit操作，保留commit时候改的的代码。
```

强制回滚

```
git reset --hard commit-id
//回退到某个版本，并且删除修改的文件
```

> 场景： 没有 commit，而且是新建的文件或者文件夹，抛弃所有这些修改

```
git clean
//clean 用来 抛弃所有untracked 状态的文件
```

> tips: git 文件的三种状态

1.  untracked 未被 git 跟踪的文件 (新增文件，未加入到暂存区)
2.  new files(Changes to be committed) 文件被 commit 到了暂存区
3.  modified (Changes not staged for commit) 已经提交过的文件被修改，还未提交到暂存区

> 场景： 抛弃所有 modified 状态的 文件

```
git checkout .  //一定要带点（.）

git checkout -- ''*.js'
//如果是删除某类文件可以用

git checkout -- 1.js
//删除某个文件
```

> 如果要删除工作区的修改可以连用

`git checkout . && git clean -xdf`

> 场景： 提交信息写错，或者遗漏提交

```
//如果有遗漏，先add
git add

//然后再次提交
git commit -m "some message" --amend
```
