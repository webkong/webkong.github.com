---
layout: "post"
title: "python-内建的一些函数"
date: "2017-07-14 11:03"
author: "webkong"
categories:
- Python
tags:
- 内建函数
- 原创
- any()
- all()
---

> 特权，往往掌握在一部分群体中，内建函数就是有特权的一部分。
> 享受着编译器和虚拟机 (运行时) 深度支持。

<!-- more -->
### any()

`any(iterable) -> bool`

Return True if bool(x) is True for any x in the iterable.
If the iterable is empty, return False.

当迭代对象中任意一个不为False，则返回True。只有当所有x为False，结果才是False。

可用于检测所有的值是不是False，如果有一个是True,则返回True,如果所有的都是False,才返回False。

有点类似于 `或` 。

```
data = (0, 0.0, None, "", list(), tuple(), dict(), set(), frozenset())

any(map(bool, data))

False
```
### all()

`all(iterable) -> bool`

Return True if bool(x) is True for all values x in the iterable.
If the iterable is empty, return True.`

当迭代对象所有的值为True, 则返回True,任意一个不为True,则返回False

有点类似 `与`

```
all([1,2,3,4,5,6])

True

all([0,1,2,3,4,5])

False
```
