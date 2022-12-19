# 在 WordPress 中构建你自己简单的延迟加载功能

> 原文：<https://dev.to/alexmacarthur/build-your-own-simple-lazy-loading-functionality-in-wordpress-g1d>

当你想在你的 WordPress 应用程序中加入任何给定的特性时，很少会缺少第三方插件来实现它。但是有时候，不管你是想避免不熟悉的第三方插件带来的不可避免的复杂性，还是出于其他原因，你可能会觉得有必要尝试自己开发这个特性。延迟加载图像可能是这些特性之一，幸运的是，自己设置它并开始获得性能优势是相当简单的。

我在这里假设你对你的 WordPress 应用程序有完全的开发支配权，并且你相对熟悉 [WordPress 插件 API](https://codex.wordpress.org/Plugin_API)——这个特色的钩子系统使 WordPress 开发如此灵活。虽然我们*可以*通过把所有东西放在你的主题的`functions.php`文件中来设置无插件的延迟加载，但我们将推出我们自己的极其基础的插件。这通常是一个比仅仅使用你的主题更好的主意——它将更好地分离关注点，你将能够在不损失功能的情况下切换主题，并且它将很容易随时切换，这在调试时特别有帮助。

*知道这一点:*我不会深入探讨一个制作精良的 WordPress 插件的哲学。自己去尝试和争论那些爵士乐。我只是想告诉你用几行代码就能制作一个懒惰加载插件的基本框架。让我们开始吧。

## 设置你的插件

在插件目录中，创建一个`simple-lazy-loading`目录和一个名为`simple-lazy-loading.php`的文件。打开文件，并在顶部放置以下内容:

```
<?php
/*
* Plugin Name: Simple Lazy Loading
*/ 
```

Enter fullscreen mode Exit fullscreen mode

最起码，你现在有了一个可以在 WordPress admin 中激活的插件。去那里把它打开。

[![Activate Simple Lazy Loading Plugin](../Images/2a8ee9a84678c5f8b1de503f03cf2905.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e94ghUR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://macarthur.me/static/bbde81e9993542c110f7c5ac7d95f362/a296c/simple-lazy-loading.jpg)

完成后，打开刚刚创建的文件，我们就可以开始了！

## 让我们变懒吧

你可以延迟加载很多东西，但是在这里，我们只关注延迟加载存储在你的 WordPress 数据库中的图片，这些图片是作为帖子或页面内容被发布的。基本上，你可以通过 WordPress admin 上传和使用图片。

为了做到这一点，让我们使用 [Lozad](https://github.com/ApoorvSaxena/lozad.js) 作为我们的惰性加载 JavaScript 库。它没有 jQuery 依赖性，看起来维护得非常积极，并且它利用了[交叉点观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) ，这将为您带来更好的性能，尤其是随着未来浏览器支持的增长。与此同时，你可以加入一个[聚划算](https://github.com/w3c/IntersectionObserver/tree/master/polyfill)。

### 让 Lozad 站起来

首先，将 Lozad 库入队。我们还想为交叉点观察器 API 添加官方的 W3C polyfill。我们将这两个脚本放在页脚中，因为我们是负责任的 web 开发人员，不希望这些脚本阻碍页面呈现。

```
add_action('wp_enqueue_scripts', function () {
   wp_enqueue_script( 'intersection-observer-polyfill', 'path-to-intersection-observer.js', [], null, true );
   wp_enqueue_script( 'lozad', 'https://cdn.jsdelivr.net/npm/lozad@1.3.0/dist/lozad.min.js', ['intersection-observer-polyfill'], null, true );
}); 
```

Enter fullscreen mode Exit fullscreen mode

**接下来，初始化 Lozad。**开箱即用,“lozad”类用于监视要惰性加载的元素，但是因为我们无论如何都要修改一下配置，所以让我们将其更改为更通用的“惰性加载”类。另外，请注意，我在这里只使用了`wp_add_inline_script`，因为我们编写的 JS 数量很少。您可以将它放在一个单独的 JS 文件中——只要确保它在`lozad`加载后执行。

```
add_action('wp_enqueue_scripts', function () {
    wp_enqueue_script( 'intersection-observer-polyfill', 'path-to-intersection-observer.js', [], null, true );
    wp_enqueue_script( 'lozad', 'https://cdn.jsdelivr.net/npm/lozad@1.3.0/dist/lozad.min.js', ['intersection-observer-polyfill'], null, true );
    wp_add_inline_script( 'lozad', '
        lozad(".lazy-load").observe();
    ');
}); 
```

Enter fullscreen mode Exit fullscreen mode

*整齐！但是如果我不想通过类来定位元素呢？*没问题！您可以传入一个`NodeList`，而不是定义一个要监视的类。在这种情况下，我们的`wp_add_inline_script`部分看起来更像这样:

```
wp_add_inline_script( 'lozad', '
    var myElements = document.querySelectorAll("#main img");
    lozad(myElements).observe();
'); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们以这种方式进行设置，Lozad 将会寻找任何属于`#main`的子图像。

**我们的下一步是将我们的目标类添加到我们想要延迟加载的每个图像中，并将`src`属性更改为`data-src`。**这将阻止浏览器默认加载图像(这会阻止页面的其余部分呈现)，只有当`lozad`将`data-src`变回`src`时才会这样做，这将在用户滚动到这些图像时发生。

对于 WordPress 数据库之外的自定义页面上的任何图像，设置我们需要的属性是非常容易的。手动做就行了。不幸的是，处理存储在数据库中的图像需要我们通过`the_content`钩子过滤一切。我不喜欢像这样过滤帖子内容的想法，但是如果你像你应该做的那样正确地缓存你的网站，这不是一个问题，至少在性能方面。

```
add_filter('the_content', function ($content) {
    //-- Change src/srcset to data attributes.
    $content = preg_replace("/<img(.*?)(src=|srcset=)(.*?)>/i", '<img$1data-$2$3>', $content);

    //-- Add .lazy-load class to each image that already has a class.
    $content = preg_replace('/<img(.*?)class=\"(.*?)\"(.*?)>/i', '<img$1class="$2 lazy-load"$3>', $content);

    //-- Add .lazy-load class to each image that doesn't already have a class.
    $content = preg_replace('/<img((.(?!class=))*)\/?>/i', '<img class="lazy-load"$1>', $content);

    return $content;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们用滤波器做了三件独立的事情。

*   将每幅图像上的`src`和`srcset`属性更改为`data-src`和`data-srcset`。
*   为每个图像添加一个`lazy-load`类，这样 Lozad 就可以正确地定位我们想要延迟加载的图像。
*   为每个还没有附加类的图像添加一个`lazy-load`类。

从技术上来说，你已经完成了。现在，每一个你从 WordPress 编辑器中填充了内容的页面，默认情况下图片应该*而不是*被加载，取而代之的是只有当它们进入视图时才被加载。但这可能还不够好。

### 让我们把它变得更漂亮

如果图像只在进入视图时才加载，当这种情况发生时，您会在页面上看到一个难看的弹出窗口。为了让一切以更美好的方式发生，我们有一些选择。

**我们来调整一下`rootMargin`，这样当*到*的时候图像就开始加载了。**通过这种方式，他们甚至可以在用户到达之前就做好准备，一切都将变得更加无缝。回到我们的`wp_add_inline_script`呼叫:

```
wp_add_inline_script( 'lozad', '
    lozad(".lazy-load", { 
        rootMargin: "300px 0px"
    }).observe();
'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当图像在 300 像素内可见时，Lozad 将触发它们开始加载。如果您认为合适，可以随意调整该值。

我们还可以在图像加载后添加一个类，这样它们会以一种超级漂亮、壮观的方式淡出。通过添加一个`loaded`回调:
来实现

```
wp_add_inline_script( 'lozad', '
    lozad(".lazy-load", { 
        rootMargin: "300px 0px", 
        loaded: function (el) {
            el.classList.add("is-loaded");
        }
    }).observe();
'); 
```

Enter fullscreen mode Exit fullscreen mode

并添加一些 CSS 来隐藏图像，直到它们被完全加载，然后允许它们淡入。注意，如果您添加了下面的 CSS，您将需要在一个样式表中或者通过一些其他的方法将它排队。

```
.lazy-load {
    transition: opacity .15s;
    opacity: 0;
}

.lazy-load.is-loaded {
    opacity: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

嘣。现在，每张图片都有一个`0`的`opacity`，直到它们被载入，这时`is-loaded`会淡入它们。

### 做好准备

在 WordPress 的 5.0 版本中，我们将看到备受期待的 block editor 推出( [Gutenberg](https://github.com/WordPress/gutenberg) )。这种惰性加载设置对 Gutenberg 有效吗？

**回答:是的。**

新的编辑器仍将依靠`the_content`返回数据库内容，这意味着我们在这里所做的一切仍将保持原样。然而，Gutenberg 将稍微改变添加到图像中的类。例如，图像块将使用附加了对齐类的`<figure>`标签来包装图像，而不是在图像本身上。所以，如果你不像这里显示的那样通过类来定位图像，而是使用嵌套选择器或其他东西，请注意这可能会对你产生什么影响。没什么大不了的。

### 很快，这一切可能都没有意义了

延迟加载是一个很好的、负责任的实现方式，但是，这一切可能会在几年后开箱即用，完全由浏览器自己管理。Google Chrome 已经朝着这个方向发展了。所以，要注意。很快，我刚才给你看的所有东西可能都毫无意义，我需要在这篇文章的顶部放一个悲伤的免责声明，你读这篇文章可能只是在浪费时间。

希望这有所帮助！