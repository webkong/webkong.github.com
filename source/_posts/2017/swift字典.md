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

>原文：

[http://www.runoob.com/swift/swift-dictionaries.html](http://www.runoob.com/swift/swift-dictionaries.html)
[https://developer.apple.com/](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105)

>Swift 字典用来存储无序的相同类型数据的集合，Swift 字典会强制检测元素的类型，如果类型不同则会报错。
Swift 字典每个值（value）都关联唯一的键（key），键作为字典中的这个值数据的标识符。
和数组中的数据项不同，字典中的数据项并没有具体顺序。我们在需要通过标识符（键）访问数据的时候使用字典，这种方法很大程度上和我们在现实世界中使用字典查字义的方法一样。
Swift 字典的key没有类型限制可以是整型或字符串，但必须是唯一的。
如果创建一个字典，并赋值给一个变量，则创建的字典就是可以修改的。这意味着在创建字典后，可以通过添加、删除、修改的方式改变字典里的项目。如果将一个字典赋值给常量，字典就不可修改，并且字典的大小和内容都不可以修改。

<!-- more -->

### 创建空字典

```swift
 var namesOfIntegers = [Int: String]()
// namesOfIntegers is an empty [Int: String] dictionary

namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = [:]
// namesOfIntegers is once again an empty dictionary of type [Int: String]

```

###  创建一个字典

```swift
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
//也可以
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

### 访问和修改字典

```swift
//add
airports["LHR"] = "London"
// the airports dictionary now contains 3 items

//accessing
airports["YYZ"] // 如果没有返回nil

//remove for key
removedValue = airports.removeValue(forKey: "DUB")

//modify/update
airports["LHR"] = "London Heathrow"

//update
airports.updateValue("Dublin Airport", forKey: "DUB")

```

### 遍历字典

```swift
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}

//只遍历值或者key
for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}
// Airport code: YYZ
// Airport code: LHR

for airportName in airports.values {
    print("Airport name: \(airportName)")
}
// Airport name: Toronto Pearson
// Airport name: London Heathrow

还可以单独使用keys和values来获取对应的array


let airportCodes = [String](airports.keys)
// airportCodes is ["YYZ", "LHR"]

let airportNames = [String](airports.values)
// airportNames is ["Toronto Pearson", "London Heathrow"]

```
