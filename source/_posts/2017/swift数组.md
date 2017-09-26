---
layout: "post"
title: "swift数组"
date: "2017-09-22 15:05"
author: "webkong"
categories:
- Swift
tags:
- 数组
---

>参考：

[http://www.runoob.com/swift/swift-arrays.html](http://www.runoob.com/swift/swift-arrays.html)
[https://developer.apple.com/](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105)

>Swift 数组使用有序列表存储同一类型的多个值。相同的值可以多次出现在一个数组的不同位置中。
Swift 数组会强制检测元素的类型，如果类型不同则会报错，Swift 数组应该遵循像Array<Element>这样的形式，其中Element是这个数组中唯一允许存在的数据类型。
如果创建一个数组，并赋值给一个变量，则创建的集合就是可以修改的。这意味着在创建数组后，可以通过添加、删除、修改的方式改变数组里的项目。如果将一个数组赋值给常量，数组就不可更改，并且数组的大小和内容都不可以修改。

<!-- more -->

### 创建数组

```Swift
var someArray = [SomeType]()

//以下是创建一个初始化大小数组的语法：
var someArray = [SomeType](repeating: InitialValue, count: NumbeOfElements)

//以下实例创建了一个类型为 Int ，数量为 3，初始值为 0 的空数组：
var someInts = [Int](repeating: 0, count: 3)

var someInts:[Int] = [10, 20, 30]

var arr:[Any] = [1,2,3, "3"]

```
### 访问数组

我们可以根据数组的索引来访问数组的元素，语法如下：

```Swift
var someVar = someArray[index]
```

### 修改数组

可以使用 `append()` 方法或者赋值运算符` += `在数组末尾添加元素

```Swift
var someInts = [Int]()

someInts.append(20)
someInts.append(30)
someInts += [40]

var someVar = someInts[0]

print( "第一个元素的值 \(someVar)" )
print( "第二个元素的值 \(someInts[1])" )
print( "第三个元素的值 \(someInts[2])" )

someInts[2] = 50
print( "第三个元素的值 \(someInts[2])" )

//一次性更改范围值
shoppingList[4...6] = ["Bananas", "Apples"]
//在特定的位置插入item
shoppingList.insert("Maple Syrup", at: 0)

 //移除特定位置的item
 let mapleSyrup = shoppingList.remove(at: 0) // item移除后， subscript会自动去掉空隙，所以，如果删除 0 ， 那么之前的1 就会变成 0 。
//移除最后一个item
 let apples = shoppingList.removeLast()

```

### 遍历数组（Iterating Over an Array）

```Swift
var someStrs = [String]()

someStrs.append("Apple")
someStrs.append("Amazon")
someStrs.append("Runoob")
someStrs += ["Google"]

for item in someStrs {
   print(item)
}

//如果我们同时需要每个数据项的值和索引值，可以使用 String 的 enumerate() 方法来进行数组遍历

for (index, item) in someStrs.enumerated() {
    print("在 index = \(index) 位置上的值为 \(item)")
}

在 index = 0 位置上的值为 Apple
在 index = 1 位置上的值为 Amazon
在 index = 2 位置上的值为 Runoob
在 index = 3 位置上的值为 Google

```

其他： [https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID121](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID121)

### 合并数组

我们可以使用加法操作符（+）来合并两种已存在的相同类型数组。新数组的数据类型会从两个数组的数据类型中推断出来

```Swift
var intsA = [Int](repeating: 2, count:2)
var intsB = [Int](repeating: 1, count:3)

var intsC = intsA + intsB

for item in intsC {
    print(item)
}

```
### 数组属性

#### count属性

使用 count 属性来计算数组元素个数

```Swift
print(arr.count)
```
#### isEmpty属性

```Swift
print(arr.isEmpty) true/false
```
