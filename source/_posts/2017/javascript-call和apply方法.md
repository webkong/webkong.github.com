---
layout: "post"
title: "javascript-call和apply方法"
date: "2017-06-20 15:04"
author: "webkong"
categories:
- JavaScript
tags:
- 函数
- call
- apply
- 原创
- JavaScript
---

> 诗三百，一言以蔽之，思无邪。
> 曲千奏，一朝为闻之，念起灵。
> 戏千吟，一舞为倾之，葬语花。
> 棋百枚，一泣为终之，焚墨目。


使用别人的方法，拿来主义的实现者，`call()`和`apply()`。

<!-- more -->


### Function.prototype.call()

#### 语法：

  `fun.call([thisObj[,arg1[, arg2[,   [,.argN]]]]]) `

#### 参数：
  thisObj : 在fun函数运行时指定的this值。

  arg1,arg2... : 参数列表

#### 说明：

让`call()`中的对象，调用当前对象(fun) 所拥有的function。

`call()`方法可将一个函数的对象上下文从初始的上下文改变为由 `thisObj` 指定的新对象。

如果没有提供 `thisObj` 参数，那么 `Global` 对象被用作 `thisObj`。


#### 示例

```JavaScript
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}
var f = new Food('webkong', 1999);

console.log(f.name,f.price);

```

```JavaScript
function add(a, b) {
  console.log(a + b);
}

function sub(a, b) {
  console.log(a - b);
}

add.call(sub, 3, 1); //4
```



### Function.prototype.apply()

#### 语法：

  `fun.apply([thisObj[,argArray]]) `

#### 参数：
  thisObj : 在fun函数运行时指定的this值。

  argArray: 参数数组

#### 说明：

apply()方法和 call() 方法类似，只有一个区别，就是call()方法接受的是若干个参数的列表，而apply()方法接受的是一个包含多个参数的数组。

如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。
如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。






### 参考

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
