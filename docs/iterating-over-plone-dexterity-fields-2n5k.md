# 在 Plone 灵巧域上迭代

> 原文：<https://dev.to/mrtango/iterating-over-plone-dexterity-fields-2n5k>

要获取对象的所有相关模式，您可以使用 plone.dexterity.utils 提供的 iterSchemata 函数。从模式列表中，您可以使用 zope.schema 提供的 getFields 函数来获取字段

```
from plone.dexterity.utils import iterSchemata
from zope.schema import getFields

fields = {}
schemes = iterSchemata(context)
for schema in schemes:
    fields.update(getFields(schema))

for name, field in fields.items():
    print("Field: {0}, value: {1}".format(name, field)) 
```

Enter fullscreen mode Exit fullscreen mode