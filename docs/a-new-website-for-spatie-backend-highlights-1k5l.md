# Spatie 的新网站:后端亮点

> 原文：<https://dev.to/freekmurze/a-new-website-for-spatie-backend-highlights-1k5l>

四年来，我们第一次完全重新设计了我们公司的网站。我们今天发布了它。该网站是一个简单的 Laravel 应用程序，有一些技术细节。诚然，我们也开源了这款应用，你可以在 GitHub 的[回购中找到代码。](https://github.com/spatie/spatie.be)

在这篇博文中，我想让你带你参观一下幕后。

[![homepage](img/5842e246165f48288d4212a84dcb814a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dDQyd7NI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/homepage.png)

## GitHub 集成

在过去的几年里，我们越来越多地参与到开源社区中。我们已经创建了 100 多个包，下载量超过 1500 万次。在我们的网站上，我们专门为[留出了一整块区域](https://spatie.be/open-source)来突出我们的努力。我们列出了我们所有的开源包和项目。

[![Packages](img/dc5c71e6e1b6b1c2fc4a70bb2274b551.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NQe1-MCH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/packages.png)

我们使用 GitHub API 将公共存储库中的大部分信息导入数据库。导入是通过[这个控制台命令](https://github.com/spatie/spatie.be/blob/fa75b6acde82be56fad1ddc971f930a7ac24a65b/app/Console/Commands/ImportGitHubRepositories.php)完成的，这个控制台命令是[计划每天运行的](https://github.com/spatie/spatie.be/blob/master/app/Console/Kernel.php#L16)。

GitHub 没有任何关于下载次数的信息。这就是为什么[有一个单独的命令](https://github.com/spatie/spatie.be/blob/master/app/Console/Commands/ImportPackagistDownloads.php)让[向打包 API](https://github.com/spatie/packagist-api) 查询下载号。

涉足开源有[很多好处](https://freek.dev/the-story-behind-our-open-source-efforts#the-benefits-of-creating-packages)。我们认为降低那些想开始为开源做贡献的人的门槛是很重要的。这就是为什么我们在回购上也列出了一些简单的问题。这些是完美的

[![Issues](img/a98513429f6c0ba5b46778f049d1be58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qrJfDc1s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/issues.png)

那些容易的问题，由问题上的特定标签识别，也通过 GitHub API 导入。下面是处理简单问题导入的命令。请注意，任何新的问题也是[自动推文](https://github.com/spatie/spatie.be/blob/a3854d7e9f1a7a2a151455fdaeae188441ee1215/app/Console/Commands/ImportGitHubIssues.php#L58-L64)。

如果我们得不到社区的任何帮助，我们的开源产品就不会那么好。我们感谢任何人，我们用 PR 改进了我们的代码，为我们修复了一个错别字，提出了一个问题来问我们或报告了一个问题。Symfony 最近开始感谢他们网站上的随机贡献者。我们认为这是一个很好的姿态，所以我们也这样做了。下面是[命令](https://github.com/spatie/spatie.be/blob/master/app/Console/Commands/ImportRandomContributor.php)，它导入一个随机贡献者。

[![Contributor](img/024ccecd5787672f69657aa33d67e09f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vj-ukqeC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/contributor.png)

## 显示响应图像

我们所有的开源软件都有一个特定的许可证，叫做 postcardware。这意味着如果我们的代码进入你的生产环境，你需要从你的家乡给我们寄一张明信片。对我们来说，收到来自全球各地的明信片是一件很酷的事。我们在我们网站的这个专用页面上分享我们在[收到的每一张明信片。](https://spatie.be/open-source/postcards)

[![Postcards](img/4d2b99d758a3dfc27031e898e18d25a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xSffeEMO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/postcards.png)

我们现在有大约 200 张明信片展示在那一页上。当我用手机给明信片拍照时，文件大小大约是 4 到 6 MB。如果我们直接使用这些图像，页面大小大约为 1GB。

你可能会想，只是创建一个缩略图版本，但我们走得更远。我们使用[我们自己的媒体库](https://docs.spatie.be/laravel-medialibrary)为这些明信片生成相应的图像。这意味着如果你通过一个小的移动设备访问网站，你的浏览器将会下载比你在一个大显示器设备上工作时更小的图片。我们利用[的`srcset`属性](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)来完成这个行为。

第一次访问明信片页面时，您可能会注意到明信片的模糊版本。这是一种叫做渐进图像加载的技术。我们在页面中嵌入一个模糊的 svg 版本的明信片，这样浏览器就可以立即显示出来。当它被下载的时候，浏览器会用一个真实的图像替换它。

以下是媒体库为响应图像生成的标记。

```
<img srcsetimg/medialibrary/539/responsive-images/open-source ___medialibrary_original_2400_2605.jpg 2400wimg/medialibrary/539/responsive-images/open-source___ medialibrary_original_2007_2178.jpg 2007wimg/medialibrary/539/responsive-images/open-source ___medialibrary_original_1680_1823.jpg 1680wimg/medialibrary/539/responsive-images/open-source___ medialibrary_original_1405_1525.jpg 1405wimg/medialibrary/539/responsive-images/open-source ___medialibrary_original_1176_1276.jpg 1176wimg/medialibrary/539/responsive-images/open-source___ medialibrary_original_983_1066.jpg 983wimg/medialibrary/539/responsive-images/open-source ___medialibrary_original_823_893.jpg 823wimg/medialibrary/539/responsive-images/open-source___ medialibrary_original_688_746.jpg 688wimg/medialibrary/539/responsive-images/open-source ___medialibrary_original_576_625.jpg 576wimg/medialibrary/539/responsive-images/open-source___ medialibrary_original_482_523.jpg 482w, data:image/svg+xml;base64,PCFET0NUWVBFIHN2ZyBQVUJMSUMgIi0vL1czQy8vRFREIFNWRyAxLjEvL0VOIiAiaHR0cDovL3d3dy53My5vcmcvR3JhcGhpY3MvU1ZHLzEuMS9EVEQvc3ZnMTEuZHRkIj4KPHN2ZyB2ZXJzaW9uPSIxLjEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiIHg9IjAiCiB5PSIwIiB2aWV3Qm94PSIwIDAgMjQwMCAyNjA1Ij4KCTxpbWFnZSB3aWR0aD0iMjQwMCIgaGVpZ2h0PSIyNjA1IiB4bGluazpocmVmPSJkYXRhOmltYWdlL2pwZWc7YmFzZTY0LC85ai80QUFRU2taSlJnQUJBUUVBWUFCZ0FBRC8vZ0E3UTFKRlFWUlBVam9nWjJRdGFuQmxaeUIyTVM0d0lDaDFjMmx1WnlCSlNrY2dTbEJGUnlCMk9EQXBMQ0J4ZFdGc2FYUjVJRDBnT1RBSy85c0FRd0FEQWdJREFnSURBd01EQkFNREJBVUlCUVVFQkFVS0J3Y0dDQXdLREF3TENnc0xEUTRTRUEwT0VRNExDeEFXRUJFVEZCVVZGUXdQRnhnV0ZCZ1NGQlVVLzlzQVF3RURCQVFGQkFVSkJRVUpGQTBMRFJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVUvOEFBRVFnQUlnQWZBd0VSQUFJUkFRTVJBZi9FQUI4QUFBRUZBUUVCQVFFQkFBQUFBQUFBQUFBQkFnTUVCUVlIQ0FrS0MvL0VBTFVRQUFJQkF3TUNCQU1GQlFRRUFBQUJmUUVDQXdBRUVRVVNJVEZCQmhOUllRY2ljUlF5Z1pHaENDTkNzY0VWVXRId0pETmljb0lKQ2hZWEdCa2FKU1luS0NrcU5EVTJOemc1T2tORVJVWkhTRWxLVTFSVlZsZFlXVnBqWkdWbVoyaHBhbk4wZFhaM2VIbDZnNFNGaG9lSWlZcVNrNVNWbHBlWW1acWlvNlNscHFlb3FhcXlzN1MxdHJlNHVickN3OFRGeHNmSXljclMwOVRWMXRmWTJkcmg0dVBrNWVibjZPbnE4Zkx6OVBYMjkvajUrdi9FQUI4QkFBTUJBUUVCQVFFQkFRRUFBQUFBQUFBQkFnTUVCUVlIQ0FrS0MvL0VBTFVSQUFJQkFnUUVBd1FIQlFRRUFBRUNkd0FCQWdNUkJBVWhNUVlTUVZFSFlYRVRJaktCQ0JSQ2thR3h3UWtqTTFMd0ZXSnkwUW9XSkRUaEpmRVhHQmthSmljb0tTbzFOamM0T1RwRFJFVkdSMGhKU2xOVVZWWlhXRmxhWTJSbFptZG9hV3B6ZEhWMmQzaDVlb0tEaElXR2g0aUppcEtUbEpXV2w1aVptcUtqcEtXbXA2aXBxckt6dExXMnQ3aTV1c0xEeE1YR3g4akp5dExUMU5YVzE5aloydUxqNU9YbTUranA2dkx6OVBYMjkvajUrdi9hQUF3REFRQUNFUU1SQUQ4QSsyckh3NUtnUEJyMCtjOGZrWmFYdzI1Y0VxZUtibVhHTFJEZGVISFpqZ0dzSEk3SUt4V1BoaHovQUFtbGMzUjYxRG95Um43b3JEbU1lUWsvc2xNL2RGSE9QbEd0b3lNZnVpbHpGcFdHL3dCaUovZEZITU0zZkxyTW9YeTZBRTh1Z0E4dWdDU2dZVUFGQUJRQVVBRkFCUUFVQWYvWiI+Cgk8L2ltYWdlPgo8L3N2Zz4= 32w" sizes="1px" srcimg/medialibrary/539/open-source.jpg" width="2400"> 
```

关于这一切如何工作的更多背景信息可以在媒体库文档的相关页面上找到。

## 避开饼干

我们认为 cookie 警告很难看，会让用户体验更差。为了避免在我们的网站上放置这样的警告，我们决定我们的网站不应该设置任何 cookies。如果你想知道如何避免在 laravel 应用程序中设置任何 cookies，请阅读 Dieter Stinglhamber 的这篇博文。

[![Cookies](img/e83879608def32873f92ae4d3aa6a0ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Em_FrHlE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/cookies.png)

我们的网站有一个非常小的管理部分来上传明信片。因为为了让 [csrf 保护](https://laravel.com/docs/5.6/csrf)在那个表单上工作，我们需要设置一个会话 cookie。这就是为什么我们[在访问那个页面时偷偷改变](https://github.com/spatie/spatie.be/blob/1412b11e84f2191ea2daba05d057ece2652b5a05/app/Providers/SessionServiceProvider.php)会话驱动。

[![Add postcard](img/78669037b07eb431aa39a0f1bbb8c604.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hVRKrSGD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/add-postcard.png)

## 采集访客统计

对于我们的大多数客户网站，我们使用谷歌分析。因为谷歌分析依赖于设置 cookies，所以我们不能在 spatie.be 上使用它。我们使用 [Matomo](https://matomo.org) 代替谷歌分析。这是一个免费的、自托管的开源工具，可以收集网站访问者的信息。在服务器上安装很容易[。为了收集关于网站访问者](https://matomo.org/docs/installation/#the-5-minute-matomo-installation)[的数据，每个页面上都包含了一个小的 JavaScript 片段](https://github.com/spatie/spatie.be/blob/master/resources/views/layout/partials/analytics.blade.php)。它可以[在不设置任何 cookie](https://matomo.org/faq/general/faq_157/)的情况下运行。

[![Matomo](img/ba12a147f083203432db81e22cd8a9e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d3llbEQF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/new-spatie-be/matomo.png)

有趣的事实:“matomo”是一个日语单词，可以翻译为“诚实”或“体面”。

## 通过缓存响应提高性能

在没有任何缓存的情况下，这个应用程序已经非常快了。在我的 MacBook Pro(从 2015 年开始)上，响应时间从 150 到 200 毫秒不等。在我看来，这是可以接受的，但我们可以做得更好。

我们最受欢迎的软件包之一是 [laravel-responsecache](https://github.com/spatie/laravel-responsecache) 。通过缓存整个响应，它可以大大提高任何 laravel 应用程序的速度。如果你需要更多关于它如何工作的信息，请阅读[这篇博文](https://freek.dev/speed-up-a-laravel-app-by-caching-the-entire-response)。安装软件包后，平均响应时间约为 50 毫秒。

我们采用了一种简单的方法来使缓存无效。每当数据库中发生变化，我们就清除 responsecache。这发生在[基础雄辩模型](https://github.com/spatie/spatie.be/blob/1412b11e84f2191ea2daba05d057ece2652b5a05/app/Models/Model.php#L12-L27)中。

## 关闭思绪

我们的新网站主要是由威廉·范·博克斯特设计的。在开发过程中，我们公司的每个人都做出了贡献，这是一个团队的努力。我们对结果很满意，但我们不会就此止步。在接下来的几个月里，我们会添加更多的内容，并做一些调整。

请务必查看我的同事 Willem 的客座博文，了解更多关于我们新网站前端发生的事情。

你觉得我们的新网站怎么样？请在下面的评论中告诉我们！