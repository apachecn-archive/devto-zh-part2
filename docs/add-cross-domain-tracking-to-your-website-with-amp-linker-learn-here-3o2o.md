# 使用 AMP 链接器将跨域跟踪添加到您的网站-在此了解

> 原文：<https://dev.to/scottmathson/add-cross-domain-tracking-to-your-website-with-amp-linker-learn-here-3o2o>

您正在使用加速移动网页(AMP)吗？

如果是这样，您的分析会话数据可能会有偏差和不准确。

这篇文章展示了如何轻松地添加跨域分析跟踪到 AMP HTML 布局。以及跨域跟踪的背景、AMP，AMP 的缓存 URL 和生态系统中的 Google Analytics 等等。利用 AMP 链接器跟踪用户从谷歌的 AMP 缓存域到其他域的旅程。

**目录**:

*   跨域跟踪和倾斜数据
*   谷歌分析和 AMP
    *   向 AMP 添加 Google Analytics
    *   Google AMP 缓存
*   为跨域跟踪添加 AMP 链接器

## 跨域跟踪和偏斜数据

没有什么比拥有臃肿、不准确或缺失的分析和网络流量数据更糟糕的了。在分析中跟踪会话是通过本地存储或 cookies 来完成的，这些是基于一个域一个域的。

域“a”无法轻松访问域“b”上的 cookie，随着现代浏览器不断添加新的隐私和安全功能，其中一些功能禁止第三方 cookie，您的分析和数据可能会出现偏差。

在 AMP 实例中，当用户从一个域移动到另一个域时，基于单个会话的跟踪可以在您的 Google Analytics 数据中显示为多个会话。这可能会导致更多的用户和会话数据以及会话中较低的页面视图数据。

AMP 在 Google 自己的 AMP 缓存域上为用户提供内容(下面了解更多关于 AMP 缓存的信息)。你的 AMP HTML 模板可能有链接回到你自己的域名，无论是通过主导航，页脚链接，内容本身的另一个 CTA，还是在谷歌的缓存浏览器本身。当点击时，这些链接会将用户带到你的域名，远离谷歌的 AMP 缓存 URL，这就是你的分析变得不准确的地方。

对于那些使用 AMP HTML 和 Google Analytics 的人来说，你现在可以非常容易地设置跨域跟踪，并在 AMP 缓存和你自己的域中跟踪你的用户，请继续阅读！

## 谷歌分析和 AMP

AMP 需要使用独特的标记和元素，将 Google Analytics 功能引入 AMP HTML 是通过一个`amp-analytics`组件/元素和一个包含在`<head>`中的脚本来完成的。这篇简短的文章的唯一目的是介绍这个:AMP 链接器的一个具体特性。我将简单介绍一下通过`amp-analytics`添加谷歌分析，因为这是一个先决条件。

只是在寻找新的跨域跟踪放大器链接器？继续。

### 将谷歌分析添加到 AMP

如前所述，将 Google Analytics 跟踪添加到 AMP 需要在模板的`<head>`中添加一个特定的脚本来引入这个独特的`amp-analytics`组件。

在`</head>` :
收盘前补充一下吧

```
<script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
<!doctype html>
<html amp>
   <head>
      <meta charset="utf-8">
      Hello world
      <link rel="canonical" href="https://example.com/blog/hello-world/" />
      <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">
      <script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script>
   </head>
   <body>
      <p>Hello world!</p>
   </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

**SEO 旁注** : *请确保你已经将你的域名设为规范*。

一旦包含了这个脚本，我们现在可以在模板的`<body>`中添加我们的`amp-analytics`元素。为了确保我们利用 AMP 内置的谷歌分析支持，我们将在类型属性中添加`googleanalytics`。

```
<amp-analytics type="googleanalytics">
    ...
</amp-analytics> 
```

Enter fullscreen mode Exit fullscreen mode

AMP 目前只支持某些谷歌分析跟踪。为了在您的 AMP 帖子/页面上实现基于会话的浏览量，让我们加入以下 JSON:

```
<amp-analytics type="googleanalytics">
   <script type="application/json">
      {
        "vars": {
         "account": "UA-XXXXX-Y"
        },
        "triggers": {
         "trackPageview": {
            "on": "visible",
            "request": "pageview"
         }
        }
      }
   </script>
</amp-analytics> 
```

Enter fullscreen mode Exit fullscreen mode

**注意** : *请确保您已经设置了唯一的谷歌追踪“UA”id*。

现在，我们在谷歌分析中跟踪我们的 AMP 内容的浏览量！

### 谷歌放大器缓存

> Google AMP 缓存是一个基于代理的内容交付网络，用于交付所有有效的 AMP 文档。它获取 AMP HTML 页面，缓存它们，并自动提高页面性能。当使用 Google AMP 缓存时，文档、所有 JS 文件和所有图像都从同一个来源加载……—ampproject.org

正如本文前面提到的，这就是跨域跟踪的问题所在。你的 AMP 内容通过他们自己的 URL(看起来像 google.com/amp/s/example.com/blog/hello-world/)提供给你的用户。

您的 AMP 内容可能会链接到您自己的域，当用户从 Google 的 AMP 缓存 URL 导航到您自己的域时，这可能会扭曲数据，因为这种跨域之旅可能会创建新的会话。

AMP 的缓存 CDN 非常强大，内置了一些很好的功能，如预验证系统，确保帖子或页面能够正常工作，以及其他功能。

不过，我们需要添加 AMP 链接器来确保分析中的数据准确。

## 添加 AMP 链接器进行跨域跟踪

本文中给出的这个问题的解决方案是在您的 AMP 模板中添加一个非常简单的。AMP Linker 是加速移动页面中新发布的功能，它可以确保跨域会话不会膨胀。

当用户点击远离 AMP 缓存 URL 时，它将带有唯一 id 的参数附加到域“b”的 URL。领域“b”你有谷歌分析，可以很容易地解析这个参数，并转化为第一方 cookie。

该 url 将类似于:`https://example.com/about/?_gl=1*1vlvis7*_ga*U0Rt...`

我们将启用 AMP 链接器，并通过在您的 AMP 模板
的`<body>`内的谷歌分析浏览量跟踪元素中包含`"linkers"`并启用它来确保您的数据的准确性

```
<amp-analytics type="googleanalytics">
   <script type="application/json">
      {
        "vars": {
         "account": "UA-XXXXX-Y"
        },
        "linkers": {
         "enabled": true
        },
        "triggers": {
         "trackPageview": {
            "on": "visible",
            "request": "pageview"
         }
        }
      }
   </script>
</amp-analytics> 
```

Enter fullscreen mode Exit fullscreen mode

现在全齐了:

```
<!doctype html>
<html amp>
   <head>
      <meta charset="utf-8">
      Hello world
      <link rel="canonical" href="https://example.com/blog/hello-world/" />
      <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">
      <script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script>
   </head>
   <body>
      <amp-analytics type="googleanalytics">
         <script type="application/json">
            {
                "vars": {
                 "account": "UA-XXXXX-Y"
                },
                "linkers": {
                 "enabled": true
                },
                "triggers": {
                 "trackPageview": {
                    "on": "visible",
                    "request": "pageview"
                 }
                }
            }
         </script>
      </amp-analytics>
      <p>Hello world!</p>
   </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

**注意** : *再次提醒，请确保您已经设置了唯一的谷歌追踪“UA”id*。

非常感谢您查看这篇文章！