---
layout: "post"
title: git ssh配置和使用
date: 2017-01-20 17:17:55
categories:
  - Git
tags:
  - git
  - 原创
---

> 记录一下 git 的 ssh 配置,备忘。

1.配置用户和邮箱

```
$ git config --global user.name "webkong"
$ git config --global user.email "webkong@webkong.cn"
```

如果在多用户情况下，一般设置本地用户

```
//cd to repo path
$ git config --local user.name "webkong"
$ git config --local user.email "webkong@webkong.cn"
```

<!-- more -->

2.生成密钥

```
$ ssh-keygen -t rsa -C "webkong@webkong.cn"  [-f id_rsa_github]

// -f 后面带生成的文件名， 在配置多git账号的时候使用,以区分不同的密钥文件

然后连续敲3个回车，确认默认设置。

就会在 ~/ 用户目录下生成.ssh文件包含 id_rsa 和 id_rsa.pub两个文件。
```

id_rsa.pub 就是公钥文件，将内容复制出来，添加到私人 Git 服务器或者是 github 上。

可以使用 ssh-agent 密钥管理器，将私钥交给 ssh-agent 来管理和使用

> 如果 windows 系统上传完还是不能使用，就要将 ssh 加入到 agent 里面。

```
ssh-agent -s
ssh-add ~/.ssh/id_rsa
```

> 如果 执行 ssh-add 时添加私钥到 git 中报错 Could not open a connection to your authentication agent

```
eval `ssh-agent` //执行启动，再执行add操作
```

```
ssh-add -l  //查看
```

> 若途中报错 “Permissions ???? for '/Users/xxx/.ssh/rsa' are too open”的问题，则是该私钥文件权限过高，需要设置其权限

```
chmod 600 ~/.ssh/rsa
```

3.多 git 账号配置

在 `~/.ssh`目录新建文件 `config`

```
Host github.com //配置host地址
    HostName github.com
    User webkong //用户名
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github //公钥地址

Host gitlab.webkong.org
    HostName gitlab.webkong.org
    User wangsw
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
```
