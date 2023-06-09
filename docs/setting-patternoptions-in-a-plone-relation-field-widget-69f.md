# 在 Plone 关系字段小部件中设置 pattern_options

> 原文：<https://dev.to/mrtango/setting-patternoptions-in-a-plone-relation-field-widget-69f>

为了使用 Plone 5 中 RelationWidget 的全部功能，您可以设置一些 pattern_options，这在链接 [Mockup docs](http://plone.github.io/mockup/dev/#pattern/relateditems) 中有描述。

如果使用 ReleationField，通常希望浏览数据库中的所有对象，但只允许选择特定的 portal _ types。

RelatedItemsFieldWidget 使用的模式 relateditems 具有通过设置 selectableTypes 列表(数组)来配置它的选项。

您可以将 patter _ options 参数传递给小部件配置，使用 plone.autoform .指令如下:

```
directives.widget(
    'location',
    RelatedItemsFieldWidget,
    pattern\_options={'selectableTypes': ['location']},
)
locations = RelationList(
    title=\_(u'Locations'),
    value\_type=RelationChoice(
        title=\_(u'Related'),
        source=CatalogSource(),
    ),
    required=False,
) 
```

Enter fullscreen mode Exit fullscreen mode

这将使用您提供的配置更新 pattern_options。

同样，您可以为模式添加更多的配置设置，例如 basePath。