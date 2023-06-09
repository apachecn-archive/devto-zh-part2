# 为 Plone REST API 编写自定义序列化程序

> 原文：<https://dev.to/mrtango/writing-a-custom-serializer-for-plone-rest-api-478n>

以下代码显示了两个自定义 FieldSerializer 适配器，它们处理 Choice 和 MultiChoice (Collection)字段的值。为此，我们为 IChoice 接口注册了一个适配器，为 ICollection 接口注册了一个适配器。两者都支持 collective.taxonomy 提供的词汇表。

默认情况下，Plone REST API 只会返回普通的令牌，因为这是在使用选择或多选字段时实际存储的内容。但是在客户端，我们需要令牌和标题，这样我们就可以轻松地显示它。因此，我们需要类似这样的内容作为选择字段的值。

## 预期响应

### I 选择字段

```
"solution\_category": {
    "title": "A nice title",
    "token": "a-nice-title"
}, 
```

Enter fullscreen mode Exit fullscreen mode

### ICollection 字段

```
"solution\_category": [
    {
        "title": "A nice title",
        "token": "a-nice-title"
    },
    {
        "title": "An awesome title",
        "token": "an-awesome-title"
    },
] 
```

Enter fullscreen mode Exit fullscreen mode

## 实现

restapi.py

```
from plone.dexterity.interfaces import IDexterityContent
from plone.restapi.interfaces import IFieldSerializer
from plone.restapi.serializer.converters import json\_compatible
from zope.component import adapter
from zope.component import getUtility
from zope.interface import implementer
from zope.interface import Interface
from zope.schema.interfaces import IChoice
from zope.schema.interfaces import ICollection
from zope.schema.interfaces import IVocabularyFactory

def \_get\_vocab\_term(context, field, value):
    """ Get vocab term dict
 returns: {'token': token, 'title': title}
 """
    vocab\_term = {
        'token': None,
        'title': None,
    }
    vocab\_term['token'] = value
    factory = getUtility(IVocabularyFactory, field)
    if not factory:
        return vocab\_term

    # collective.taxonomy support:
    if hasattr(factory, 'translate'):
        vocab\_term['title'] = \_get\_taxonomy\_vocab\_title(
            context,
            factory,
            value,
        )
    elif IVocabularyFactory.providedBy(factory):
        vocab\_term['title'] = \_get\_vocab\_title(
            context,
            factory,
            value,
        )
    return vocab\_term

def \_get\_taxonomy\_vocab\_title(context, factory, value):
        vocab\_title = factory.translate(
            value,
            context=context,
        )
        return vocab\_title

def \_get\_vocab\_title(context, factory, value):
    vocab = factory(context)
    vocab\_title = vocab.getTermByToken(value).title
    return vocab\_title

class BaseFieldSerializer(object):

    def \_\_init\_\_(self, field, context, request):
        self.context = context
        self.request = request
        self.field = field

    def \_\_call\_\_(self):
        return json\_compatible(self.get\_value())

@adapter(IChoice, IDexterityContent, Interface)
@implementer(IFieldSerializer)
class ChoiceFieldSerializer(BaseFieldSerializer):
    """
 """
    def get\_value(self, default=None):
        value = getattr(
            self.field.interface(self.context),
            self.field.\_\_name\_\_,
            default,
        )
        if not value:
            return

        term = \_get\_vocab\_term(
            self.context,
            self.field.vocabularyName,
            value,
        )
        return term

@adapter(ICollection, IDexterityContent, Interface)
@implementer(IFieldSerializer)
class CollectionFieldSerializer(BaseFieldSerializer):
    """
 """
    def get\_value(self, default=None):
        terms = []
        values = getattr(
            self.field.interface(self.context),
            self.field.\_\_name\_\_,
            default,
        )
        if not values:
            return
        if not IChoice.providedBy(self.field.value\_type):
            return

        for value in values:
            term = \_get\_vocab\_term(
                self.context,
                self.field.value\_type.vocabularyName,
                value,
            )
            terms.append(term)
        return terms 
```

Enter fullscreen mode Exit fullscreen mode

## 登记

要激活它，我们需要注册两个适配器:

configure.zcml

```
<adapter factory=".restapi.ChoiceFieldSerializer" />
<adapter factory=".restapi.CollectionFieldSerializer" /> 
```

Enter fullscreen mode Exit fullscreen mode