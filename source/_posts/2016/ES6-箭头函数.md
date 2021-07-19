---
layout: "post"
title: "ES6-箭头函数"
date: "2016-09-26 17:48"
categories:
- JavaScript
tags:
- ES6
---
**ES6 允许使用箭头（=>）来定义函数**

### 语法

1. 具有一个参数的简单函数

```
var f = a => a;
//相当于
var f = function(a){
  return a;
}
```

2. 没有参数或者参数为多个，用`()`

```
//没有参数
var f = () => console.log('没有参数');
//多个参数
var f = (a,b,c) => console.log(a+b+c);
```
相当于
```
var f = function(){console.log('没有参数');}

var f = function(a,b,c){console.log(a+b+c);}
```
3. 如果箭头函数代码块多于一条语句，用`{}`括起来

```
var f = (a,b,c) => {
  let d = a + b + c ;
  console.log(c);
}
```
4. 如果返回为对象时，对象外面要用`()`括起来，因为大括号会被解释成代码块

```
var f = a => ({id:a,name:'wekbong'});
//
var f = a => {
  //something
  return({
    id:a,
    name:'webkong'
    })
};
```
5. 直接作为事件的handler

```
document.addEventLister('click',event=>{
    console.log(event)
  });
```

6. 箭头函数可嵌套,箭头函数内部可以使用箭头函数

```
let insert = (value) => ({into:(array)}) =>({after:(afterValue)}) => {
  array.splice(array.indexOf(afterValue)) + 1,0,value);
  return array;
}})});
//相当于

function insert(value) {
  return {into: function (array) {
    return {after: function (afterValue) {
      array.splice(array.indexOf(afterValue) + 1, 0, value);
      return array;
    }};
  }};
}

```

### 注意点

箭头函数的几个使用注意点

1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。
4. 不可以使用yield命令，因此箭头函数不能用作Generator函数。

第一点尤其值得注意。this 对象的志向是可变的，但是在箭头函数中它是固定的。
```
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 });
```
上面代码中，setTimeout的参数是一个箭头函数，这个箭头函数的定义生效是在foo函数生成时，而它的真正执行要等到100毫秒后。如果是普通函数，执行时this应该指向全局对象window，这时应该输出21。但是，箭头函数导致this总是指向函数定义生效时所在的对象（本例是{id: 42}），所以输出的是42。

```
function Timer() {
  this.s1 = 0;
  this.s2 = 0;
  // 箭头函数
  setInterval(() => this.s1++, 1000);
  // 普通函数
  setInterval(function () {
    this.s2++;
  }, 1000);
}

var timer = new Timer();

setTimeout(() => console.log('s1: ', timer.s1), 3100);
setTimeout(() => console.log('s2: ', timer.s2), 3100);
// s1: 3
// s2: 0
```
上面代码中，Timer函数内部设置了两个定时器，分别使用了箭头函数和普通函数。前者的this绑定定义时所在的作用域（即Timer函数），后者的this指向运行时所在的作用域（即全局对象）。所以，3100毫秒之后，timer.s1被更新了3次，而timer.s2一次都没更新。

箭头函数可以让this指向固定化，这种特性很有利于封装回调函数。下面是一个例子，DOM事件的回调函数封装在一个对象里面。

```
var handler = {
  id: '123456',

  init: function() {
    document.addEventListener('click',
      event => this.doSomething(event.type), false);
  },

  doSomething: function(type) {
    console.log('Handling ' + type  + ' for ' + this.id);
  }
};
```
上面代码的init方法中，使用了箭头函数，这导致这个箭头函数里面的this，总是指向handler对象。否则，回调函数运行时，this.doSomething这一行会报错，因为此时this指向document对象。

this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。
