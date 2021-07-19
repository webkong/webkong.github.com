---
layout: post
title: 如何优雅的使用mock server
author: webkong
categories:
  - JavaScript
tags:
  - mock
  - webpack
  - Vue
date: 2018-12-07 11:24:25
---

## 事出有因

昨天跟同事讨论我们在用的[rap2](https://github.com/thx/rap2-delos)(一个集接口编写和 mock server 的开源项目)和刚上线了一个 easy-mock 的 server，到底哪个好用。

我们主要讨论的点有个两个： 接口的一致性、 编码的无侵入性。

<!-- more -->

## 背景

自从前后端分离后，完成前后端的分工之后，大家就可以各司其职，并行开发。前后端的协议标准就是接口文档。前端的所有逻辑和展现全部依赖接口文档中规定的数据结构。所以接口文档就变成了开发过程的重中之重。

当然有一份文档也是不够的，前端开发页面逻辑调试、测试同学测试接口、接口文档不断的更新、接口复杂的返回情况等诸多问题，这就需要有一个 Mock Server 来帮忙。

更多的关于 Mock 内容的请大家自行 Google 了解不再赘述。

## 接口的一致性

接口文档，是整个开发过程中的约定和依据，所以同步更新文档也成为关键一环。

### 之前的办法

使用 markdown 维护一个文档，放到 wiki 来管理，每次更新到 wiki 上面，大家来阅读。如果要 Mock 数据（easy-mock 或者其他 mock server），相关人员修改 mock 字段，更新完毕。

### 优雅的做法

在线编辑接口，实时保存，同步更新。例如采用 Rap2（支持 JSON 复制，支持接口的移动复制），保存后就是接口文档，所有人实时更新，同时 Mock 数据自动更新。

## 编码的无侵入性

> Mock 对代码的侵入越小，我们维护和开发的成本也越低。以 `Vue-cli2.x`生成的项目为例：

在工程中的 config 目录`dev.env.js`，配置开发环境下 API 数据来源地址

```JavaScript
...
module.exports = merge(prodEnv, {
    NODE_ENV: '"development"',
    ENV_CONFIG: '"dev"',
    // BASE_API: '"dev server"',
    BASE_API: '"http://rap2.taobao.org:8080/app/mock/39"', // mock server
})

```

这样我们在开发过程中，用到`axios`，只需要根据不同环境编写一次代码即可

```javaScript
const service = axios.create({
  baseURL: process.env.NODE_ENV === 'production' ? process.env.BASE_API : '/api',
  ...
});
```

从 Mock 数据切换到 Dev Server 的 API 只需要注释一行配置文件代码，无需修改项目源代码。非常友好。

这时候我们再看 easy-mock 跟 rap2 生成的 Mock Url：

```
http://easy-mock.com/mock/5c08e554bd9f9c3a01c5abf6/api/info
=> host/mock/projectId/:path

http://rap2.taobao.org:8080/app/mock/45/GET/api/info
=> host/app/mock/:repositoryId/:method/:path
```

> ps:后来发现 rap2 的 url 可以不带 method，但是就是不能使用 restful API 这种根据 method 区分的接口了。

从生成的 url 来看，rap2 中有`:method`的变量，若是要无缝切换需要更多的工作。无法直接替换`BASE_API`。如果在代码里拼接会有很大的代码侵入性。

所以考虑在工程层面进行优化：

在工程目录`config`目录中`index.js`中，可以设置代理来统一处理`:method`变量。在`proxyTable`对象中加入[`bypass`](https://webpack.docschina.org/configuration/dev-server/#devserver-proxy)方法，对每个通过代理的接口，处理 request url 成为一个合格的 rap2 mock 的 url。

```javaScript
module.exports = {
  dev: {
    ...
    proxyTable: {
      '/api': {
        target: config.BASE_API.substring(1, config.BASE_API.length - 1),
        changeOrigin: true,
        pathRewrite: {
          '^/api': '/'
        },
        bypass: function (req, res, proxyOptions) {
          if (proxyOptions.target.indexOf('mock') !== -1) {
            const path = req.url.replace(proxyOptions.context, proxyOptions.context + '/' + req.method);
            // path => /api/GET/path
            req.url = path;
          }
        }
      }
    },
```

这样就能无侵入性的使用 rap2 了。

## 结论

从接口文档的一致性和编码的无侵入性来看，rap2 可能更优一点，但是 rap2 毕竟是一个年轻的开源项目，很多机制并不完善。从去年年初使用 rap2，最近升级到新版本数据库表结构还要做映射调整，适合喜欢折腾的人。这样 easy-mock 的稳定性优势就值得考虑。

在我看来，未来的 Mock Server 不单单只是一个 Mock，肯定是集 接口编写 + 自动 Mock 于一身的，还有一些类似接口字段校验的辅助功能。

个人还是很看好 rap2 的，毕竟从 rap1 重构的项目，有一定的使用背景，重构应该能出更好的开源项目。

再次感谢开源项目对开发者带来的便利。
