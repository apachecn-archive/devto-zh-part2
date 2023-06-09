# 引擎盖下的 LaravelNuxt

> 原文：<https://dev.to/skyrpex/laravelnuxt-under-the-hood-36lp>

在这篇文章中，我们将看到 laravel-nuxt 是如何工作的。如果不知道是什么，可以在这里查看
的介绍和教程[。](https://dev.to/skyrpex/create-a-spa-with-laravel-and-nuxt--54k)

基本上，有两种不同的模式:

*   [开发模式](#development-mode)
*   [生产模式](#production-mode)

## 发展模式

`laravel-nuxt dev`命令将启动两个相互交互的服务器:Nuxt 的和 Laravel 的开发服务器。

### Nuxt 服务器

将使用
在内部启动

```
nuxt dev --spa 
```

Enter fullscreen mode Exit fullscreen mode

这是我们将通过浏览器获取的服务器。有多种用途:

*   默认情况下监听端口 8000。
*   编译资产并启用[热模块替换](https://webpack.js.org/concepts/hot-module-replacement/)。
*   当访问一个特殊的开发 URL(默认为`/__laravel_nuxt__`)时呈现 HTML 页面。
*   代理到 Laravel 服务器的任何其他路由(不是`/__laravel_nuxt__`)。

代理到 Laravel 是强制性的，因为我们不会使用 Nuxt 处理头或 cookies。

### Laravel 服务器

Laravel 服务器将使用
在内部启动

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

我们不应该在这里打电话。它将:

*   默认情况下监听 8001 端口。
*   照常为您的路线提供服务(应该只包含 API 路线)。
*   提供一个后备路由，无论何时进行非 AJAX 调用，它都将呈现`/__laravel_nuxt__`页面。
    *   这由`Pallares\LaravelNuxt\Controllers\NuxtController::class`控制器处理。
    *   这应该是你唯一的网络路径。

通过从 Nuxt 到 Laravel 的代理，我们让我们的应用程序将任何头和 cookies(如`laravel_session`)附加到由 Nuxt 呈现的 HTML 中。

## 生产模式

`laravel-nuxt build`命令将会执行:

```
nuxt build 
```

Enter fullscreen mode Exit fullscreen mode

它会将你所有的前端资产编译到`public/_nuxt`目录，这个目录通常包含一些 JS 文件和 CSS 文件，以及一个由`Pallares\LaravelNuxt\Controllers\NuxtController::class`控制器提供服务的`index.html`文件。

> 任何对 Laravel 服务器的非 AJAX 请求(不匹配任何现有路由)都将呈现您的 HTML 页面。您可能已经注意到，您永远不会从 Laravel 获得 404 响应(但是您仍然可以使用 Nuxt 呈现 404 页面)。

执行此命令后，应用程序将准备好部署。也可以使用`php artisan serve`和打开`http://localhost:8000/`来测试。

如果你还有任何疑问，不要犹豫，在这里写一个问题！问候。