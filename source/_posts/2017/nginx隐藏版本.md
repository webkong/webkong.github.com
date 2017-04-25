---
layout: "post"
title: "nginx隐藏版本"
date: "2017-04-11 15:25:52"
author: "webkong"
categories:
- 服务器端
tags:
- nginx
- 隐藏版本
---

>mark and learn
在搭建好了nginx之后，一般会隐藏版本号，避免不必要的麻烦，
```
curl -I www.nginx.org

```
```
HTTP/1.1 301 Moved Permanently
Server: nginx/1.11.7
Date: Tue, 11 Apr 2017 07:30:05 GMT
Content-Type: text/html
Content-Length: 185
Connection: keep-alive
Keep-Alive: timeout=15
Location: http://nginx.org/
```

如上，就会看到nginx的版本是1.11.7，之前爆出的Nginx版本漏洞，这样暴露出来的版本就容易变成被利用的信息，从安全角度来讲，隐藏会相对好一些。

**1.进入nginx配置文件**

在http{}里面加上`server_tokens off;`

**2.修改php-fpm配置文件，根据自己的配置文件名修改(fastcgi.conf...)**

```
找到：
fastcgi_param SERVER_SOFTWARE nginx/$nginx_version;
改为：
fastcgi_param SERVER_SOFTWARE nginx;
```
**3.重新加载nginx配置**

```
nginx -s reload
```
>Other

如果要隐藏php的版本，“X-Powered-By: PHP/5.2.13” 这个信息
编辑 `php.ini`配置文件，修改或者加入 expose_php = Off
再重启php-fpm 

```
/etc/init.d/php-fpm restart 
```