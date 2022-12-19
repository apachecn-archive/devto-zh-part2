# 用 Python 和 Django Rest 框架创建生产就绪 API 第 1 部分

> 原文：<https://dev.to/andreagrandi/creating-a-production-ready-api-with-python-and-django-rest-framework---part-1-l34>

本教程的目的是展示如何使用 **Python** 和 [Django Rest 框架](http://www.django-rest-framework.org)为 **REST API** 创建一个生产就绪的解决方案。我将向您展示如何首先创建一个非常基本的 API，如何处理认证和权限，我还将介绍映像的部署和托管。该教程的完整源代码可在:[https://github.com/andreagrandi/drf-tutorial](https://github.com/andreagrandi/drf-tutorial)获得

### 完整教程的总结

1.  创建 API 的基本结构
2.  [添加认证和发布方法](https://www.andreagrandi.it/2016/10/01/creating-a-production-ready-api-with-python-and-django-rest-framework-part-2/)
3.  [处理细节和对现有数据的更改](https://www.andreagrandi.it/2017/03/12/creating-a-production-ready-api-with-python-and-django-rest-framework-part-3/)
4.  [测试 API](https://www.andreagrandi.it/2017/08/17/creating-a-production-ready-api-with-python-and-django-rest-framework-part-4/)
5.  从 Sqlite 切换到 PostgreSQL
6.  在 Heroku 上托管 API
7.  添加图像字段并将图像保存到 S3

### 为 API 创建基本结构

对于本教程，我将假设您已经在您的系统上正确安装了至少 **Python** (我将使用 Python 2.7 . x)[**virtualenv**](https://pypi.python.org/pypi/virtualenv)和[**virtualenv wrapper**](https://virtualenvwrapper.readthedocs.io/en/latest/)，并且我将一步一步地解释如何创建其他的东西。

**注:**在撰写本文时，本教程已经基于 **Django 1.10.1** 和 **Django Rest 框架 3.4.7**

### 创建主项目结构

```
mkdir drf-tutorial
mkvirtualenv drf-tutorial
cd drf-tutorial
pip install django djangorestframework
django-admin.py startproject drftutorial .
cd drftutorial
django-admin.py startapp catalog 
```

Enter fullscreen mode Exit fullscreen mode

### 数据模型

我们将为通用产品目录创建 API，使用一个非常简单的结构(保持简单)。编辑文件 **catalog/models.py** ，添加以下几行:

```
from __future__ import unicode_literals
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=255)
    description = models.TextField()
    price = models.DecimalField(decimal_places=2, max_digits=20) 
```

Enter fullscreen mode Exit fullscreen mode

你可以在我的博客[上找到剩余的教程 https://www . Andrea grandi . it/2016/09/28/creating-production-ready-API-python-django-rest-framework-part-1/](https://www.andreagrandi.it/2016/09/28/creating-production-ready-api-python-django-rest-framework-part-1/)