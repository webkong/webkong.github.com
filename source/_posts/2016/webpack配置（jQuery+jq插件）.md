---
title: webpack配置（jQuery+jq插件）
date: 2016-07-19 18:16:50
categories:
- 前端自动化
tags:
- JavaScript
- 模块化
- webpack
- jQuery
- 原创
---

>安装webpack过程略过，同学可自行查看上一篇文章.

1.在项目目录下新建文件`webpack.config.js`

```
module.exports = {
     entry: './src/app.js',
     output: {
         path: './bin',
         filename: 'app.bundle.js'
     }
 };
 ```
这个一个符合CommonJS风格的module

2.简单的配置之后就可以运行打包。
```
webpack
```
webpack就会读取配置文件`webpack.config.js`，build并生成`app.bundle.js`

3.还可以依赖插件,通过loader加载不同格式的文件
```
const webpack = require('webpack');

module.exports = {
    entry: './src/app.js',
    output: {
        path: './bin',
        filename: 'app.bundle.js',
    },
    module: {
        loaders: [{ //加载器
            test: /\.jsx?$/,
            exclude: /node_modules/,
            loader: 'babel',
        }]
    },
    resolve: {
        extension: ['', '.js', '.css'],
        alias: {//设置别名
            jquery: ("jquery"),
            qrcode: ("jquery.qrcode")
        }
    },
    plugins: [
        new webpack.ProvidePlugin({
            $: "jquery",
            jQuery: "jquery",
            "window.jQuery": "jquery"
        })
    ]
}
```
4.**使用jQuery和jQuery插件**
如上面的代码plugins：
```
lugins: [ //全局变量不用require
    new webpack.ProvidePlugin({
        $: "jquery",
        jQuery: "jquery",
        "window.jQuery": "jquery"
    })
]
```
>对于不支持CommonJS规范的模块，需要安装 [imports-loader](https://github.com/webpack/docs/wiki/shimming-modules)

```
//app.js
require('imports?$=jquery!qrcode'); //引入 jQuery的二维码插件
这样就可以成功引入jQuery和jQuery的插件。
```

>config文件说明

**entry**
配置要打包的文件的入口;可以配置多个入口文件，下面会有介绍。

**resolve**
配置文件后缀名(extensions)，除了js，还有jsx、coffee等等。
alias配置项，可以为常用模块配置改属性，可以节省编译的搜索时间。例如：
```
    resolve:{
        extensions:['.js','.jsx'],
        alias:{
            'react':path.join(nodeModulesPath,'react/react.js')
        }
    }
```

**output**
配置输出文件的路径，文件名等。

**module(loaders)**
配置要使用的loader。对文件进行一些相应的处理。比如babel-loader可以把es6的文件转换成es5。
大部分的对文件的处理的功能都是通过loader实现的。loader就相当于gulp里的task。loader可以用来处理在入口文件中require的和其他方式引用进来的文件。loader一般是一个独立的node模块，要单独安装。

loader配置项：
```
test: /\.(js|jsx)$/,           //注意是正则表达式，不要加引号，匹配要处理的文件
loader: 'eslint-loader',       //要使用的loader，"-loader"可以省略
include: [path.resolve(__dirname, "src/app")],   //把要处理的目录包括进来
exclude: [nodeModulesPath]     //排除不处理的目录
```
目前已有的loader列表：https://webpack.github.io/docs/list-of-loaders.html

一个module的例子:
```
module: {
    preLoaders: [
      {
        test: /\.(js|jsx)$/,
        loader: 'eslint-loader',
        include: [path.resolve(__dirname, "src/app")],
        exclude: [nodeModulesPath]
      },
    ],
    loaders: [
      {
        test: /\.(js|jsx)$/, //正则表达式匹配 .js 和 .jsx 文件
        loader: 'babel-loader?optional=runtime&stage=0',//对匹配的文件进行处理的loader
        exclude: [nodeModulesPath]//排除node module中的文件
      }
    ]
}
```
**plugins**
顾名思义，就是配置要使用的插件。plugin是比loader功能更强大的插件，能使用更多的wepack api。来看一个使用plugin的例子：
```
plugins: [
    //压缩打包的文件
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        //supresses warnings, usually from module minification
        warnings: false
      }
    }),
    //允许错误不打断程序
    new webpack.NoErrorsPlugin(),
    //把指定文件夹xia的文件复制到指定的目录
    new TransferWebpackPlugin([
      {from: 'www'}
    ], path.resolve(__dirname,"src"))
  ]
```
目前已有的plugins列表：[http://webpack.github.io/docs/list-of-plugins.html](http://webpack.github.io/docs/list-of-plugins.html)


[webpack官方文档](http://webpack.github.io/docs/usage.html)


>可以暴露成全局变量的2个配置 ,引用国际友人的解释。[来源](http://stackoverflow.com/questions/23305599/webpack-provideplugin-vs-externals)

`externals`
`ProvidePlugin`

It's both possible: You can include libraries with a `<script>` (i. e. to use a library from a CDN) or include them into the generated bundle.

If you load it via `<script>` tag, you can use the externals option to allow to write require(...) in your modules.

Example with library from CDN:
```
<script src="https://code.jquery.com/jquery-git2.min.js"></script>

// the artifial module "jquery" exports the global var "jQuery"
externals: { jquery: "jQuery" }

// inside any module
var $ = require("jquery");
```
Example with library included in bundle:
```
copy `jquery-git2.min.js` to your local filesystem

// make "jquery" resolve to your local copy of the library
// i. e. through the resolve.alias option
resolve: { alias: { jquery: "/path/to/jquery-git2.min.js" } }

// inside any module
var $ = require("jquery");
```
The `ProvidePlugin` can map modules to (free) variables. So you could define: "Every time I use the (free) variable xyz inside a module you (webpack) should set `xyz` to `require("abc")`."

Example without `ProvidePlugin`:
```
// You need to require underscore before you can use it
var _ = require("underscore");
_.size(...);
```
Example with ProvidePlugin:
```
plugins: [
  new webpack.ProvidePlugin({
    "_": "underscore"
  })
]

// If you use "_", underscore is automatically required
_.size(...)
```
Summary:

Library from CDN: Use `<script>` tag and `externals` option
Library from filesystem: Include the library in the bundle. (Maybe modify resolve options to find the library)
`externals`: Make global vars available as module
`ProvidePlugin`: Make modules available as free variables inside modules
