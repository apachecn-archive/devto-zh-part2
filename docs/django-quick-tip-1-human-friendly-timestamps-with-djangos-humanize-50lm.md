# Django 快速提示# 1:Django 人性化的人性化时间戳

> 原文：<https://dev.to/fleepgeek/django-quick-tip-1-human-friendly-timestamps-with-djangos-humanize-50lm>

欢迎阅读这篇简短的 Django 技巧。这里没有长篇大论，因为这是一个“快速”。这就是我们要实现的目标:

[![Tranformed timestamp](../Images/55c9ecf831fb7218d077a607266c1c11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XtOOk4rW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jai675t6vj4qhwnhxz9r.png)

为了实现这一点，我们将使用位于`django.contrib.humanize`中的`humanize`模块。
人性化模块是一个模板过滤器的集合，为我们显示的数据增加了人情味。它没有在我们的数据库中给出精确的值，而是将其美化成一种易读的格式。

# 我们开始工作吧

## 1。将模块添加到您的`INSTALLED_APPS`:

```
'django.contrib.humanize' 
```

Enter fullscreen mode Exit fullscreen mode

## 2。将其加载到您的模板中:

```
{% load humanize %} 
```

Enter fullscreen mode Exit fullscreen mode

## 3。将过滤器应用于您的时间戳

```
{{ viewed_on|naturaltime }} 
```

Enter fullscreen mode Exit fullscreen mode

# 就是这样！

`humanize`模块还包含其他过滤器。这里有一个[链接](https://docs.djangoproject.com/en/2.0/ref/contrib/humanize/)到文档。
现在，让你的应用变得人性化。

# 视频版

[https://www.youtube.com/embed/jsXHgjXRJLY](https://www.youtube.com/embed/jsXHgjXRJLY)