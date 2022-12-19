# Django 迁移:入门

> 原文：<https://dev.to/taranjeet/django-migrations-a-primer-163b>

Django 中的迁移是将模型中的更改传播到数据库模式中的一种方式。

官方的 Django 文档将移民总结为

> 迁移是 Django 传播您对模型所做更改的方式(添加字段、删除模型等)。)到您的数据库模式中

在 Django 版本中，迁移作为内置功能被引入。迁移是通过运行某些命令手动创建的，但是它们自动执行将更改应用到数据库的任务。我们可以为模型中的任何变化创建一个迁移，比如

*   添加新型号。

*   在模型中添加特定字段

*   更新模型中的字段属性

*   删除模型中的字段

*   重命名模型中的字段

自动生成的迁移用于将更改应用到数据库模式。要应用数据更改，需要手动编写[数据迁移](https://docs.djangoproject.com/en/2.0/topics/migrations/#data-migrations)。

让我们考虑一个库系统的例子，并浏览创建和应用迁移的整个例子。考虑到我们的项目是`django_library`，里面有一个叫`library`的 app。我们现在只关注`Book`车型。