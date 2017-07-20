---
layout: "post"
title: "Vue-cli使用自定义模板"
date: "2017-07-19 16:26"
author: "webkong"
categories:
- Vue
tags:
- Vue-cli
- 自定义模板
- JavaScript
---

>Vue，越来越流行，因为它的易用性和良好的性能，随之而来的是vue-cli初始化项目，没有适用所有场景的模板，根据自己的业务场景初始化项目，才是最酷的。

<!-- more -->

### 官方模板

1. 前提安装 Vue-cli

```
$ npm install -g vue-cli
```

2. 初始化方式

```
$ vue init <template-name> <project-name>
```
例如：

```
$ vue init webpack test
```
创建一个 webpack 为模板的 名为 test 的项目


3. 查看官方的模板

```
$ vue list
```
会输出

```
  ★  browserify - A full-featured Browserify + vueify setup with hot-reload, linting & unit testing.
  ★  browserify-simple - A simple Browserify + vueify setup for quick prototyping.
  ★  pwa - PWA template for vue-cli based on the webpack template
  ★  simple - The simplest possible Vue setup in a single HTML file
  ★  webpack - A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.
  ★  webpack-simple - A simple Webpack + vue-loader setup for quick prototyping.

```
这就是官方提供给我们的几种模板， 拿webpack为例， 默认初始化是没有`sass`相关包的， 当然也没有`http`请求的包，那么就需要一个适合自己的模板来初始化项目。


4. 自定义模板并使用

>以MAC为例

vue-cli 默认的模板存放在 `~/.vue-templates`目录下面， 我们可以根据官方的`webpack`来自定义自己的模板 `webpack2`。

用自定义模板的时候

```
vue init ~/.vue-templates/webpack2 test
```



### 参考

[https://github.com/vuejs/vue-cli](https://github.com/vuejs/vue-cli)
