---
title: Django的基础使用
date: 2016-12-14 15:31:37
categories:
- Python
tags:
- Django
- 原创

---

### 环境依赖

#### 基础依赖

1. Python任意版本，3.x没有mysql引擎

2. pip  8.x 及以上

3. virtualenv  Python虚拟环境，隔离本地项目



>使用virtualenv

```

mkdir mysite; cd mysite

virtualenv --no-site-package venv //创建一个虚拟环境

source venv/bin/activate //进入虚拟环境

deactivate //退出

```

>接下来就可以在环境中配置Django的各种依赖



#### Django依赖

1. pip install Django

2. pip install MySQL-python

3. pip install Pillow

>还有其他依赖

>django-suit

>fast-rest-fromwork

#### 验证

```

import django

print(django.get_version())

```

### 创建和使用Django



#### 创建项目

```

django-admin startproject site



//生成的目录结构

mysite/

    manage.py //django的命令行工具

    mysite/

        __init__.py

        settings.py  //django的配置文件

        urls.py //url调度

        wsgi.py

```

将应用配置进settings,以激活应用



#### 数据库建立

#### 配置数据库

```

//settings.py

DATABASES = {

    'default': {

        'ENGINE': 'django.db.backends.mysql',

        'NAME': 'django-test',

        'USER': 'root',

        'PASSWORD': '1234567890',

        'HOST': '127.0.0.1',

        'PORT': '3306',

    }

}

```

#### 数据迁移（migrate）

```

python manage.py migrate

```

#### 启动服务

```

python manage.py runserver

```



### 创建应用（模型）

```

python manage.py startapp ad

```

#### 新建Models



```

class AdInfo(models.Model):

    id = models.CharField(primary_key=True, max_length=100)

    name = models.CharField(unique=True, max_length=100)

    description = models.CharField(max_length=1000, blank=True, null=True)

    priority = models.IntegerField()

    status = models.IntegerField()

    start_time = models.DateTimeField(blank=True, null=True)

    end_time = models.DateTimeField()

    max_show_count = models.BigIntegerField()

    max_day_show_count = models.BigIntegerField()

    create_time = models.DateTimeField()

    update_time = models.DateTimeField()



    class Meta:

        managed = True

        db_table = 'ad_info'





class AdInfoPlacement(models.Model):

    ad_placement = models.ForeignKey('AdPlacement', models.DO_NOTHING)

    ad_info = models.ForeignKey(AdInfo, models.DO_NOTHING)

    create_time = models.DateTimeField()



    class Meta:

        managed = True

        db_table = 'ad_info_placement'

        unique_together = (('ad_info', 'ad_placement'),)





class AdPlacement(models.Model):

    id = models.CharField(primary_key=True, max_length=100)

    name = models.CharField(max_length=100)

    app_id = models.CharField(max_length=100)

    status = models.IntegerField()

    create_time = models.DateTimeField()



    class Meta:

        managed = True

        db_table = 'ad_placement'

        unique_together = (('app_id', 'name'),)





class AdProperties(models.Model):

    ad_info_id = models.CharField(max_length=100)

    property_key = models.CharField(max_length=100, blank=True, null=True)

    property_value = models.CharField(max_length=2000, blank=True, null=True)

```



>django不支持双主键或者多主键



执行命令通知django修改models

```

python manage.py makemigrations //创建迁移文件

python manage.py migrate //更新到数据库

```



> python manage.py inspectdb > models.py //反向生成model

#### 创建管理员，admin管理



```

python manage.py createsuperuser

根据提示一次输入用户名，邮箱和密码就OK了

```

启动服务看一下。


这样就成功启动了，根据自己定义的Model的Django。在host/admin/目录下可以登陆并看到相应的管理条目


#### 自定义管理

未完待续...

### 其他链接

[官方文档options](http://python.usyiyi.cn/documents/django_182/ref/contrib/admin/index.html)

[官方文档actions](http://python.usyiyi.cn/documents/django_182/ref/contrib/admin/actions.html)


[配置详情](http://python.usyiyi.cn/documents/django_182/topics/settings.html)
[url调度器](http://python.usyiyi.cn/documents/django_182/topics/http/urls.html)
