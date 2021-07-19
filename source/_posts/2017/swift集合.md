---
layout: "post"
title: "swift集合"
date: "2017-09-23 11:05"
author: "webkong"
categories:
- Swift
tags:
- 集合
---

>参考:
[https://developer.apple.com/](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105)

>集合中存储相同类型的不同值, 集合中没有定义的顺序。当项目的顺序不重要时，或者当您需要确保某个项目只显示一次时，可以使用集合而不是数组。set中的值是unique的。

<!-- more -->


### 创建一个空set

```swift
var letters = Set<Character>()
print("letters is of type Set<Character> with \(letters.count) items.")
// Prints "letters is of type Set<Character> with 0 items."


```

### 创建一个数组文字的集合

```swift
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
```
### 访问和修改set

#### insert

```swift
favoriteGenres.insert("Jazz")
```
#### remove

```swift
favoriteGenres.remove("Rock") // 返回remove的item， 如果没有相应的item，返回nil
```
#### removeAll

```swift
favoriteGenres.removeAll()
```

#### contains

```swift
//是否set中包含特定的item
if favoriteGenres.contains("Funk") {
    print("I get up on the good foot.")
} else {
    print("It's too funky in here.")
}
// Prints "It's too funky in here."


```

### 遍历set

```swift
for genre in favoriteGenres {
    print("\(genre)")
}
// Jazz
// Hip hop
// Classical

//set是无序的，如果要按照某中order， 可以使用sorted()， 返回一个排序的array

for genre in favoriteGenres.sorted() {
    print("\(genre)")
}
// Classical
// Hip hop
// Jazz

```

### set 属性

#### count

```swift
print("I have \(favoriteGenres.count) favorite music genres.")
```
#### isEmpty

```swift
if favoriteGenres.isEmpty {
    print("As far as music goes, I'm not picky.")
} else {
    print("I have particular music preferences.")
}
```

### set 操作

可以高效地完成Set的一些基本操作，比如把两个集合组合到一起，判断两个集合共有元素，或者判断两个集合是否全包含，部分包含或者不相交。

#### intersection

两个集合的交集(common values)

#### symmetricDifference

不包含两个集合交集的部分的集合

#### union

两个集合的并集

#### subtracting

根据不在该集合中的值创建一个新的集合

![](/images/2017/swift-set.png)

```swift

let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sorted()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted()
// []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
// [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
// [1, 2, 9]

```

### 集合的关系(成员、相等)
下面的图描述了三个集合-a,b和c,以及通过重叠区域表述集合间共享的元素。集合a是集合b的父集合，因为a包含了b中所有的元素，相反的，集合b是集合a的子集合，因为属于b的元素也被a包含。集合b和集合c彼此不关联，因为它们之间没有共同的元素。

![](/images/2017/swift-set2.png)

#### 是否相等

通过`==`来判断两个集合是否包含完全相同的值。

#### 子集关系

`isSubset(of:)` 判断是不是某个集合的子集

#### 父集关系

`isSuperset(of:)` 判断是不是某个集合的父集

#### 真子集

`isStrictSubset(of:)` 判断是不是一个集合的子集，并且两个集合不相同

#### 真父级

`isStrictSuperset(of:)` 判断是不是一个集合的父集，并且两个集合不相同

#### 没有交集

`isDisjoint(with:) `
