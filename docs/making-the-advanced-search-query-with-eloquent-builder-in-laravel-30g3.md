# 用 Laravel 中的口才生成器制作高级搜索查询

> 原文：<https://dev.to/mohammadfouladgar/making-the-advanced-search-query-with-eloquent-builder-in-laravel-30g3>

在最近的项目中，我们需要一个先进的搜索系统。这个系统包括许多过滤器，需要一个灵活和可扩展的搜索系统。
我决定为这个系统实现一个包，你可以在 [GitHub](https://github.com/mohammad-fouladgar/eloquent-builder) 中看到它，并在你的项目中使用它。

## 有什么问题吗？

问题是您将会遇到一组过滤器，您必须检查这些过滤器，以便在查询中添加许多条件。

编写大量术语肯定会降低代码的可读性，减慢开发过程。

此外，您只能在相同的范围内使用过滤器和术语，并且它们不可重复使用。

## 无奈之解

你必须**重构**你的代码！

为了解决这个问题，你需要通过用**多态**替换你的许多条件来**重构**你的代码。

点击[此处](https://sourcemaking.com/refactoring/replace-conditional-with-polymorphism)了解更多关于这种设计模式的信息。

## 实际例子:

假设我们想要获得带有所请求参数的用户列表，如下所示:

```
http://dev.to/api/users/search?age_more_than=25&gender=male&has_published_post=true 
```

Enter fullscreen mode Exit fullscreen mode

请求的参数如下:

```
[ 
  'age_more_than' => '25',
  'gender' => 'male',
  'has_published_post' => 'true',
] 
```

Enter fullscreen mode Exit fullscreen mode

在一个**通用**实现中，将会出现以下代码: