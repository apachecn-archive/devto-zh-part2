# 为 Plone REST API 编写定制服务

> 原文：<https://dev.to/mrtango/writing-a-custom-service-for-plone-rest-api-934>

## Plone REST API 扩展

Plone REST API 服务允许您使用扩展功能，以减少对客户端的请求。通过这种方式，您可以在一个回复中包含您希望与主要内容一起显示的附加信息。Plone REST API 已经提供了一些有用的服务，比如 Breadcrumb 服务。与扩展标志一起，您可以轻松地将特定的服务信息扩展到主响应中。

关于扩展函数的详细信息，请参见此处:[https://plonerestapi . readthedocs . io/en/latest/expansion . html](https://plonerestapi.readthedocs.io/en/latest/expansion.html)

## 实现

我们在 Plone 包中创建了以下结构:

```
services/
├── configure.zcml
├── __init__.py
└── related_articles
    ├── configure.zcml
    ├── get.py
    ├── __init__.py 
```

Enter fullscreen mode Exit fullscreen mode

在服务文件夹中，我们有这个 configure.zcml

```
<configure >

  <include package=".related\_articles" />

</configure> 
```

Enter fullscreen mode Exit fullscreen mode

也将这一行添加到父 configure.zcml:

```
<include package=".services" /> 
```

Enter fullscreen mode Exit fullscreen mode

为了注册实际的服务，我们需要 related_articles 文件夹中的以下 zcml 配置:

```
<configure

    xmlns:plone="http://namespaces.plone.org/plone">

  <adapter factory=".get.RelatedArticles" name="related-articles"/>

  <plone:service
    method="GET"
    for="zope.interface.Interface"
    factory=".get.RelatedArticlesGet"
    name="@related-articles"
    permission="zope2.View"
    />

</configure> 
```

Enter fullscreen mode Exit fullscreen mode

服务代码如下所示:

get.py

```
# -\*- coding: utf-8 -\*-
from plone import api
from plone.restapi.interfaces import IExpandableElement
from plone.restapi.services import Service
from zope.component import adapter
from zope.interface import Interface
from zope.interface import implementer

@implementer(IExpandableElement)
@adapter(Interface, Interface)
class RelatedArticles(object):

    def \_\_init\_\_(self, context, request):
        self.context = context
        self.request = request

    def \_\_call\_\_(self, expand=False):
        result = {
            'related-articles': {
                '@id': '{}/@related-articles'.format(
                    self.context.absolute\_url(),
                ),
            },
        }
        if not expand:
            return result

        solution\_categories = [c for c in self.context.solution\_category]
        article\_brains = api.content.find(
            portal\_type="Chapter",
            solution\_category={
                'query': solution\_categories,
                'operator': 'and',
            }
        )
        items = []
        for article in article\_brains:
            items.append({
                'title': article.Title,
                '@id': article.getURL(),
            })

        result['related-articles']['items'] = items
        return result

class RelatedArticlesGet(Service):

    def reply(self):
        related\_articles = RelatedArticles(self.context, self.request)
        return related\_articles(expand=True)['related-articles'] 
```

Enter fullscreen mode Exit fullscreen mode

该服务使用 plone.api 来查找 portal_type 章节的所有相关对象，并创建一个 dicts 列表，其中包含每一章的标题和 [@id](https://dev.to/id) 。与扩展标志一起，客户端可以如下扩展它:

[http://localhost:7080/Plone/some-content？expand =相关文章](http://localhost:7080/Plone/some-content?expand=related-articles)

您将在@component 部分找到扩展的服务信息:

```
"@components": {
    "actions": {
        "@id": "http://localhost:7080/Plone/some-content/@actions"
    },
    "breadcrumbs": {
        "@id": "http://localhost:7080/Plone/some-content/@breadcrumbs"
    },
    "navigation": {
        "@id": "http://localhost:7080/Plone/some-content/@navigation"
    },
    "related-articles": {
        "@id": "http://localhost:7080/Plone/some-content/@related-articles",
        "items": [{
            "@id": "http://localhost:7080/Plone/articles/harvesting",
            "title": "Harvesting"
        }]
    },
    "workflow": {
        "@id": "http://localhost:7080/Plone/some-content/@workflow"
    }
}, 
```

Enter fullscreen mode Exit fullscreen mode