---
title: jQuery的promise
date: 2016-07-21 23:35:08
categories:
- JavaScript
tags:
- JavaScript
- jQuery
- 原创
---

本文粗浅的讨论一下`promise`。仅一家之言，不能尽信之。
在现在流行的单页面应用SPA构建中，难免会有AJAX异步请求数据进行视图的渲染。这时候就会遇到在页面渲染的时候，数据还没有请求过来的情况，就会导致页面缺失或者是页面渲染失败。或者是在某个特定返回之后才能执行某些`methods`。
有人说，你可以把AJAX设置成同步，那就不得不科普一下了。

>JavaScript语言的执行环境是“单线程”，所谓单线程，就是一次只能够执行一个任务，如果有多个任务的话就要排队，前面一个任务完成后才可以继续下一个任务。
> “单线程”的好处就是实现起来比较简单、操作容易；坏处就是容易造成阻塞，因为队列中如果有一个任务耗时比较长，那么后面的任务都无法快速执行，或导致页面卡在某个状态上，体验很差。

JavaScript通过 `异步模式`，来解决这个问题，一般有如下方式：

+ 回调函数 `callbacks`
+ 事件监听 `addEventListener`
+ Promise对象

关于回调函数，如下代码：
```
$.ajax({
  url:"test.json",
  type: "GET",
  data: {username:$("#username").val()},
  dataType: "json",
  beforSend:function(){
       // 禁用按钮防止重复提交
      $("#submit").attr({ disabled: "disabled" });
  },
  complete:function(msg){
      //请求完成后调用的回调函数（请求成功或失败时均调用）
  } ,
  error:function(msg){
      //请求失败时被调用的函数
  } ,
  Sucess:function(msg){
      //请求成功后调用的回调函数
  }
});

```
当执行ajax请求时候，如果成功了，就执行回调函数`sucess`，如果失败就会调`error`，当我们处理的任务不是很多的时候，这种还是可以应付的，但是如果任务比较多的时候，当依赖回调的函数还要依赖其他的函数的时候就会出现依赖嵌套，使代码的维护性和可读性都变差，并且它们篡改了代码的流程，加重调试的负担。

监听器越多，对浏览器的消耗越大，导致响应速度，加载速度变慢等。

所以这时候就会用到`promise`

什么是Promise
Promise 对象用来进行延迟(deferred) 和异步(asynchronous ) 计算。

现在可以这么写

```
  var promise = $.ajax({
    url: "/url"
  });
  promise.done(successFunction);
  promise.fail(errorFunction);
  promise.always(alwaysFunction);

```

从 jQuery 1.5 开始，`$.ajax()`返回的`jqXHR`对象 实现了 `Promise `接口, 使它拥有了 Promise 的所有属性，方法和行为。为了让回调函数的名字统一，便于在`$.ajax()`中使用。`jqXHR`也提供`.error()` `.success()`和`.complete()`方法。这些方法都带有一个参数，该参数是一个函数，此函数在 `$.ajax()`请求结束时被调用,并且这个函数接收的参数，与调用 `$.ajax()`函数时的参数是一致。这将允许你在一次请求时，对多个回调函数进行赋值，甚至允许你在请求已经完成后，对回调函数进行赋值(如果该请求已经完成，则回调函数会被立刻调用)。
```
  jqXHR.done(function(data, textStatus, jqXHR) {});
```
一个可供选择的 success 回调选项的构造函数，.done()方法取代了的过时的`jqXHR.success()`方法.
```
  jqXHR.fail(function(jqXHR, textStatus, errorThrown) {});
```
一种可供选择的 error 回调选项的构造函数，.fail()方法取代了的过时的.error()方法。
```
  jqXHR.always(function(data|jqXHR, textStatus, jqXHR|errorThrown) { });
```
一种可供选择的 complete 回调选项的构造函数，.always()方法取代了的过时的.complete()方法。

在响应一个成功的请求后，该函数的参数和`.done()`的参数是相同的：data, textStatus, 和 jqXHR 对象.对于失败的请求，参数和.fail()的参数是相同的：jqXHR 对象, textStatus, 和 errorThrown。请参阅deferred.always()的实现细节。

jqXHR.then(function(data, textStatus, jqXHR) {}, function(jqXHR, textStatus, errorThrown) {});
包含了 .done() 和 .fail()方法的功能，（从 jQuery 1.8 开始）允许底层被操纵。


promise对象的一大好处，就是它允许你自由添加多个回调函数,可以用链式调用的方法，随时使用。

在任何时刻，Promise只能处于三种状态之一：未完成(unfulfilled)、已完成(resolved)和已失败(resolved)。promise默认的状态是unresolved，任何处于回调队列的函数都会被执行。假设，如果一个Ajax调用成功，$.resolved会被调用，同时promise的状态转为resolved，以及任何监听done的回调都会被执行；相反，则$.rejected会被调用，同时promise的状态转为rejected，以及任何监听fail的回调都会被执行。


在jQuery中，pomise对象类似功能的deferred对象


[http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html](http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html)
