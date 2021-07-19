---
title: webpack的安装
date: 2016-07-19 10:16:50
categories:
- 前端自动化
tags:
- JavaScript
- 模块化
- webpack
- 原创
---

前端不能少了模块化、压缩、打包等等的工具，就来用一下webpack。Mac OX 系统为例。

>[webpack官网](http://webpack.github.io/)

**安装webpack**

>需要的依赖
>1. [node.js](http://nodejs.org/)
>2. npm

执行命令
```
sudo npm install webpack -g
```
输入密码，安装完成之后，就在全局安装了webpack，并且`webpack`命令就可以使用了。

**在项目中使用**

```
$ mkdir test
$ cd test
$ npm init

```
通过npm 初始化一个项目，生成一个`package.json`文件。

```
npm install webpack --save-dev
```
就会在项目中添加`webpack`的依赖，也可以选择安装哪个版本

```
$ npm install webpack@1.2.x --save-dev
```
后面会说到如何配置webpack，和如何添加jQuery和jQuery的插件。
