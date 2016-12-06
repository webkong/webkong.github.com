---
title: Django重置admin密码
date: 2016-12-05 18:39:31
categories:
- Python
tags:
- Django
- 重置密码
- 原创
---

>当忘记了Django的管理员密码可以通过一下两个办法来解决

### 直接修改

```
# 在项目目录下
python manage.py changepassword admin

Changing password for user 'admin'
Password:
Password (again):
Password changed successfully for user 'admin'
```

就修改成功了。

### 如果修改不成功可以通过新建来达到目的

```
python manage.py createsuperuser root
```

可以删掉之前的，重新再添加一个。
