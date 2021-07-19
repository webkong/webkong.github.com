---
title: JavaScript连等赋值
date: 2016-07-18 20:00:07
categories:
- JavaScript
tags:
- JavaScript
- 连等赋值
- 原创
---
说一下JavaScript的连等赋值，遇到的坑，例如
```
var a = {n:1};
var b = a; // 持有a，以回查
a.x = a = {n:2};
alert(a.x);// --> undefined
alert(b.x);// --> {n:2}

```
我的思路出发点
>1.运算符优先级来考虑，.运算优先于=赋值运算
>2.右结合性

```
var a = {n:1}; /*定义a，赋值为{n:1}，假设指向存储地址为add_1*/
var b = a; /*定义b，赋值为a，指向同一存储地址add_1*/
a.x = a = {n:2};
/*（1）：定义a.x，赋值为a，即{n:1}，存储地址add_1上面的内容被更改
  （2）：a.x重新赋值为{n:2}，存储地址add_1上面的内容被更改
  （3）：a重新赋值为{n:2}，更改存储地址add_2*/
alert(a.x);
/*现在a的存储地址add_2，内容为{n:2}，上面并不存在a.x属性，所以为undefined*/
alert(b.x);
/*现在b的存储地址add_1，内容为{n:1,x:{n:2}}，所以b.x为{n:2}*/

```
