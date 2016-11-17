---
title: JavaScript隐式转换
date: 2016-01-27 11:46:55
categories:
- JavaScript
tags:
- JavaScript
- 类型转换
- 原创
---

当一些操作涉及到不一样的数据类型，就会出现类型转换（隐形）

+ 符号`+ -`
```
  + 有字符串拼接和加法运算的功能

  如果把一个变量a ，转换成数字，最简单的 方法就是 用 a-0
  同理如果想转化成字符串就 a+''  ,用变量加上一个空字符串

```
+ 等于"=="和严格等于"==="
```
严格等于 ===
判断两边的类型（相同or不同），直接返回true OR  false
null === null     true  == 也是true
undefined === undefined   true  == 也是true
NaN === NaN  返回false ，NaN 不等于自己 。 == 也返回false
new Object 不等于  new Object  因为他们不是相同的对象,返回false  。 == 也返回false
```
+ 类型不同会尝试转换类型后比较
```
null == undifined  true
number == string  转 number  1 == '1.0'  true
boolean == ?  转 number true == 1 true
Object == number | string  尝试转换成基础类型
new String('hi') == 'hi' true 其他的返回FALSE
```
