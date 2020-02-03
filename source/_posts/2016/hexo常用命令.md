---
title: Hexo常用命令
date: 2016-09-08 17:54:00
categories: Blog
categories:
  - Hexo

---### hexo 安装升级

```
npm install hexo -g #安装
npm update hexo -g #升级
hexo init #初始化
```

### 简写

```
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo p == hexo publish
hexo g == hexo generate#生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy#部署
```

### 开启本地服务器

```
hexo server #Hexo 会监视文件变动并自动更新，您无须重启服务器。
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP

hexo clean #清除缓存 网页正常情况下可以忽略此条命令
hexo g #生成静态网页
hexo d #开始部署
```

### 监视文件变动

```
hexo generate #使用 Hexo 生成静态文件快速而且简单
hexo generate --watch #监视文件变动
```

### 完成后部署

```
两个命令的作用是相同的
hexo generate --deploy
hexo deploy --generate
hexo deploy -g
hexo server -g
```

### 发布草稿

```
hexo publish [layout] <title>
```

### 模版

```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub

hexo new [layout] <title>
hexo new photo "My Gallery"
hexo new "Hello World" --lang tw
```
