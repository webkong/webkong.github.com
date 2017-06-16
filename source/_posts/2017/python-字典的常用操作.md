---
layout: "post"
title: "python-字典的常用操作"
date: "2017-06-15 11:02"
author: "webkong"
categories:
- Python
tags:
- dict
- 原创
---

>罗列字典的常用操作，遵循艾宾浩斯记忆遗忘曲线，让知识固化，方便查询和使用。

<!-- more -->

### 创建dict

#### dict函数

dict函数可以通过其他映射，比如字典或者`(key,value)`遮这样的序列对建立字典。

```
>>> items = [('name','webkong'),('age',18)]
>>> d = dict(items)
>>> d
{'age': 18, 'name': 'webkong'}
```

或者通过关键字参数来创建

```
>>> d = dict(name='webkong',age=18)
>>> d
{'age': 18, 'name': 'webkong'}

```

如果不带参数，就会返回空字典. 和 list, tuple, str函数一样。

```
>>> a = dict()
>>> a
{}

```
也可以直接用字面量

```
>>> c = {'name':'webkong','age':19}
>>> c
{'age': 19, 'name': 'webkong'}
```
### 基本字典操作

#### len()

返回键值对的数量

#### del

del a['name']  #删除键为name的值

#### in

'name' in a 返回 True 检查字典中是否含有键为name的项

#### 赋值

a['name'] = 'wsw'


> 键的类型： 字典的键是不可变类型，有可能是整型、浮点型（实型）、字符串或元组。
> 添加：键不存在，也可以赋值，这样字典就会建立新的项。


### 字典格式化字符串

```
>>> info
{'age': 19, 'name': 'webkong'}
>>> "%(name)s's age is %(age)s." % info
"webkong's age is 19."
```
只要给出的键可以在字典中找到，就可以使用任意数量的转换说明符。在模板系统中非常有用。


### 字典方法

#### clear

原地操作， 清除字典中的所有项， 无返回值（或者说返回None）。

#### copy

返回一个具有相同键值对的新字典。这种方法实现的是浅复制，因为值本身是相同的，而不是副本。

```
>>> info
{'skills': ['html', 'css', 'js'], 'age': 19, 'name': 'webkong'}
>>> a = info.copy()
>>> a
{'skills': ['html', 'css', 'js'], 'age': 19, 'name': 'webkong'}
>>> a['name'] = 'webkong1'
>>> a
{'skills': ['html', 'css', 'js'], 'age': 19, 'name': 'webkong1'}
>>> info
{'skills': ['html', 'css', 'js'], 'age': 19, 'name': 'webkong'}
>>> a['skills'].remove('html')
>>> a
{'skills': ['css', 'js'], 'age': 19, 'name': 'webkong1'}
>>> info
{'skills': ['css', 'js'], 'age': 19, 'name': 'webkong'}
```
在替换值得时候，原字典不受影响， 但是如果用原地操作修改了某个值，原字典也会改变。

避免这种问题的一种方式就是深复制(deep copy)

```
>>> from copy import deepcopy
>>> info
{'skills': ['css', 'js'], 'age': 19, 'name': 'webkong'}
>>> i = deepcopy(info)
>>> f = info.copy()
>>> f['skills'].append('js')
>>> f
{'skills': ['css', 'js', 'js'], 'age': 19, 'name': 'webkong'}
>>> i
{'skills': ['css', 'js'], 'age': 19, 'name': 'webkong'}
```

#### fromkeys

使用给定的键建立新的字典，每一个键默认的值是None.

```
>>> a = {}.fromkeys(['name','age'])
>>> a
{'age': None, 'name': None}
```

#### get

访问字典项的方法，在访问不存在的项时，返回None。也可以自己定义“默认值”

```
>>> a
{'age': None, 'name': None}
>>> a.get('kk','NNN')
'NNN'
```
#### has_key

检查字典中是否又给出的键。

```
a.has_key('name') 相当于 k in d

```

#### items & iteritems

items 方法将所有的字典项以列表的方式返回，list中的每一个项来自(key, value)，返回时没有次序。

```
 info
{'skills': ['css', 'js', 'js'], 'age': 19, 'name': 'webkong'}
>>> info.items()
[('skills', ['css', 'js', 'js']), ('age', 19), ('name', 'webkong')]
```

iteritems 方法和items大致相同，但是会返回一个迭代器对象。


#### keys & iterkeys

keys方法将字典中的键以列表的形式返回， iterkeys返回迭代器对象。

```
>>> info
{'skills': ['css', 'js', 'js'], 'age': 19, 'name': 'webkong'}
>>> info.keys()
['skills', 'age', 'name']

```
#### pop

pop方法用来获取对应键的值，然后将这个键值对从字典中移除。

```
>>> info
{'skills': ['css', 'js', 'js'], 'age': 19, 'name': 'webkong'}
>>> d = info.pop('name')
>>> d
'webkong'
>>> info
{'skills': ['css', 'js', 'js'], 'age': 19}
```
#### popitem

popitem 类似于list.pop，但不同的是，popitem弹出的是随机项，因为字典没有最后项的概念。 若想一个接一个地移除并处理项，这个方法就很适合，不用首先获取键的列表。

```
a.popitem()
```


#### setdefault

setdefault可以在字典不包含给定键值对的情况下设置相应的键值。
```
>>> b = {}
>>> b.setdefault('name','N/A')
'N/A'
>>> b
{'name': 'N/A'}
>>> b['name'] = 'webkong'
>>> b
{'name': 'webkong'}
>>> b.setdefault('name','N/A')
'webkong'
>>> b
{'name': 'webkong'}
```
#### update

update 方法可以利用一个字典项更新另外一个字典。

```
>>> a
{'skills': ['css', 'js', 'js'], 'age': 19, 'name': 'webkong'}
>>> x = {'age':18}
>>> a.update(x)
>>> a
{'skills': ['css', 'js', 'js'], 'age': 18, 'name': 'webkong'}
>>> y = {'kk':'kkkk'}
>>> a.update(y)
>>> a
{'skills': ['css', 'js', 'js'], 'kk': 'kkkk', 'age': 18, 'name': 'webkong'}
```

#### values & itervalues

values 方法以列表的形式返回字典的值， 返回的值可以包含重复的元素。 itervalues 返回一个迭代对象。

```
>>> a
{'skills': ['css', 'js', 'js'], 'kk': 'kkkk', 'age': 18, 'name': 'webkong'}
>>> a.values()
[['css', 'js', 'js'], 'kkkk', 18, 'webkong']
```
