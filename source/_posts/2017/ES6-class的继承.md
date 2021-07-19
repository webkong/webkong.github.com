---
layout: "post"
title: "ES6-Class的继承"
date: "2017-06-22 14:27"
author: "webkong"
categories:
- JavaScript
tags:
- ES6
- class类
- 继承
---

> 他们问我坚持了这么久是为了什么 ?
> 我说我没有坚持因为喜欢所以快乐
> 很多人一辈子忙忙碌碌不会懂得：
> 有个被嘲笑的梦想万一有天实现了呢？

类：就是对象的抽象。
对象：就是类的实例。
子类：继承自父类，又各有特长。龙生九子，各不成龙。

<!-- more -->
`extends`关键词被用在类声明或者类表达式上，以创建一个类是另一个类的子类。实现继承，比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

#### 使用extends
```JavaScript
class Square extends Polygon {
  constructor(length) {
    // 这里把length传参给父类的构造方法
    // 作为父类Polygon的宽和高
    super(length, length);
    // 备注：在衍生类中使用this前必须先调用super()方法
    // 忽视这一点将会导致一个引用错误
    this.name = 'Square';
  }
  get area() {
    return this.height * this.width;
  }
  set area(value) {
    this.area = value;
  }
}
```
根据Polygon类创建一个名为Square的类，也就是说，Square继承自Polygon。
`super`关键字，它在这里表示父类的构造函数，用来新建父类的this对象。


#### 使用extends扩展内建对象

```JavaScript
class myDate extends Date {
  constructor() {|
    super();
  }

  getFormattedDate() {
    var months = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
    return this.getDate() + "-" + months[this.getMonth()] + "-" + this.getFullYear();
  }
}
```
这个myDate继承自Date，并新增getFormattedDate方法。

>注：只有调用super之后，才可以使用this关键字，否则会报错。这是因为子类实例的构建，是基于对父类实例加工，只有super方法才能返回父类实例。

#### 获取父类

`Object.getPrototypeOf`方法可以用来从子类上获取父类。可以使用这个方法判断，一个类是否继承了另一个类。


#### super关键字


`super`这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。

第一种情况，super作为函数调用时，代表父类的构造函数。`ES6`要求，子类的构造函数必须执行一次`super`函数。

`super`虽然代表了父类A的构造函数，但是返回的是子类B的实例，即`super`内部的`this`指的是`B`，因此`super()`在这里相当于`A.prototype.constructor.call(this)`。

```JavaScript
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

第二种情况，super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

```JavaScript
class A {
  p() {
    return 2;
  }
}

class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}

let b = new B();

```

上面代码中，子类B当中的`super.p()`，就是将`super`当作一个对象使用。这时，`super`在普通方法之中，指向`A.prototype`，所以`super.p()`就相当于`A.prototype.p()`。

这里需要注意，由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。

```JavaScript
class A {
  constructor() {
    this.p = 2;
  }
}

class B extends A {
  get m() {
    return super.p;
  }
}

let b = new B();
b.m // undefined
```

上面代码中，p是父类A实例的属性，super.p就引用不到它。

如果属性定义在父类的原型对象上，super就可以取到。

```JavaScript
class A {}
A.prototype.x = 2;

class B extends A {
  constructor() {
    super();
    console.log(super.x) // 2
  }
}

let b = new B();
```

上面代码中，属性x是定义在A.prototype上面的，所以super.x可以取到它的值。

ES6 规定，通过super调用父类的方法时，super会绑定子类的this。

```JavaScript
class A {
  constructor() {
    this.x = 1;
  }
  print() {
    console.log(this.x);
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
  }
  m() {
    super.print();
  }
}

let b = new B();
b.m() // 2
```
上面代码中，super.print()虽然调用的是A.prototype.print()，但是A.prototype.print()会绑定子类B的this，导致输出的是2，而不是1。也就是说，实际上执行的是super.print.call(this)。

由于绑定子类的this，所以如果通过super对某个属性赋值，这时super就是this，赋值的属性会变成子类实例的属性。

```JavaScript
class A {
  constructor() {
    this.x = 1;
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
    super.x = 3;
    console.log(super.x); // undefined
    console.log(this.x); // 3
  }
}

let b = new B();
```
上面代码中，super.x赋值为3，这时等同于对this.x赋值为3。而当读取super.x的时候，读的是A.prototype.x，所以返回undefined。

如果super作为对象，用在静态方法之中，这时super将指向父类，而不是父类的原型对象。

```JavaScript
class Parent {
  static myMethod(msg) {
    console.log('static', msg);
  }

  myMethod(msg) {
    console.log('instance', msg);
  }
}

class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg);
  }

  myMethod(msg) {
    super.myMethod(msg);
  }
}

Child.myMethod(1); // static 1

var child = new Child();
child.myMethod(2); // instance 2
```
上面代码中，super在静态方法之中指向父类，在普通方法之中指向父类的原型对象。






#### 参考

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes/extends](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes/extends)


[http://es6.ruanyifeng.com/#docs/class-extends](http://es6.ruanyifeng.com/#docs/class-extends)
