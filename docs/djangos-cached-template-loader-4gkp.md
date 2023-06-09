# Django 的缓存模板加载器

> 原文：<https://dev.to/easyaspython/djangos-cached-template-loader-4gkp>

在我们对 Django 更好性能的永无止境的渴望中，我们不断寻找在更短时间内完成更少工作的方法，同时为我们的用户保持一致的体验。当我们发现可以使用 Django tool belt 的内置方法时，这种感觉尤其美妙！

Django 模板包含两个概念，**和 [**扩展**](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/#extends) ，这可以导致使用多个模板来呈现一个页面。一个基本模板可能包括你的页面需要的所有 JavaScript、CSS 和框架 HTML 设置。例如，它可能**包括**用于导航和页脚的其他模板。其他模板可能**扩展**基本模板，以删除基本内容的某些部分或添加特定于页面的部分。因此，单个页面在最终呈现并发送回浏览器之前使用少量模板是很常见的。**

 **随着网站功能和受众的增加，其模板的数量和复杂性也会增加，这并不罕见。可能会涉及到几层扩展，并且模板包含似乎可以无限期地在多个级别上嵌套包含。这可以很快偷偷摸摸！

当 Django 决定是时候呈现一个页面时，它通过一系列的 [**加载器**](https://docs.djangoproject.com/en/2.0/ref/templates/api/#loader-types) 来决定使用哪些模板。加载程序按顺序运行，搜索它们所知道的模板，看是否有匹配所请求的模板名的模板。Django 使用它通过这些加载器找到的第一个模板，如果没有加载器能找到匹配，最终引发一个`TemplateNotFound`异常。一旦 Django 决定了一个模板，它就从磁盘中读取该模板，并跟踪它需要的任何进一步的模板引用，直到它有足够的信息来呈现当前页面。这看起来很简单，但是有一个问题！

[![](img/4e5dee2af429faf8b81aefc992d27798.png)T2】](https://cdn-images-1.medium.com/proxy/1*I_UzmUJd7ozroRmQbEEKQA.gif)

在 Django 1.10 之前，默认行为是从磁盘中读取每个需要的模板，每个请求，*每次使用*。循环中包含的模板将在循环运行时从磁盘中读取。我们的一个特定应用程序在高峰流量期间每分钟接收 500 个请求，这通常是可以管理的。然而，其中一些请求需要数百甚至数千次使用单个模板，这给 I/O 和 CPU 等资源带来了压力。

解决这个问题的关键是 Django 的 [**缓存模板加载器**](https://docs.djangoproject.com/en/2.0/ref/templates/api/#django.template.loaders.cached.Loader) 。当其他加载程序发现应用程序请求的模板时，缓存的加载程序会将模板内容存储在内存中。下一次请求该模板时，缓存加载器不需要搜索匹配，而是直接从内存中返回模板内容，而不是输出到磁盘。我们的应用程序的这一特定部分的性能不再那么依赖于传入的请求数量，允许性能更可预测地扩展。

在 Django 1.10 之前的版本中或者在明确列出加载器的应用程序中启用缓存模板加载器是很容易的:

```
TEMPLATES = [{
    ...
    'loaders': [
        'some.custom.Loader',
        ('django.template.loaders.cached.Loader', [
            'django.template.loaders.app_directories.Loader',
            'django.template.loaders.filesystem.Loader',
        ])
     ]
}] 
```

只需在 Django 的`django.template.loaders.cached.Loader`包装器中包含您想要缓存的加载器！请注意，在 Django 1.10+中，默认设置仅在`DEBUG`为`False`时打开缓存加载器，因此要自己实现这一行为，您需要在`settings.py`中添加一些额外的逻辑。文档还警告只对带有线程安全模板标签的模板使用缓存加载器。

这个小小的加法能有多有效呢？您的里程数几乎肯定会有所不同，但我们可以向您展示它是如何影响我们上述的应用程序的。

[![](img/99b5d9f10689b3f4cbba5907b4789445.png)T2】](https://cdn-images-1.medium.com/proxy/1*4aSmBhTgyL9ZnyY9GBX43w.png)

[![](img/c57f66d69cfaff74f228e1459208bb1d.png)T2】](https://cdn-images-1.medium.com/proxy/1*KlR7zB9h-JDFTy7mTsC2uQ.png)

刚过上午 9 点，我们就在这个应用程序中启用了缓存模板加载器。新服务器上的平均 CPU 几乎是旧服务器的一半，总体噪音更小。同样，该应用程序的响应时间缩短了近 50%，并且更加稳定。由于这一改变，我们的服务器数量也减少了一台，提高了 33%!

如果您运行的是 Django <1.10 或 1.10+版本，并且已经明确列出了您的模板加载器，而没有使用缓存加载器，那么考虑一下它能为您带来哪些性能优势。

这个故事最初出现在 [*打造更聪明的*](https://medium.com/build-smarter) 上。

#### 你可能也会喜欢[我在姜戈](https://easyaspython.com/django/home)的其他建筑故事。

* * ***