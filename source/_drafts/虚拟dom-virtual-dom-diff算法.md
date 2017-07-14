---
layout: "post"
title: "虚拟dom(virtual dom)diff算法"
date: "2017-06-23 19:38"
author: "webkong"
categories:
- JavaScript
tags:
- 虚拟dom
- virtual dom
- diff算法
- vue.js
---

> 适用的解往往不是最好的，但是肯定是最平衡的。

### 目录

* Diff算法
* 虚拟Dom
* 虚拟Dom的Diff算法

<!-- more -->
### Diff算法

用过版本控制的同学都见过diff文件，在两个不同的文件之间找差异，在删掉的地方用'-'标识，在新增加的地方用'+'标识(并不是所以的都是-+，但是都有标识新增和删掉的符号)。例如最流行的Git的diff文件(点击查看大图)：

<img src="/images/2017/diff-gitDiff.png" width="100px"/>

如图所示，很明确的就知道两个文件的差异是什么，本次的修改又有哪些。那么diff的原理又是什么呢？

#### LCS

LCS (最长公共子序列, Longest common subsequence problem)。

diff操作，就是基于解决LCS问题， 也就是说，假设给出两个序列：

```
  a b c d f g h j q z   //A

  a b c d e f g i j k r x y z   //B
```
我们的目的是，在这两个序列当中，找到相同顺序的最长序列（we want to find a longest sequence of items that is present in both original sequences in the same order）。注意是相同顺序的序列而`不是连续的序列`。

那么对于上面的两个序列(A、B)来说，他们的最长公共子序列是：

```
  a b c d f g  j  z
```

那么， A对于B序列的差异就可以表示成：

```
  e   h i   q   k r x y
  +   - +   -   + + + +

```
上面， `+`表示新增的字符， `-`表示要删除的字符。

也就是说，如果A序列要变成B序列，要在相应的位置上进行增加和删除操作。

当然我们在平时比较文件的diff时候，会把文件转化成两个列表， 列表的每一个元素就是文件的某一行，所以在上面git的diff文件中会显示每一行的 `-` 和 `+` 。

用js简单实现一个算法：

```javascript
let a = ['a','b','c','d','f','g','h','j','q','z'];
let b = ['a','b','c','d','e','f','g','i','j','k','r','x','y','z'];

let lcs = (a, b) =>{

}
```

### 虚拟dom





















### 参考

[https://en.wikipedia.org/wiki/Diff_utility](https://en.wikipedia.org/wiki/Diff_utility)

[https://en.wikipedia.org/wiki/Longest_common_subsequence_problem](https://en.wikipedia.org/wiki/Longest_common_subsequence_problem)

[https://en.wikipedia.org/wiki/Dynamic_programming](https://en.wikipedia.org/wiki/Dynamic_programming)

[全面理解虚拟DOM，实现虚拟DOM](http://www.ituring.com.cn/article/211352)
