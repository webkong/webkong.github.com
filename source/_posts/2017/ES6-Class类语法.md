---
layout: "post"
title: "ES6-Class基本语法"
date: "2017-06-19 15:24"
author: "webkong"
categories:
- JavaScript
tags:
- ES6
- 类class
---

>身为一个以JavaScript作为主语言的码农，怎能不更新...


在ES6中，引入了Class类的概念，更接近于传统语言的写法，通过关键字`class`关键字来定义。简述基本语法和注意的点。


主要内容和demo来自 阮一峰老师的这篇 [Class 的基本语法](http://es6.ruanyifeng.com/#docs/class)

<!-- more -->
### ES5模拟的类

[JavaScript定义class的三种方法](https://blog.webkong.cn/2014/JavaScript%E5%AE%9A%E4%B9%89%E7%B1%BB)

在ES5没有类概念的时候，模拟类的几种常用的办法，其中构造函数法是最经典也是必会方法。通过在构造函数的Prototype对象上添加属性和方法来构造类。

```JavaScript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);

```
其他就不再赘述。


### ES6中的"类"（Class）

#### 基础语法

基本上， ES6的`class`可以看做一个语法糖，它的绝大部分功能，ES5都能做到，新的`class`只是让对象原型的写法更清晰、更想面向对象的编程的语法而已。

```JavaScript
//定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

上面定义了一个类，可以看到有一个`constructor`方法，这就是构造方法，而`this`关键字代表实例对象。还定义了一个`toString`的方法。

ES6的类，完全可以看做构造函数的另一种写法。

```JavaScript
class Point {
  // ...
}

typeof Point // "function"
Point === Point.prototype.constructor // true
```
这说明，类的数据类型是函数，类本身就是只想构造函数。

使用的时候，也是直接对类使用`new`命令，跟构造函数的用法一致。

构造函数的`prototype`属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的`prototype`属性上面。

```JavaScript
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};

let p = new Point(1,2);

p.constructor === Point.prototype.constructor // true
```

`p`是`Point`类的实例，它的`constructor`方法就是`B`类原型上的`constructor`方法。

由于类的方法都定义在`prototype`对象上面，所以类的新的方法可以添加在`prototype`对象上面。 `Object.assign`方法可以一次向类添加多个方法。

所以，可以看出，ES6的class只是对ES5模拟类的一个语法糖。


但是， 这样定义的类的内部的方法，是不可枚举的，与ES5的行为不一致。

还有一点，类的属性名可以采用表达式。

```JavaScript
let methodName = 'getArea';

class Square {
  constructor(length) {
    // ...
  }

  [methodName]() {
    // ...
  }
}
```

#### 严格模式
类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。

#### constructor方法

`constructor`方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

```JavaScript
class Point {
}

// 等同于
class Point {
  constructor() {}
}
```
上面代码中，定义了一个空的类Point，JavaScript 引擎会自动为它添加一个空的`constructor`方法。

`constructor`方法默认返回实例对象（即this），完全可以指定返回另外一个对象。

```JavaScript
class Foo {
  constructor() {
    return Object.create(null);
  }
}

new Foo() instanceof Foo
// false

```
上面代码中，`constructor`函数返回一个全新的对象，结果导致实例对象不是Foo类的实例。

类必须使用new调用，否则会报错。


#### 类的实例对象

通过`new`命令来生成实例对象。

与ES5一样，实例的属性除非显式定义在其本身(即定义在this对象上)，否则都是定义在原型上（即定义在class上）。

```JavaScript
//定义类
class Point {

  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

}

var point = new Point(2, 3);

point.toString() // (2, 3)

point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true

```
上面代码中，`x`和`y`都是实例对象`point`自身的属性（因为定义在this变量上），所以`hasOwnProperty`方法返回`true`，而`toString`是原型对象的属性（因为定义在Point类上），所以`hasOwnProperty`方法返回`false`。

与 ES5 一样，类的所有实例共享一个原型对象。

```JavaScript
var p1 = new Point(2,3);
var p2 = new Point(3,2);

p1.__proto__ === p2.__proto__
//true
```
上面代码中，p1和p2都是`Point`的实例，它们的原型都是`Point.prototype`，所以`__proto__`属性是相等的。


这也意味着，可以通过实例的__proto__属性为“类”添加方法。

```JavaScript
var p1 = new Point(2,3);
var p2 = new Point(3,2);

p1.__proto__.printName = function () { return 'Oops' };

p1.printName() // "Oops"
p2.printName() // "Oops"

var p3 = new Point(4,2);
p3.printName() // "Oops"
```

上面代码在p1的原型上添加了一个printName方法，由于p1的原型就是p2的原型，因此p2也可以调用这个方法。而且，此后新建的实例p3也可以调用这个方法。这意味着，使用实例的`__proto__`属性改写原型，必须相当谨慎，不推荐使用，因为这会改变“类”的原始定义，影响到所有实例。

#### class表达式

与函数一样，类也可以使用表达式的形式定义。

```JavaScript
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```
上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`MyClass`而不是`Me`，`Me`只在 `Class` 的内部代码可用，指代当前类。

```JavaScript
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```
所以，可以看出，`Me`只在Class内部有定义。

如果类的内部没有用到，可以省略`Me`，也就是：

```JavaScript
const MyClass = class { /* ... */ };
```
采用 Class 表达式，可以写出立即执行的 Class。

```JavaScript
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"
```
person是一个立即执行的类的实例。


#### 没有变量提升(hoist)

类不存在变量提升（hoist），这一点与 ES5 完全不同。

```JavaScript
let f = new Foo();
class Foo {}

ReferenceError: Foo is not defined
    at Object.<anonymous> (/Users/xxx/www/node/ES6/class.js:1:75)
    at Module._compile (module.js:570:32)
    at Object.Module._extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module._load (module.js:438:3)
    at Module.runMain (module.js:604:10)
    at run (bootstrap_node.js:394:7)
    at startup (bootstrap_node.js:149:9)
    at bootstrap_node.js:509:3
```

`Foo`类使用在前，定义在后，这样就会报错，应为ES6不会把类的声明提升到代码头部。这种规定的原因和继承有关，必须保证子类在父类之后定义。

```JavaScript
  let Foo = class {};
  class Bar extends Foo {
  }
```

上面的代码不会报错，因为`Bar`继承`Foo`的时候，`Foo`已经有定义了。但是，如果存在`class`的提升，上面代码就会报错，因为`class`会被提升到代码头部，而`let`命令是不提升的，所以导致`Bar`继承`Foo`的时候，`Foo`还没有定义。

所以是不能有变量提升的。

#### 私有方法

私有方法是类的常见需求，但 ES6 没有提供，只能通过模拟实现。

一种做法是在命名上加以区别。

```JavaScript
class Widget {

  // 公有方法
  foo (baz) {
    this._bar(baz);
  }

  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }

  // ...
}
```

上面代码中，`_bar`方法前面的下划线，表示这是一个只限于内部使用的私有方法。但是，这种命名是不保险的，在类的外部，还是可以调用到这个方法。

另一种方法就是索性将私有方法移出模块，因为模块内部的所有方法都是对外可见的.

```JavaScript
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }

  // ...
}

function bar(baz) {
  return this.snaf = baz;
}
```

还有一种方法是利用Symbol值的唯一性，将私有方法的名字命名为一个Symbol值。

```JavaScript
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
};
```

上面代码中，bar和snaf都是Symbol值，导致第三方无法获取到它们，因此达到了私有方法和私有属性的效果。

#### 私有属性

与私有方法一样，ES6 不支持私有属性。目前，有一个提案，为class加了私有属性。方法是在属性名之前，使用#表示。

```JavaScript
class Point {
  #x;

  constructor(x = 0) {
    #x = +x; // 写成 this.#x 亦可
  }

  get x() { return #x }
  set x(value) { #x = +value }
}
```

上面代码中，#x就表示私有属性x，在Point类之外是读取不到这个属性的。还可以看到，私有属性与实例的属性是可以同名的（比如，#x与get x()）。

私有属性可以指定初始值，在构造函数执行时进行初始化。

```JavaScript
class Point {
  #x = 0;
  constructor() {
    #x; // 0
  }
}
```
之所以要引入一个新的前缀#表示私有属性，而没有采用private关键字，是因为 JavaScript 是一门动态语言，使用独立的符号似乎是唯一的可靠方法，能够准确地区分一种属性是否为私有属性。另外，Ruby 语言使用@表示私有属性，ES6 没有用这个符号而使用#，是因为@已经被留给了 Decorator。

该提案只规定了私有属性的写法。但是，很自然地，它也可以用来写私有方法。

```JavaScript
  class Foo {
  #a;
  #b;
  #sum() { return #a + #b; }
  printSum() { console.log(#sum()); }
  constructor(a, b) { #a = a; #b = b; }
}
```
#### this的指向

类的方法内部如果含有this，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错。

```JavaScript
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
const { printName } = logger;
printName();
```

上面代码中，printName方法中的this，默认指向Logger类的实例。但是，如果将这个方法提取出来单独使用，this会指向该方法运行时所在的环境，因为找不到print方法而导致报错。

一个比较简单的解决方法是，在构造方法中绑定this，这样就不会找不到print方法了。

```JavaScript
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }

  // ...
}
```

另一种解决方法是使用箭头函数。因为箭头函数this指向定义的对象。

```JavaScript
class Logger {
  constructor() {
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
  }

  // ...
}
```

还有一种解决方法是使用Proxy，获取方法的时候，自动绑定this。

```JavaScript
function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}

const logger = selfish(new Logger());
```

#### class的getter和setter

与 ES5 一样，在“类”的内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

```JavaScript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```

上面代码中，prop属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

存值函数和取值函数是设置在属性的 Descriptor 对象上的。

```JavaScript
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }

  get html() {
    return this.element.innerHTML;
  }

  set html(value) {
    this.element.innerHTML = value;
  }
}

var descriptor = Object.getOwnPropertyDescriptor(
  CustomHTMLElement.prototype, "html"
);

"get" in descriptor  // true
"set" in descriptor  // true

```

上面代码中，存值函数和取值函数是定义在html属性的描述对象上面，这与 ES5 完全一致。

#### class的Generater

如果某个方法之前加上星号`（\*）`，就表示该方法是一个 Generator 函数。

```JavaScript
class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {
    for (let arg of this.args) {
      yield arg;
    }
  }
}

for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world
```
上面代码中，Foo类的Symbol.iterator方法前有一个星号，表示该方法是一个 Generator 函数。Symbol.iterator方法返回一个Foo类的默认遍历器，for...of循环会自动调用这个遍历器。


#### class静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```JavaScript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```
上面代码中，Foo类的classMethod方法前有static关键字，表明该方法是一个静态方法，可以直接在Foo类上调用（Foo.classMethod()），而不是在Foo类的实例上调用。如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

父类的静态方法，可以被子类继承。

```JavaScript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

上面代码中，父类Foo有一个静态方法，子类Bar可以调用这个方法。

静态方法也是可以从super对象上调用的。

```JavaScript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

Bar.classMethod() // "hello, too"
```
#### class静态属性和示例属性

静态属性指的是 Class 本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性。

```JavaScript
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```

上面的写法为Foo类定义了一个静态属性prop。

目前，只有这种写法可行，因为 ES6 明确规定，Class 内部只有静态方法，没有静态属性。

```JavaScript
// 以下两种写法都无效
class Foo {
  // 写法一
  prop: 2

  // 写法二
  static prop: 2
}

Foo.prop // undefined
```
提案不在赘述

new.target 不再赘述


























### 来源和参考


[https://curiosity-driven.org/private-properties-in-javascript](https://curiosity-driven.org/private-properties-in-javascript)

[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
