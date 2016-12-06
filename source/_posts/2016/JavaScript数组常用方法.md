---
title: JavaScript数组常用方法
date: 2016-12-06 10:36:41
categories:
- JavaScript
tags:
- 数组
- 原创
---
>总是忘了数组的方法，所以备忘一下，记性真是不好
> 一天之计在于晨

#### concat()
连接两个或者更多的数组，并返回结果。 不会改变现有数组，会返回被连接数组的副本。

`arrayObject.concat(arr1,arr2, ... arrx)`

返回值：返回一个新的数组。

```
  var a = [1,2,3]
  console.log(a.concat([4,5]))
  // 1,2,3,4,5
```
#### join()
把数组的所有元素放入一个字符串。元素通过制定的分隔符进行分隔。

`arrayObject.join(separator)` 默认分隔符为','

返回值:返回一个字符串。

```
var arr = [1,2,3,4,5,6,'haha']
console.log(arr.join('.'))
// 1.2.3.4.5.6.haha
```
#### pop()
删除并返回数组的最后一个元素

`arrayObject.pop()`

返回值:arrayObject的最后一个元素
说明：原地操作。 pop()方法将删除arrayObject的最后一个元素，把数组长度减1，并返回删除的元素的值。如果数组为空，则不改变数组，并返回undefined值。

```
var a = [1,2,3]
console.log(a)
console.log(a.pop())
console.log(a)
// [1,2,3]
// 3
// [1,2]
```
#### push()
向数组的末尾添加一个或多个元素，并返回新的长度。

`arrayObject.push(newelement1,newelement2,....,newelementX)`

返回值：元素添加到数组之后的新长度
说明：原地操作。方法可把它的参数顺序添加到 arrayObject 的尾部。它直接修改 arrayObject，而不是创建一个新的数组。push() 方法和 pop() 方法使用数组提供的先进后出栈的功能。unshift()可以在头部添加一个或者多个元素

```
var a = [1,2,3,4,5]
console.log(a)
console.log(a.push(7))
console.log(a)
// [1,2,3,4,5]
// 6
// [1,2,3,4,5,7]
```

#### reverse()
颠倒数组中元素的顺序。

`arrayObject.reverse()`

返回值：颠倒顺序的数组本身
说明：原地操作。直接改变原来的数组，不会创建新的数组。

```
var a = [1,2,3]
console.log(a)
console.log(a.reverse()=== a)
console.log(a)
```

#### slice()
从已有的数组中返回选定的元素。

`arrayObject.slice(start,end)`
start 必须，规定从何处开始选取。如果是负值，那么它规定从数组的为不开始算起的位置。也就是 -1 是最后一个元素，-2 是倒数第二个，以此类推
end 可选，规定从何处结束选取。该参数是数组片段结束处的数组下标。如果没有指定，那么切分的数组包含从start到数组结束的所有元素。如果这个参数是负数，那么它规定的是从数组尾部开始算起的元素。

返回值:返回一个新的数组。包含start到end(不含)的元素

说明：非原地操作，返回子数组。start,end都是元素的下标

```
var a = [1,2,3,4,5,6,7]
console.log(a)
console.log(a.slice(1)) //从下标是1开始选取到最后 [2,3,4,5,6,7]
console.log(a.slice(1,3))//从下标1开始到下标3 [2,3]
console.log(a.slice(1,-2)) //从下标1开始到倒数第二个[2,3,4,5]
console.log(a.slice(-2)) //从倒数第二个开始到最后 [6,7]
console.log(a.(-3,-1)) //从倒数第三个到倒数第一个[5,6]
```
#### splice()
方法向/从数组中添加/删除项目，然后返回被删除的项目。

`arrayObject.splice(index,howmany,item1,.....,itemX)`

index 必须，整数， 规定添加/删除的位置，如果是负数，可从数组结尾处规定位置
howmany 必须，要删除的项目的数量。如果设置为0 ，则不会删除项目。就是删除几个
item,...,itemx 可选，向数组添加的新的项目

说明：原地操作。 splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。
如果从 arrayObject 中删除了元素，则返回的是含有被删除的元素的数组。

```
var a = [1,2,3,4,5];
var b = [];
b = a.slice(0);
console.log(a.splice(1,3,9,8,7,6)) //执行后的返回值 [ 2, 3, 4 ]
console.log(a) //[ 1, 9, 8, 7, 6, 5 ]
console.log(b.splice(1,2))//[ 2, 3 ]
console.log(b)//[ 1, 4, 5 ]

```
#### sort()
用于对数组的元素进行排序。

`arrayObject.sort(sortby)` sortby可选。规定排序顺序。必须是函数。

说明:原地操作。 数组在原数组上进行排序，不生成副本。
如果调用该方法时没有使用参数，将按字母顺序对数组中的元素进行排序，说得更精确点，是按照字符编码的顺序进行排序。要实现这一点，首先应把数组的元素都转换成字符串（如有必要），以便进行比较。
如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：
若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
若 a 等于 b，则返回 0。
若 a 大于 b，则返回一个大于 0 的值。

#### shift()

把数组的第一个元素从其中删除，并返回第一个元素的值。
`arrayObject.shift()`

说明：原地操作。 如果数组是空的，那么 shift() 方法将不进行任何操作，返回 undefined 值。和pop()方法使用相同

#### unshift()
向数组的开头添加一个或更多元素，并返回新的长度。
`arrayObject.unshift(newelement1,newelement2,....,newelementX)`

说明：原地操作。类似push()方法。unshift() 方法将把它的参数插入 arrayObject 的头部，并将已经存在的元素顺次地移到较高的下标处，以便留出空间。该方法的第一个参数将成为数组的新元素 0，如果还有第二个参数，它将成为新的元素 1，以此类推。

#### toString()
把数组转换为字符串，并返回结果。数组中的元素之间用逗号分隔。

`arrayObject.toString()`

说明：arrayObject 的字符串表示。返回值与没有参数的 join() 方法返回的字符串相同。

#### toLocaleString()
把数组转换为本地字符串。
首先调用每个数组元素的 toLocaleString() 方法，然后使用地区特定的分隔符把生成的字符串连接起来，形成一个字符串。

#### valueOf()
返回 Array 对象的原始值。

`arrayObject.valueOf()`

说明：方法通常由 JavaScript 在后台自动调用，并不显式地出现在代码中。

#### indexOf()
indexOf()方法返回给定元素能找在数组中找到的第一个索引值，否则返回-1。

`arr.indexOf(searchElement[, fromIndex = 0])`

searchElement:要查找的元素
fromIndex:开始查找的位置,如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回-1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即-1表示从最后一个元素开始查找，-2表示从倒数第二个元素开始查找 ，以此类推。 注意：如果参数中提供的索引值是一个负值，仍然从前向后查询数组。如果抵消后的索引值仍小于0，则整个数组都将会被查询。其默认值为0.

indexOf 在ECMA-262 标准 的第5版中被加入，但并非所有的浏览器都支持该方法。可以如下使用

```
// Production steps of ECMA-262, Edition 5, 15.4.4.14
// Reference: http://es5.github.io/#x15.4.4.14
if (!Array.prototype.indexOf) {
  Array.prototype.indexOf = function(searchElement, fromIndex) {

    var k;

    // 1. Let O be the result of calling ToObject passing
    //    the this value as the argument.
    if (this == null) {
      throw new TypeError('"this" is null or not defined');
    }

    var O = Object(this);

    // 2. Let lenValue be the result of calling the Get
    //    internal method of O with the argument "length".
    // 3. Let len be ToUint32(lenValue).
    var len = O.length >>> 0;

    // 4. If len is 0, return -1.
    if (len === 0) {
      return -1;
    }

    // 5. If argument fromIndex was passed let n be
    //    ToInteger(fromIndex); else let n be 0.
    var n = +fromIndex || 0;

    if (Math.abs(n) === Infinity) {
      n = 0;
    }

    // 6. If n >= len, return -1.
    if (n >= len) {
      return -1;
    }

    // 7. If n >= 0, then Let k be n.
    // 8. Else, n<0, Let k be len - abs(n).
    //    If k is less than 0, then let k be 0.
    k = Math.max(n >= 0 ? n : len - Math.abs(n), 0);

    // 9. Repeat, while k < len
    while (k < len) {
      // a. Let Pk be ToString(k).
      //   This is implicit for LHS operands of the in operator
      // b. Let kPresent be the result of calling the
      //    HasProperty internal method of O with argument Pk.
      //   This step can be combined with c
      // c. If kPresent is true, then
      //    i.  Let elementK be the result of calling the Get
      //        internal method of O with the argument ToString(k).
      //   ii.  Let same be the result of applying the
      //        Strict Equality Comparison Algorithm to
      //        searchElement and elementK.
      //  iii.  If same is true, return k.
      if (k in O && O[k] === searchElement) {
        return k;
      }
      k++;
    }
    return -1;
  };
}
```
#### isArray()
方法用来判断某个值是否为Array。如果是，则返回 true，否则返回 false。

`Array.isArray(value)`
value 要检测的值

#### every()
every() 方法测试数组的所有元素是否都通过了指定函数的测试。

`arr.every(callback[, thisArg])`

callback:用来测试每个元素的函数。
thisArg:执行 callback 时使用的 this 值。

说明：非原地操作。
every 方法为数组中的每个元素执行一次 callback 函数，直到它找到一个使 callback 返回 false（表示可转换为布尔值 false 的值）的元素。如果发现了一个这样的元素，every 方法将会立即返回 false。否则，callback 为每一个元素返回 true，every 就会返回 true。callback 只会为那些已经被赋值的索引调用。不会为那些被删除或从来没被赋值的索引调用。

callback 被调用时传入三个参数：元素值，元素的索引，原数组。

如果为 every 提供一个 thisArg 参数，在该参数为调用 callback 时的 this 值。如果省略该参数，则 callback 被调用时的 this 值，在非严格模式下为全局对象，在严格模式下传入 undefined。
every 不会改变原数组。

#### filter()
filter() 方法使用指定的函数测试所有元素，并创建一个包含所有通过测试的元素的新数组。

`var new_arrary = arr.filter(callback[, thisArg])`

callback:用来测试数组的每个元素的函数。调用时使用参数 (element, index, array)。
返回true表示保留该元素（通过测试），false则不保留。
thisArg:可选。执行 callback 时的用于 this 的值。

说明：非原地操作。 返回通过filter的元素数组

#### map()
善用map,能有效避免js里面for循环的坑

map() 方法返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组。

`array.map(callback[, thisArg])`
参数说明：

callback, 原数组中的元素经过该方法后返回一个新的元素。
  1. currentValue ,callback 的第一个参数，数组中当前被传递的元素。
  2. index, callback 的第二个参数，数组中当前被传递的元素的索引。
  3. array, callback 的第三个参数，调用 map 方法的数组。
thisArg, 执行 callback 函数时 this 指向的对象。

#### reduce()
reduce() 方法接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始合并，最终为一个值。

`arr.reduce(callback,[initialValue])`
参数说明：
callback, 执行数组中每个值的函数，包含四个参数
  1. previousValue， 上一次调用回调返回的值，或者是提供的初始值（initialValue）
  2. currentValue， 数组中当前被处理的元素
  3. index， 当前元素在数组中的索引
  4. array, 调用 reduce 的数组
initialValue, 作为第一次调用 callback 的第一个参数。

说明：回调函数第一次执行时，previousValue 和 currentValue 的取值有两种情况，如果 initialValue 在调用 reduce 时被提供，那么第一个 previousValue 等于 initialValue ，并且currentValue 等于数组中的第一个值；如果initialValue 未被提供，那么previousValue 等于数组中的第一个值，currentValue等于数组中的第二个值。

```
[0,1,2,3,4].reduce(function(previousValue, currentValue, index, array){
  return previousValue + currentValue;
});
```

previousValue | currentValue | index | array | return      | value
--------------|--------------|-------|-------|-------------|------
first call    | 0            | 1     | 1     | [0,1,2,3,4] | 1
second call   | 1            | 2     | 2     | [0,1,2,3,4] | 3
third call    | 3            | 3     | 3     | [0,1,2,3,4] | 6
fourth call   | 6            | 4     | 4     | [0,1,2,3,4] | 10


### ES6中的方法

#### from()
可以将一个类数组对象或可遍历对象转换成真正的数组。

#### copyWithin()
会浅拷贝数组的部分元素到同一数组的不同位置，且不改变数组的大小，返回该数组。

#### Array.of()
Array.of() 方法会将它的任意类型的多个参数放在一个数组里并返回。

 Array.of() 和 Array 构造函数不同的是：在处理数值类型的参数时，Array.of(42) 创建的数组只有一个元素，即 42, 但 Array(42) 创建了42个元素，每个元素都是undefined。

 `Array.of(element0[, element1[, ...[, elementN]]])`
#### fill()
使用 fill() 方法，可以将一个数组中指定区间的所有元素的值, 都替换成或者说填充成为某个固定的值。

`arr.fill(value[, start = 0[, end = this.length]])`
value:用来填充数组元素的值。
start:可选，开始索引。
end:可选，结束索引。
说明：原地操作。具体要填充的元素区间是 [start, end) , 一个半开半闭区间.
如果 start 是个负数, 则开始索引会被自动计算成为 length+start, 其中 length 是 this 对象的 length 属性值. 如果 end 是个负数, 则结束索引会被自动计算成为 length+end.

fill 方法故意被设计成通用方法, 也就是说它不需要 this 值必须是个数组对象, 类数组对象也是可以调用该方法的 .

fill 方法是个可变方法, 它会改变调用它的 this 对象本身, 然后返回它, 而并不是返回一个副本.


#### find()
如果数组中某个元素满足测试条件，find() 方法就会返回那个元素的第一个值，如果没有满足条件的元素，则返回 undefined。
区别：findIndex()方法返回的是满足条件的元素的索引，而非它的值。

`arr.find(callback[, thisArg])`

参数说明:
callback
在数组每一项上执行的函数，接收 3 个参数：
  1. element, 当前遍历到的元素。
  2. index, 当前遍历到的索引。
  3. array, 数组本身。
thisArg
可选，指定 callback 的 this 参数。






#### 使用技巧

##### slice()方法复制数组，看前面的例子

##### push()和pop()可以实现先进后出栈的原理

##### push()和shift()可以实现先进先出队列的原理

>后续继续添加

### 参考：

[MDN-JavaScript标准库](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)
[w3school JavaScript Array 对象](http://www.w3school.com.cn/jsref/jsref_obj_array.asp)
