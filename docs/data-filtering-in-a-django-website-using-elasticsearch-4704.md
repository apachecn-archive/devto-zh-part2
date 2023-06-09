# 使用 Elasticsearch 在 Django 网站中过滤数据

> 原文：<https://dev.to/djangotricks/data-filtering-in-a-django-website-using-elasticsearch-4704>

[![](img/77147fdad6066b16c0205e3bc0a5ed83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LdQRjd58--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-gR38pCjj45s/WyW2N_jxcuI/AAAAAAAAB2w/Lg3_wQnnjPIMfpN2uBbz_poYGfVS5LjNQCLcBGAs/s1600/data-filtering-in-a-django-website-using-elasticsearch.png)

在我的[Web Development with Django Cookbook](https://www.packtpub.com/web-development/web-development-django-cookbook-second-edition)部分
*表单和视图*中有一个菜谱*过滤对象列表*。它向您展示了如何通过在表单中选择不同的过滤参数来动态过滤 Django QuerySet。从实践来看，这种方法运行良好，但是对于大量数据和复杂的嵌套过滤器，性能可能会变慢。您知道——由于 SQL 中所有的内部连接，页面加载甚至可能需要 12 秒。这可不是什么好行为。我知道我可以对数据库进行反规范化，或者使用索引来优化 SQL。但是我找到了一个更好的方法来提高加载速度。最近，我们开始在一个项目中使用 Elasticsearch，它的数据过滤性能似乎快了很多:在我们的例子中，根据您选择的查询参数，它从 2 倍增加到 16 倍。

## 什么是 Elasticsearch？

Elasticsearch 是基于 java 的搜索引擎，它以 JSON 格式存储数据，并允许您使用特殊的基于 JSON 的查询语言进行查询。使用 [elasticsearch-dsl](https://elasticsearch-dsl.readthedocs.io/en/latest/) 和 [django-elasticsearch-dsl](https://github.com/sabricot/django-elasticsearch-dsl) ，我可以将我的 django 模型绑定到 elasticsearch 索引，并重写我的对象列表视图以使用 elasticsearch 查询而不是 Django ORM。Elasticsearch DSL 的 API 像 Django QuerySets 或 jQuery 函数一样是可链接的，我们很快就会看到它。

## 设置

首先，让我们安装 Elasticsearch 服务器。Elasticsearch 是一个相当复杂的系统，但是它提供了方便的默认配置。

在 macOS 上，您可以用自制软件安装并启动服务器:

```
$ brew install elasticsearch
$ brew services start elasticsearch 
```

Enter fullscreen mode Exit fullscreen mode

对于其他平台，[安装说明](https://www.elastic.co/guide/en/elasticsearch/reference/current/_installation.html)也是相当清楚的。

然后在你的 Django 项目的虚拟环境中安装 **django-elasticsearch-dsl** 。我猜，“DSL”代表“领域特定语言”。

使用 pipenv，项目目录中的内容如下:

```
$ pipenv install django-elasticsearch-dsl 
```

Enter fullscreen mode Exit fullscreen mode

如果你只使用了 **pip** 和**虚拟环境**，那么你可以在你的项目环境被激活的情况下这样做。

```
(venv)$ pip install django-elasticsearch-dsl 
```

Enter fullscreen mode Exit fullscreen mode

这将依次安装相关的低级客户端库: **elasticsearch-dsl** 和 **elasticsearch-py** 。

在 Django 项目设置中，将`'django_elasticsearch_dsl'`添加到`INSTALLED_APPS`。

最后，在这里添加定义默认连接配置的行:

```
ELASTICSEARCH_DSL={
    'default': {
        'hosts': 'localhost:9200'
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

## Django 模型的弹性搜索文档

为了演示如何在 Django 中使用 Elasticsearch，我将创建`Author`和`Book`模型，然后为书籍创建 Elasticsearch 索引文档。

### models.py

```
# -*- coding: UTF-8 -*- from __future__ import unicode_literals

from django.db import models
from django.utils.translation import ugettext_lazy as _
from django.utils.encoding import python_2_unicode_compatible

@python_2_unicode_compatible
class Author(models.Model):
    first_name = models.CharField(_("First name"), max_length=200)
    last_name = models.CharField(_("Last name"), max_length=200)
    author_name = models.CharField(_("Author name"), max_length=200)

    class Meta:
        verbose_name = _("Author")
        verbose_name_plural = _("Authors")
        ordering = ("author_name",)

    def __str__(self):
        return self.author_name

@python_2_unicode_compatible
class Book(models.Model):
    title = models.CharField(_("Title"), max_length=200)
    authors = models.ManyToManyField(Author, verbose_name=_("Authors"))
    publishing_date = models.DateField(_("Publishing date"), blank=True, null=True)
    isbn = models.CharField(_("ISBN"), blank=True, max_length=20)

    class Meta:
        verbose_name = _("Book")
        verbose_name_plural = _("Books")
        ordering = ("title",)

    def __str__(self):
        return self.title 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的。只是一个带有字段`id`、`first_name`、`last_name`、`author_name`的`Author`模型，以及一个带有字段`id`、`title`、`authors`、`publishing_date`和`isbn`的`Book`模型。我们去看文件吧。

### [T1】documents . py](#documentspy)

在你的 app 的同一个目录下，创建`documents.py`，内容如下:

```
# -*- coding: UTF-8 -*- from __future__ import unicode_literals

from django_elasticsearch_dsl import DocType, Index, fields
from .models import Author, Book

# Name of the Elasticsearch index search_index = Index('library')
# See Elasticsearch Indices API reference for available settings search_index.settings(
    number_of_shards=1,
    number_of_replicas=0
)

@search_index.doc_type
class BookDocument(DocType):
    authors = fields.NestedField(properties={
        'first_name': fields.TextField(),
        'last_name': fields.TextField(),
        'author_name': fields.TextField(),
        'pk': fields.IntegerField(),
    }, include_in_root=True)

    isbn = fields.KeywordField(
        index='not_analyzed',
    )

    class Meta:
        model = Book # The model associated with this DocType 
        # The fields of the model you want to be indexed in Elasticsearch
        fields = [
            'title',
            'publishing_date',
        ]
        related_models = [Author]

    def get_instances_from_related(self, related_instance):
        """If related_models is set, define how to retrieve the Book instance(s) from the related model."""
        if isinstance(related_instance, Author):
            return related_instance.book_set.all() 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了一个`BookDocument`，它将包含字段:`title`、`publishing_date`、`authors`和`isbn`。

`authors`将是在`BookDocument`的嵌套字典列表。`isbn`将是一个`KeywordField`，这意味着它不会被标记化、小写，也不会被处理和处理。

这些文档字段的值将从`Book`模型中读取。

使用信号，当添加、更改或删除一个`Book`实例或`Author`实例时，文档将自动更新。在方法`get_instances_from_related()`中，我们告诉搜索引擎当一个作者被更新时更新哪些书。

### 建立索引

当索引文档准备好时，让我们在服务器上构建索引:

```
(venv)$ python manage.py search_index --rebuild 
```

Enter fullscreen mode Exit fullscreen mode

## 姜戈查询集诉弹性搜索请求

SQL 和 Elasticsearch 查询的概念非常不同。一个是处理关系表，另一个是处理字典。一种是使用人类可读的逻辑语句的查询，另一种是使用嵌套的 JSON 结构。一种是逐字使用内容，另一种是在后台进行字符串处理，并给出每个结果的搜索相关性。

即使有很多不同之处，我也会尽量在 Django ORM 和 **elasticsearch-dsl** API 之间进行类比。

### 1。查询定义

Django QuerySet:

```
queryset = MyModel.objects.all() 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
search = MyModelDocument.search() 
```

Enter fullscreen mode Exit fullscreen mode

### 2。数数

Django QuerySet:

```
queryset = queryset.count() 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
search = search.count() 
```

Enter fullscreen mode Exit fullscreen mode

### 3。循环

Django QuerySet:

```
for item in queryset:
    print(item.title) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
for item in search:
    print(item.title) 
```

Enter fullscreen mode Exit fullscreen mode

### 4。要查看生成的查询:

Django QuerySet:

```
>>> queryset.query 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
>>> search.to_dict() 
```

Enter fullscreen mode Exit fullscreen mode

### 5。按包含值的单个字段筛选

Django QuerySet:

```
queryset = queryset.filter(my_field__icontains=value) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
search = search.filter('match_phrase', my_field=value) 
```

Enter fullscreen mode Exit fullscreen mode

### 6。按等于一个值的单个字段筛选

Django QuerySet:

```
queryset = queryset.filter(my_field__exact=value) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
search = search.filter('match', my_field=value) 
```

Enter fullscreen mode Exit fullscreen mode

如果字段类型是字符串，而不是数字，则必须在索引文档
中将其定义为`KeywordField`

```
my_field = fields.KeywordField() 
```

Enter fullscreen mode Exit fullscreen mode

### 7。使用任一条件(或)进行筛选

Django QuerySet:

```
from django.db import models
queryset = queryset.filter(
    models.Q(my_field=value) |
    models.Q(my_field2=value2)
) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
from elasticsearch_dsl.query import Q
search = search.query(
    Q('match', my_field=value) |
    Q('match', my_field2=value2)
) 
```

Enter fullscreen mode Exit fullscreen mode

### 8。使用所有条件进行筛选(AND)

Django QuerySet:

```
from django.db import models
queryset = queryset.filter(
    models.Q(my_field=value) &
    models.Q(my_field2=value2)
) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
from elasticsearch_dsl.query import Q
search = search.query(
    Q('match', my_field=value) & 
    Q('match', my_field2=value2)
) 
```

Enter fullscreen mode Exit fullscreen mode

### 9。按小于或等于特定值的值进行筛选

Django QuerySet:

```
from datetime import datetime

queryset = queryset.filter(
    published_at__lte=datetime.now(),
) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
from datetime import datetime

search = search.filter(
    'range',
    published_at={'lte': datetime.now()}
) 
```

Enter fullscreen mode Exit fullscreen mode

### 10。按嵌套字段中的值筛选

Django QuerySet:

```
queryset = queryset.filter(
    category__pk=category_id,
) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
search = search.filter(
    'nested', 
    path='category', 
    query('match', category__pk=category_id)
) 
```

Enter fullscreen mode Exit fullscreen mode

### 11。按相关模型中的多个值之一进行筛选

Django QuerySet:

```
queryset = queryset.filter(
    category__pk__in=category_ids,
) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
from django.utils.six.moves import reduce
from elasticsearch_dsl.query import Q

search = search.query(
    reduce(operator.ior, [
        Q(
            'nested', 
            path='category', 
            query('match', category__pk=category_id),
        )
        for category_id in category_ids
    ])
) 
```

Enter fullscreen mode Exit fullscreen mode

这里的`reduce()`函数使用按位 OR 运算符(|)组合了一系列的`Q()`条件。

### 12。排序

Django QuerySet:

```
queryset = queryset.order_by('-my_field', 'my_field2') 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
search = search.sort('-my_field', 'my_field2') 
```

Enter fullscreen mode Exit fullscreen mode

### 13。动态创建查询

Django QuerySet:

```
import operator
from django.utils.six.moves import reduce

filters = []
if value1:
    filters.append(models.Q(
        my_field1=value1,
    ))
if value2:
    filters.append(models.Q(
        my_field2=value2,
    ))
queryset = queryset.filter(
    reduce(operator.iand, filters)
) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
import operator
from django.utils.six.moves import reduce
from elasticsearch_dsl.query import Q

queries = []
if value1:
    queries.append(Q(
        'match',
        my_field1=value1,
    ))
if value2:
    queries.append(Q(
        'match',
        my_field2=value2,
    ))
search = search.query(
    reduce(operator.iand, queries)
) 
```

Enter fullscreen mode Exit fullscreen mode

### 14。页码

Django QuerySet:

```
from django.core.paginator import (
    Paginator, Page, EmptyPage, PageNotAnInteger
)

paginator = Paginator(queryset, paginate_by)
page_number = request.GET.get('page')
try:
    page = paginator.page(page_number)
except PageNotAnInteger:
    page = paginator.page(1)
except EmptyPage:
    page = paginator.page(paginator.num_pages) 
```

Enter fullscreen mode Exit fullscreen mode

弹性搜索查询:

```
from django.core.paginator import (
    Paginator, Page, EmptyPage, PageNotAnInteger
)
from django.utils.functional import LazyObject

class SearchResults(LazyObject):
    def __init__(self, search_object):
        self._wrapped = search_object

    def __len__(self):
        return self._wrapped.count()

    def __getitem__(self, index):
        search_results = self._wrapped[index]
        if isinstance(index, slice):
            search_results = list(search_results)
        return search_results

search_results = SearchResults(search)

paginator = Paginator(search_results, paginate_by)
page_number = request.GET.get('page')
try:
    page = paginator.page(page_number)
except PageNotAnInteger:
    page = paginator.page(1)
except EmptyPage:
    page = paginator.page(paginator.num_pages) 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，ElasticSearch 不支持 Django 的分页。因此，我们必须用 lazy `SearchResults`类包装搜索查询，以提供必要的功能。

## 举例

我用关于姜戈的书做了一个例子。你可以[从 Github](https://github.com/archatas/experiment-with-django-and-elasticsearch) 下载并测试它。

## 外卖

*   使用 Elasticsearch 过滤比使用 SQL 数据库快得多。
*   但这是以额外的部署和支持时间为代价的。
*   如果在同一个服务器上有多个使用 Elasticsearch 的网站，[为每个网站配置一个新的集群和节点](https://www.elastic.co/guide/en/elasticsearch/reference/current/zip-targz.html#zip-targz-configuring)。
*   Django ORM 在某种程度上可以映射到 Elasticsearch DSL。
*   我把本文中提到的 Django ORM 和 Elasticsearch DSL 的比较总结成[一个备忘单](https://www.djangotricks.com/goodies/tq4NRGdnXkiM/)。把它打印在一张纸上，作为你发展的参考。

<center>
[✨✨✨
Get Django ORM vs. Elasticsearch DSL Cheat Sheet
✨✨✨](https://www.djangotricks.com/goodies/tq4NRGdnXkiM/)</center>

* * *

卡尔·弗雷德里克森的封面照片。