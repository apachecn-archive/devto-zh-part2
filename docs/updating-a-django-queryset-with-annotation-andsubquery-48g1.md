# 用注释和子查询更新 Django 查询集

> 原文：<https://dev.to/pauloxnet/updating-a-django-queryset-with-annotation-andsubquery-48g1>

> 使用注释和子查询更新 Django 查询集的操作指南

## 前言

在 Django [的官方文档](https://docs.djangoproject.com)中，没有关于使用 Django ORM **update()** 和 **annotate()** 函数通过使用注释值来更新 queryset 中的所有行的信息。

我们将展示一种只使用 Django ORM **子查询()**而不使用 *extra()* 函数或 *SQL* 代码来更新带注释的 Django queryset 的方法。

## 型号

首先，我们使用 weblog 应用程序代码，可以在 Django 文档中的[“Making Queries”](https://docs.djangoproject.com/en/2.0/topics/db/queries/)下找到。

`Python`

```
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    rating = models.DecimalField(max_digits=3, decimal_places=2, default=5)

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    rating = models.IntegerField(default=5)

    def __str__(self):
        return self.headline 
```

Enter fullscreen mode Exit fullscreen mode

## 一期

基于所有条目的平均评级来更新博客评级的一种方法可以是:

`Python`

```
from django.db.models import Avg
from blog.models import Blog

for blog in Blog.objects.annotate(avg_rating=Avg('entry__rating')):
    blog.rating = blog.avg_rating or 0
    blog.save() 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有很多条目或博客，上面的代码可能会非常低效和缓慢，因为 Django ORM 对 for-cycle 的每一步都执行 SQL 查询。

如果我们想避免上面的代码，而在单个 SQL 请求中执行更新操作，我们可以尝试使用这样的代码:

`Python`

```
Blog.objects.update(rating=Avg('entry__rating')) 
```

Enter fullscreen mode Exit fullscreen mode

但是这不起作用，我们将会看到类似这样的错误:

```
Traceback (most recent call last):
...
FieldError: Joined field references are not permitted in this query 
```

Enter fullscreen mode Exit fullscreen mode

## 解

在 Django 1.11+中，可以使用 Django ORM，但是要使用**子查询()**:

> **子查询()表达式**
> 
> 您可以使用**子查询**表达式向**查询集**添加显式子查询。
> 
> *[参见文档](https://docs.djangoproject.com/en/2.1/ref/models/expressions/#subquery-expressions)*

`Python`

```
from django.db.models import Avg, OuterRef, Subquery
from blog.models import Blog

Blog.objects.update( 
    rating=Subquery( 
        Blog.objects.filter( 
            id=OuterRef('id') 
        ).annotate( 
            avg_rating=Avg('entry__rating') 
        ).values('avg_rating')[:1] 
    ) 
) 
```

Enter fullscreen mode Exit fullscreen mode

在 PostgreSQL 上，SQL 看起来像:

`SQL`

```
UPDATE "blog_blog"
SET "rating" = (
   SELECT AVG(U1."rating") AS "avg_rating"
   FROM "blog_blog" U0
   LEFT OUTER JOIN "blog_entry" U1 ON (U0."id" = U1."blog_id")
   WHERE U0."id" = ("blog_blog"."id")
   GROUP BY U0."id"
   LIMIT 1
) 
```

Enter fullscreen mode Exit fullscreen mode

# 堆栈溢出

我第一次写这个解决方案是作为对**栈溢出**的回答。

如果你觉得这篇文章有用，你可以[在 Stack Overflow 上为我的答案](https://stackoverflow.com/a/50134728/755343)投票，并在[我的个人资料](https://stackoverflow.com/users/755343)上阅读我的其他答案。

# 执照

本文以知识共享署名共享许可协议发布。

【creativecommons.org/licenses/by-sa】T2

# 源代码

我在 GitHub 上发布了本文使用的源代码。

【github.com/pauloxnet/django_queries】T2

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【pauloxnet】](https://github.com/pauloxnet)/[姜戈里](https://github.com/pauloxnet/djangoqueries)

### 我在文章“用 PostgreSQL 在 Django 中进行全文搜索”中使用的 docs.djangoproject.com“查询”代码。

<article class="markdown-body entry-content container-lg" itemprop="text">

# <g-emoji class="g-emoji" alias="unicorn" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f984.png">🦄</g-emoji>姜戈想要

[![Twitter: pauloxnet](img/0301fba733289c8b07d40dfa7b222c1c.png) ](https://twitter.com/pauloxnet) [ ![Code style: black](img/1abbac1bb267dc041dcfa091baa4840f.png)](https://github.com/python/black)

我的文章[“用 PostgreSQL 在 Django 中进行全文搜索”](https://www.paulox.net/2017/12/22/full-text-search-in-django-with-postgresql)中使用的源代码基于 Django 文档主题[“进行查询”](https://docs.djangoproject.com/en/stable/topics/db/queries/)中定义的博客应用程序。

## <g-emoji class="g-emoji" alias="book" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4d6.png">📖</g-emoji>文档

### 数据库

在本地 PostgreSQL 实例中创建`djangoqueries`数据库:

```
$ createdb -U postgres -O postgres djangoqueries
```

Enter fullscreen mode Exit fullscreen mode

### 虚拟环境

创建和激活虚拟环境:

```
$ python3 -m venv .venv
$ source .venv/bin/activate
```

Enter fullscreen mode Exit fullscreen mode

### <g-emoji class="g-emoji" alias="jigsaw" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f9e9.png">🧩</g-emoji> 要求

在`djangoqueries`虚拟环境中安装所需的 python 包:

```
$ python3 -m pip install -r requirements/local.txt
```

Enter fullscreen mode Exit fullscreen mode

### ⬆️ 迁徙

迁移`djangoqueries`数据库以创建所有需要的表:

```
$ python3 -m manage migrate
```

Enter fullscreen mode Exit fullscreen mode

### <g-emoji class="g-emoji" alias="microscope" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f52c.png">🔬</g-emoji>测试

运行定义的测试:

```
$ python3 -m manage test
```

Enter fullscreen mode Exit fullscreen mode

### <g-emoji class="g-emoji" alias="bar_chart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ca.png">📊</g-emoji>数据

用博客应用程序的演示数据填充`djangoqueries`数据库:

```
$ python3 -m manage populate
```

Enter fullscreen mode Exit fullscreen mode

## 许可证

**Django 查询**根据 [BSD 三条款许可](https://github.com/pauloxnet/djangoqueries/blob/master/LICENSE.md)获得许可。

## <g-emoji class="g-emoji" alias="busts_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f465.png">👥</g-emoji>作者

### <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">保罗·梅尔基耶</g-emoji>

*   <g-emoji class="g-emoji" alias="earth_africa" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f30d.png">🌍</g-emoji>博客:【www.paulox.net】T2
*   <g-emoji class="g-emoji" alias="octopus" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f419.png">🐙</g-emoji> …

</article>

[View on GitHub](https://github.com/pauloxnet/djangoqueries)

# 分享

液体错误:内部

[https://www.instagram.com/p/BoFRx63CrMS/embed/captioned/](https://www.instagram.com/p/BoFRx63CrMS/embed/captioned/)