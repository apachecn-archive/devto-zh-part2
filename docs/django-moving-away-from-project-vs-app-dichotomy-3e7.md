# Django:远离项目与应用二分法

> 原文：<https://dev.to/k4ml/django-moving-away-from-project-vs-app-dichotomy-3e7>

我在看一个开发人员学习如何使用 Django 构建应用程序的练习。他的目录结构如下:-

```
ceri main manage.py ... 
```

Enter fullscreen mode Exit fullscreen mode

`ceri`是为此项目选择的名称。但是深入查看每个目录，你会发现`ceri`只包含`settings.py`，而`main`中有`views.py`、`models.py`等。目录基本上是这个应用程序的核心。看着这个我很困惑。

他们可能在学习 Django 的教程，并从:-

```
django-admin startproject ceri 
```

Enter fullscreen mode Exit fullscreen mode

因为“项目”在业务层面上被称为“ceri”，所以这个开发人员使用“ceri”运行`startproject`也是合乎逻辑的。但是接下来他谈到了教程要求他做的下一件事:-

```
django-admin startapp ... 
```

Enter fullscreen mode Exit fullscreen mode

我的猜测是，开发人员将在一段时间内思考合适的名称。他放弃了，只是选择了“主”作为名字。

对于初学者来说，这种“应用程序”对“项目”是不必要的。没有什么可以阻止你把你所有的视图和模型放到`ceri`目录中，但是 Django 的新手会有这样的印象，这些“项目”和“应用”是必需的。这样做的理由是鼓励从一开始就编写可重用的应用程序。如果你写的“应用程序”足够通用，你可以把它插入到另一个项目中。

但是 80%的时候，你只需要写一个针对那个项目的应用程序。最常见的情况是，人们会为他们的应用程序选择一个通用名称，如“用户”、“订单”、“客户”等，通常称为顶级名称空间。我敢打赌，他们首先会遇到他们想要使用第三方包与他们一般命名的应用程序冲突的情况，而不是他们想要重用他们的应用程序的情况。

这并不是说开发人员不应该编写通用应用程序，但这可以在后面的章节中介绍。我们可以在 Stackoverflow 减少混乱和这种问题。