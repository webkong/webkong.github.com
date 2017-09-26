---
layout: "post"
title: "swift字典"
date: "2017-09-22 15:26"
author: "webkong"
categories:
- Swift
tags:
- 字典
---

原文： [http://www.runoob.com/swift/swift-dictionaries.html](http://www.runoob.com/swift/swift-dictionaries.html)

>Swift 字典用来存储无序的相同类型数据的集合，Swift 字典会强制检测元素的类型，如果类型不同则会报错。
Swift 字典每个值（value）都关联唯一的键（key），键作为字典中的这个值数据的标识符。
和数组中的数据项不同，字典中的数据项并没有具体顺序。我们在需要通过标识符（键）访问数据的时候使用字典，这种方法很大程度上和我们在现实世界中使用字典查字义的方法一样。
Swift 字典的key没有类型限制可以是整型或字符串，但必须是唯一的。
如果创建一个字典，并赋值给一个变量，则创建的字典就是可以修改的。这意味着在创建字典后，可以通过添加、删除、修改的方式改变字典里的项目。如果将一个字典赋值给常量，字典就不可修改，并且字典的大小和内容都不可以修改。
