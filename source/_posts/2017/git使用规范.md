---
layout: "post"
title: "Git使用规范"
date: "2017-12-20 15:18"
author: "webkong"
categories:
  - Git
tags:
  - git
---

> 针对 git 提交，做出如下规范，避免出现提交描述不清楚，回溯困难。

## 1.必须先拉取代码再提交代码

```
git pull
#或者
git fetch
git rebase
```

避免出现不必要的自动`merge`，和冲突代码

## 2.提交代码原则(粒度)

项目代码稳定之后(总体架构完成)，每次提交都要考虑提交的粒度问题，尽量做到 `baby step`

- 没有关联的代码不能一次 commit 提交
- 关联代码一次提交；若内容很多，可内部分层，依次提交

可以根据当前代码模块情况处理。

## 3.必须提交 Commit Message

- 没有 Commit Message 不允许提交
- 不在 `git commit` 上增加 `-m <msg>` 或 `--message=<msg>` 参数，而单独写提交信息

不允许

```
git commit -m "Fix login bug"
```

推荐

angular 的 GitHub 的 Commit

```

Fix(filterFilter): fix filtering using an object expression when the …
…filter value is undefined

Fixes #10419
Closes #10424
```

### 段落 Commit Message

Commit Message 分三个部分， header, body, footer. 其中，**header** 是必需的，body 和 footer 可以省略。
不管是哪一个部分，任何一行都不得超过 100 个字符，以免影响阅读性。

```
[<team>]<type>(<scope>): <subject>

<body>

<footer>　
```

```
[FE]Fix(card): fix card edit support to unuse item

在card修改里面，添加设置item未使用的功能
```

#### header

Header 部分只有一行，包括三个字段：type（必需）、scope（可选）、team（可选）和 subject（必需）。

##### type

用于说明 commit 的类别，只允许使用下面 7 个标识。

    Add：新加功能

    Fix：修补bug

    Modify：移除无用代码

    Remove：移除第三方模块或者移除文件

    Update：更新第三方模块

    Style： 格式（不影响代码运行的变动）

    Chore：构建过程或辅助工具的变动

##### scope

scope 用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

例如在 shrezone，可以是 file, card, image, short_video 等。

如果你的修改影响了不止一个 scope，你可以使用\*代替。

##### team

当不同的 team 一起编写代码的时候，带 team 以区分工作区域

##### subject

subject 是 commit 目的的简短描述，不超过 50 个字符。

其他注意事项：

    以动词开头，使用第一人称现在时，比如change，而不是changed或changes

    第一个字母小写

    结尾不加句号（.）

#### Body

Body 部分是对本次 commit 的详细描述，可以分成多行。

比如 react 的提交

```
Sync out another jsx transform test.

We added this test internally and it never got added here. We haven't broken it
with any transforms *yet* but it's still good to actually run all of our tests
here too.
```

注意点:

    使用第一人称现在时，比如使用change而不是changed或changes。

    要和header之间有一个空行

    应该说明代码变动的动机，以及与以前行为的对比。

#### Footer

Footer 部分只用于以下两种情况：

##### 不兼容变动

如果当前代码与上一个版本不兼容，则 Footer 部分以 BREAKING CHANGE 开头，后面是对变动的描述、以及变动理由和迁移方法。

##### 关闭 Issue

如果当前 commit 针对某个 issue，那么可以在 Footer 部分关闭这个 issue 。

#### Revert

一种特殊情况，如果当前 commit 用于撤销以前的 commit，则必须以 revert:开头，后面跟着被撤销 Commit 的 Header。

```
revert: feat(pencil): add ‘graphiteWidth‘ option

This reverts commit 667ecc1654a317a13331b17617d973392f415f02
```
