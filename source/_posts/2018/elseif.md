---
layout: "post"
title: "原来你是这样的[else if]"
date: "2018-12-14 19:09"
author: "webkong"
categories:
- JavaScript
---

## 前言

最近几年一直以JavaScript最为第一语言开发，所以死扣JavaScript，突然发现`else if`原来不是我想象中的`else if`。并对自己之前的无知感到羞愧。

写过`JavaScript`的知道在写if条件语句的时候都会写成`else if`,而不是写成`elseif`。为啥中间要有空格呢？

比如`Python`会写成`elif`、`PHP`写成`elseif`、Lua写成`elseif`。

## 经常在代码中出现的

个人认为if条件语句是每个人的代码中都会出现。他们大概是这样的

```JavaScript
function testNum(a) {
  if (a > 0) {
    return "positive";
  } else {
    return "NOT positive";
  }
}

console.log(testNum(-5));
// expected output: "NOT positive"
```
或者是这样的

```JavaScript
if (x > 5) {
 /* do the right thing */
} else if (x > 50) {
 /* do the right thing */
} else if (x > 100) {
 /* do the right thing */
} else {
 /* do the right thing */
}
```

> 那么重点来了，JavaScript是没有 `else if`语句的。
>
> 啥 ？ 那我用的到底是什么。。。


## 不得不说的代码块

我们有时候会这么写`if`

```
if(a) dosomething(a)
```
很多JavaScript代码检查工具会建议应该加上`{...}`

```
if(a) {dosomething(a)}
```
同样对于`else`，在只包含单条语句的时候可以省略代码块。

所以实际上，上面的`else if`代码实际是这样的

```
if (x > 5) {
 /* do the right thing */
} else {
  if (x > 50) {
   /* do the right thing */
  } else {
    if(x > 100 ){
      /* do the right thing */
    }else{
      /* do the right thing */
    }
  }
}
```

当我们去掉`else` 的 `{...}`去掉

```
if (x > 5) {
 /* do the right thing */
} else
  if (x > 50) {
   /* do the right thing */
  } else
    if(x > 100 ){
      /* do the right thing */
    }else{
      /* do the right thing */
    }
```

然后再把 else 和 if 弄到一行。

```
if (x > 5) {
 /* do the right thing */
} else if (x > 50) {
   /* do the right thing */
} else if(x > 100 ){
  /* do the right thing */
}else{
  /* do the right thing */
}
```

就是我们经常写的`else if`。

>所以换句话说，`else if`其实只是`else`里面单独的`if`语句，去掉了`{}`，省略了一层代码缩进。

>我们自己发明的用法，而这并不是JavaScript语法的范畴。


## 多说两句

`{...}`大括号，不仅仅只是大括号。我们在很单纯的使用大括号的时候，大概是：

### 定义一个对象字面量

```
var foo = {
  key: 1,
  value: bar()
}
```
我们把大括号赋值给了一个变量`foo`,所以现在``{...}``是一个值。

### 打上一个label

当我把`var foo =`去掉，单纯的剩下一个`{...}`

```
{
  foo:bar()
}
```
这种写法在JavaScript多见，但他又是完全合法的。`{...}`在这里只是一个普通的代码块。那么里面的`foo:bar()`为啥也是合法的呢？

这种写法是不提倡的写法，也是JavaScript不常用的特性，“标签语法”。换句话说，给一条语句加上标签，可以配合`break` / `continue`来实现goto的效果。


```
foo: {
  console.log('face');
  break foo;
  console.log('this will not be executed');
}
console.log('swap');

// this will log:

// "face"
// "swap
```


有兴趣的同学可以去MDN查阅[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/label](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/label)

### other

>当`{...}`与let一起使用的时候就特别有用，可以强制劫持块的作用域。

```JavaScript
{
  console.log(bar); //ReferenceError: bar is not defined
  let bar = 'webkong'
  console.log(bar); // webkong
}
console.log(bar) //ReferenceError: bar is not defined
```


## 最后

JavaScript是一本优秀的语言，只学其中一部分很容易，全面掌握规则却很难。她有简单易用的语法，同时语言机制又十分复杂和微妙。回顾这几年，后悔的是没有给JavaScript足够的耐心和时间，把她当成一门真正的变成语言来探索和学习。很多人可能跟我一样，最开始使用她，用的多了，才开始的回过头来学习。ES标准的不断演进，概念语法上面的不断发展，让我们期待JavaScript的未来会更有作为。


>有人会说，java也是写`else if`呢...，go好像也是呢？ 那我不管，我只想写JS。
