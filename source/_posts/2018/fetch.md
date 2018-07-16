---
layout: "post"
title: "Fetch新鲜的Ajax"
date: "2018-03-12 11:09"
author: "webkong"
categories:
- JavaScript
tags:
- Ajax
- Fetch
---

> 老骥伏枥，志在千里

Fetch 是一个现代的概念, 等同于 XMLHttpRequest。它提供了许多与XMLHttpRequest相同的功能,但被设计成更具可扩展性和高效性。

### 简而言之

Fetch 的核心在于对 HTTP 接口的抽象，包括 Request，Response，Headers，Body，以及用于初始化异步请求的 global fetch。得益于 JavaScript 实现的这些抽象好的 HTTP 模块，其他接口能够很方便的使用这些功能。

除此之外，Fetch 还利用到了请求的异步特性——它是基于 Promise 的。

说白了，Fetch是从后台获取数据的一种更优的解决方案，甚至可以说是XMLHttpRequest的替代方案。

但是fetch与ajax或者axios库有不同的点：
1.当接收到一个代表错误的 HTTP 状态码时，从 fetch()返回的 Promise 不会被标记为 reject， 即使该 HTTP 响应的状态码是 404 或 500。相反，它会将 Promise 状态标记为 resolve （但是会将 resolve 的返回值的 ok 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。

这个特点对于restfulAPI具有很好的交互。

2.默认情况下，fetch 不会从服务端发送或接收任何 cookies, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 credentials 选项）。


### 支持程度

目前只有谷歌和火狐浏览器的最新版本支持

### demo

```
fetch('http://example.com/example.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

```
fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
```


发送带凭据的请求
为了让浏览器发送包含凭据的请求（即使是跨域源），要将credentials: 'include'添加到传递给 fetch()方法的init对象。
```
fetch('https://example.com', {
  credentials: 'include'  
})
```
如果你只想在请求URL与调用脚本位于同一起源处时发送凭据，请添加credentials: 'same-origin'。

// The calling script is on the origin 'https://example.com'
```
fetch('https://example.com', {
  credentials: 'same-origin'  
})
```
要改为确保浏览器不在请求中包含凭据，请使用credentials: 'omit'。
```
fetch('https://example.com', {
  credentials: 'omit'  
})
```


[https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)
