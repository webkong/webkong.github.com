---
title: JavaScript实现获取URL参数
date: 2014-12-10 18:43:20
categories:
- JavaScript
tags:
- JavaScript
- URL参数
- 原创
---
>通过JavaScript来获取url上携带的参数

  假设url="http://xxx.com.cn?name=webkong&age=23&sex=男"

>第一种，直接js函数，截取url，拼装成对象

```
function GET(){
    var url = window.document.location.href.toString();
    var u = url.split("?");
    if(typeof(u[1]) == "string"){
        u = u[1].split("&");
        var get = {};
        for(var i in u){
            var j = u[i].split("=");
            get[j[0]] = j[1];
        }
        return get;
    }else {
        return {};
    }
};

var getParam = GET(); //通过变量保存参数对象
//getParam:{
  name:"webkong",
  age:"23",
  sex:"男"
}
```

>和第一种逻辑过程一样，只是结果是对象本身

```
var $_GET = (function(){
    var url = window.document.location.href.toString();
    var u = url.split("?");
    if(typeof(u[1]) == "string"){
        u = u[1].split("&");
        var get = {};
        for(var i in u){
            var j = u[i].split("=");
            get[j[0]] = j[1];
        }
        return get;
    } else {
        return {};
    }
})();

// 使用时, 可以直接 $_GET['name'], 就直接获得name值
```

>第二种，通过正则来匹配

```
//通过传递参数来获取url上的某一个参数值
function GET(name){
  var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)"); //构造一个含有目标参数的正则表达式对象
  var r = window.location.search.substr(1).match(reg);  //匹配目标参数
  if (r!=null) return unescape(r[2]);
  return null; //返回参数值
}
//调用 GET('name');
```

>或者扩展成jQuery

```
(function($){
  $.getUrlParam= function(name){
    var reg = new RegExp("(^|&)"+name +"=([^&]*)(&|$)");
    var r = window.location.search.substr(1).match(reg);
    if (r!=null) return unescape(r[2]);
    return null;
  }
})(jQuery);

```


>大概有这4种封装方式，可以任意搭配封装方式和逻辑代码
