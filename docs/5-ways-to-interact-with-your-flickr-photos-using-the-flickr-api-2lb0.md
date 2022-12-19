# 使用 Flickr API 与您的 Flickr 照片互动的 5 种方式

> 原文：<https://dev.to/jackhiston/5-ways-to-interact-with-your-flickr-photos-using-the-flickr-api-2lb0>

[![Flickr logo](img/cabeb9e762b0978c11798206875716ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IELEiREY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g7r7tjxf0mxxrdrn0eme.png) 
图片来源-[https://pix abay . com/en/Flickr-logo-brand-Yahoo-internet-881367/](https://pixabay.com/en/flickr-logo-brand-yahoo-internet-881367/)

当涉及许多不同的软件应用时，大量的图像数据是有用的。在机器学习中，如果没有训练材料，训练程序如何识别图像中的对象可能是一个很难解决的问题。Flickr 可以成为这种培训材料。Flickr API 是一种挖掘图像数据的绝妙方法。

要与 Flickr API 交互，您首先需要一个 API 键。api 密匙可以按照这里的指南生成:[https://www.flickr.com/services/api/misc.api_keys.html](https://www.flickr.com/services/api/misc.api_keys.html)。关于讨论 Flickr API 的有用资源，请看这个维基页面:【https://cloudinary.com/visualweb/display/IMMC/Flickr+API】T2。

在本文中，我将描述 Flickr API 对您的软件应用程序有益的五种方式。为了简单起见，我将在示例中使用 curl 命令。

# 搜索照片

你想和 Flickr 互动的主要原因是寻找图片。因此，与 Flickr API 交互的最基本方式是通过搜索照片。通过滚动以下 url 可以找到图像:

卷发[https://api.flickr.com/services/rest/?method = Flickr . photos . search&API _ key =](https://api.flickr.com/services/rest/?method=flickr.photos.search&api_key=)T3】text = test&format = rest

替换为您自己的 api 密钥，您将从本文的介绍中创建该密钥。这将返回一个 xml 格式的照片列表，其中包含标题、描述或带有值“test”的标签。然后，您可以使用响应中的照片 id 来查找图像数据。

除了文本之外，还有许多过滤器选项可供选择。要找到更多使用每个端点的方法，请访问官方文档。

# 获取热门照片

另一个与 Flickr API 交互的好方法是通过照片的流行度。Flickr API 可以为您提供他们网站上当前流行的图像数据，开箱即用。如果您的软件应用程序需要用户了解某些图像的最新情况，这将非常有用。

卷发[https://api.flickr.com/services/rest/?method = Flickr . photos . get popular&API _ key =](https://api.flickr.com/services/rest/?method=flickr.photos.getPopular&api_key=)T3】format = rest

# 添加照片评论

有时你的软件应用程序可能想要扫描 Flickr 提供的大量数据。在其他时候，您可能想要添加或删除内容。下一个我认为很有趣，因为它允许你以评论的形式在你的照片周围添加元数据。给定照片标识符，您可以对任何照片进行评论:

卷发[https://api.flickr.com/services/rest/?method = Flickr . photos . comments . add comment&API _ key =](https://api.flickr.com/services/rest/?method=flickr.photos.comments.addComment&api_key=)&photo _ id = 1&comment _ text = test+comment&format = rest

该命令允许我们在 id 为 1 的照片上添加“测试评论”的评论。如果您的软件应用程序必须与用户照片实时交互，这将非常有用。

# 地理位置数据

有时你需要访问你在 Flickr 上拍摄的所有地点。如果你想制作一张图片地图，把你所有的照片并置在一张地球地图上，这是很有用的。

卷发[https://api.flickr.com/services/rest/?method = Flickr . photos . getwithgeodata&API _ key =](https://api.flickr.com/services/rest/?method=flickr.photos.getWithGeoData&api_key=)&format = rest

# 检索图库照片

在 Flickr API 上与照片互动的另一种方式是使用图库。机器学习应用程序可能需要花时间接受训练，以理解如何对动物图像进行分类。在对照片进行分类后，应用程序可以将每张照片添加到图库中。这是一个应用程序如何将图像分类到特定主题的例子。

要列出用户创建的所有类别，可以使用以下命令:

卷发[https://api.flickr.com/services/rest/?method = Flickr . galleries . getlist&API _ key =](https://api.flickr.com/services/rest/?method=flickr.galleries.getList&api_key=)&user _ id = 1&format = rest

# 结论

在本文中，我给出了五种不同的方法，可以通过这些方法与 Flickr API 交互来操作照片数据。我希望您现在看到了 Flickr API 的多功能性，以及它可以用来帮助您构建一个具有丰富图像的应用程序。

数据来源是互联网的货币。数据是谷歌商业模式得以运行的原因，也是机器学习的方式。使用类似 Flickr API 的东西可以让你的应用程序进入一个知识的世界，帮助你的应用程序变得复杂和用户友好。