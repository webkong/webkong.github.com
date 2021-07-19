---
title: JavaScript强制类型转换
date: 2016-01-24 11:46:55
categories:
- JavaScript
tags:
- JavaScript
- 类型转换
- 原创
---

Javascript的变量是松散类型的，它可以存储Javascript支持的任何数据类型，其变量的类型可以在运行时被动态改变。

正因为Javascript中变量类型具有动态性，在程序实际执行的过程中就需要用到类型转换的概念。类型转换可以分为隐式转换和显式转换，所谓隐 式转换即程序在运行时进行的自动转换，显式转换则是人为的对类型进行强制转换。

>Javascript提供了以下转型函数：
>转换为数值类型：Number(mix)、parseInt(string,radix)、parseFloat(string)
>转换为字符串类型：toString(radix)、String(mix)
>转换为布尔类型：Boolean(mix)

* Number(mix)函数，可以将任意类型的参数mix转换为数值类型。其规则为：
```
  Boolean->Number : true,false --> 1,0
  Number->Number : 返回其本身
  undefined->Number : undefined --> NaN
  String->Number :
    + 如果字符串中只包含数字，则将其转换为十进制（忽略前导0）
    + 如果字符串中包含有效的浮点格式，将其转换为浮点数值（忽略前导0）
    + 如果是空字符串，将其转换为0
    + 如果字符串中包含非以上格式，则将其转换为NaN
    + 如果是对象，则调用对象的valueOf()方法，然后依据前面的规则转换返回的值。如果转换的结果是NaN，则调用对象的toString()方法，再次依照前面的规则转换返回的字符串值。
```

    下表列出了对象的valueOf()的返回值：

| 对象  | 返回值 |
| ---  |:----:|
| Array  | 数组的元素被转换为字符串，这些字符串由逗号分隔，连接在一起。其操作与 Array.toString 和 Array.join 方法相同。    |
| Boolean    | Boolean 值。    |
| Date    | 存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。    |
| Function    | 函数本身。    |
| Number    | 数字值。    |
| Object    | 对象本身。这是默认情况。   |
| String    | 字符串值。    |

* parseInt(string, radix)函数，将字符串转换为整数类型的数值。它也有一定的规则：
```
 忽略字符串前面的空格，直至找到第一个非空字符
 如果第一个字符不是数字符号或者负号，返回NaN
 如果第一个字符是数字，则继续解析直至字符串解析完毕或者遇到一个非数字符号为止
 如果上步解析的结果以0开头，则将其当作八进制来解析；如果以0x开头，则将其当作十六进制来解析
 如果指定radix参数，则以radix为基数进行解析
```
* parseFloat(string)函数，将字符串转换为浮点数类型的数值。
```
  它的规则与parseInt基本相同，但也有点区别：
  字符串中第一个小数点符号是有效的，另外parseFloat会忽略所有前导0，如果字符串包含一个可解析为整数的数，则返回整数值而不是浮点数值。
```
* toString(radix)方法。除undefined和null之外的所有类型的值都具有toString()方法，其作用是返回对象的字符串表示。

| 对象 |	操作 |
| -- |:----: |
| Array |	将 Array 的元素转换为字符串。结果字符串由逗号分隔，且连接起来。|
| Boolean |	如果 Boolean 值是 true，则返回 “true”。否则，返回 “false”。|
| Date |	返回日期的文字表示法。|
| Error |	返回一个包含相关错误信息的字符串。|
| Function |	返回如下格式的字符串，其中 functionname 是被调用 toString 方法函数的名称：|
| function | functionname( ) { [native code] }|
| Number |	返回数字的文字表示。|
| String |	返回 String 对象的值。|

默认	返回 “[object objectname]”，其中 objectname 是对象类型的名称。

* String(mix)函数，将任何类型的值转换为字符串，其规则为：
```
如果有toString()方法，则调用该方法（不传递radix参数）并返回结果
如果是null，返回”null”
如果是undefined，返回”undefined”
```
* Boolean(mix)函数，将任何类型的值转换为布尔值。

```
以下值会被转换为false：fals* ”"、0、NaN、null、undefined，其余任何值都会被转换为true。
```
