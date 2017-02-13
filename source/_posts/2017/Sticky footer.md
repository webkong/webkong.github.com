---
layout: "post"
title: Sticky footer
date: 2017年02月13日15:15:43
categories:
- CSS
tags:
- Flex
- 布局
- Sticky footer
- 原创
---

### Sticky footer 是什么？

> Sticky footers设计是最古老和最常见的网页效果之一，大多的CSSer都遇到过。可以概括成： 页面有一个footer，如果页面不够长，页脚在视窗底部；如果视窗足够长，页脚会被内容向下挤压，页脚最终还是在页面底部。
<!-- more -->

可以在这里看到效果 [http://www.cssstickyfooter.com/](http://www.cssstickyfooter.com/)

### 常见实现

这种效果几乎存在于所有的公司网站和单页网站，看上去很容易实现，但是实际要花费的时间远超预期。在css2.0中解决这个页脚基本都是设置一个固定高度来实现，甚至使用了js的一些计算来进行高度的定位。可以参考：
[Sticky Footer](https://css-tricks.com/snippets/css/sticky-footer/)
[Sticky CSS footers: The flexible way](https://pixelsvsbytes.com/2011/09/sticky-css-footers-the-flexible-way/)

目前比较流行的技巧就是通过设置容器`height:100%;padding-bottom:<固定高度>` ， footer `maring-top:-<固定高度>`实现

```
//html

<body>
  <div class="wrapper">
    <div class="main">
      <p>xxxx</p>
    </div>
  </div>
  <div class="footer>
    <p>xxxx</p>
  </div>
</body>


//stylus
.wrapper
  min-height 100%
  .main
    margin-top 64px
    padding-bottom 64px
.footer
  position relative
  width 32px
  height 32px
  margin -64px auto 0 auto
  clear both
```



### Flex 实现页脚

Flex的语法可以参考我之前的一个[Flex 布局](http://blog.webkong.cn/2017/Flex%E5%B8%83%E5%B1%80/)

使用flex几行代码就可以实现，先在`<body>`上设置`display:flex`, 并添加`flex-direction: column;`让内容纵向排列，或者使用组合属性`flex-flow:column`, 再设置`min-height:100vh`，让body占据整个视窗。在`<main>`上设置`flex:1`来让main自动适配剩余空间的大小。这样就OK了。

```
body {
  padding:0;
  margin:0;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
main { flex: 1; }
#contents:checked ~ p { display: none }
footer{
  background: #000;
  color: #fff;
}
```

<a href="/demo/stickyfooter.html" target="_blank">效果展示</a>







### 参考

[Sticky CSS footers: The flexible way](https://pixelsvsbytes.com/2011/09/sticky-css-footers-the-flexible-way/)
