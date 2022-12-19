# 如何从自定义插件中扩展 WP REST API:第 3 部分

> 原文：<https://dev.to/upnrunnhq/how-to-extend-wp-rest-api-from-your-custom-plugin-part-3-24m2>

**这是“如何从你的定制插件扩展 WP REST API”系列文章的第 3 篇，共 5 篇**。如果您错过了之前的教程，请查看以下列表:

1.  [如何从你的自定义插件中扩展 WP REST API(第 1 部分)](https://upnrunn.com/blog/2018/02/extend-wp-rest-api-custom-plugin-part-1/)
2.  [如何从你的自定义插件中扩展 WP REST API(第二部分)](https://upnrunn.com/blog/2018/02/extend-wp-rest-api-custom-plugin-part-2/)

在前面的教程中，我们已经创建了一个基本插件，我们可以在其中编写代码，并学习如何通过修改响应来扩展 REST API。

WordPress REST API 有默认的路由和端点。它还允许开发人员添加自定义路由和端点。太好了。在本教程中，我们将学习如何添加自定义端点。

最近，我们发布了一个叫做 [WP 餐馆列表](https://wordpress.org/plugins/wp-restaurant-listings/)的插件，你可以用它来给你的 WordPress 站点添加餐馆列表功能。这个插件没有内置的自定义端点来通过 WP REST API 访问你的餐馆站点的数据。因此，我们将在插件中添加自定义端点，通过轻松发送 HTTP 请求来访问数据。

我们将使用控制器模式，因此我们可以轻松地组织我们的代码。你可以在这里了解更多关于[控制器模式的信息。](https://developer.wordpress.org/rest-api/extending-the-rest-api/adding-custom-endpoints/#the-controller-pattern)

首先，在我们在之前的教程中创建的插件的`includes`文件夹中创建一个名为`class-rest-tutorial-restaurant-endpoint.php`的文件，内容如下。

我们还没有登记路线。为了注册新路线，我们将使用`register_rest_route`功能。我们将传入三个参数:名称空间、我们想要的路径和这个函数的选项(多个方法的数组的数组)。在`register_routes method`里面，我们来添加下面的代码。

我们使用 WP_REST_Server 常量来设置方法，这是一种常见的做法。所以我们用 WP_REST_Server::READABLE 来设置方法为' GET '。当用户向注册的 route `get_items`发送‘GET’请求时，该类的方法将被调用。让我们添加那个方法。

我们还需要添加`get_items_permissions_check`方法。为了让所有用户都能阅读餐馆，我们需要确保`get_items_permissions_check`返回 true。

接下来，我们将添加一个返回参数数组的方法。我们新注册的路由将在向 WP REST API 发送“GET”请求时接受这些参数。

最后，我们需要添加另一个方法，该方法将准备餐馆数据并作为对象返回。

现在，我们班`REST_TUTORIAL_Restaurnt_Endpoint`准备做一些很酷的事情。我们还没有包括这个类，所以我们需要先包括它。在`rest-api-tutorial.php`文件中添加以下代码。

我们正在检查是否安装了 [WP_Restaurant_Listings](https://wordpress.org/plugins/wp-restaurant-listings/) 插件。如果插件没有安装并且没有激活，我们将不会包含`REST_TUTORIAL_Restaurnt_Endpoint`类。因此，需要安装并激活 [WP_Restaurant_Listings](https://wordpress.org/plugins/wp-restaurant-listings/) 插件才能运行。

我们做得很棒。现在，如果我们向`wp-json/rest-tutorial/v1/restaurants`发送“GET”请求，我们将得到简单 JSON 格式的餐馆，如下图所示。

[![](../Images/0b7e7c9486fc2a81f3778e2531a7318e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RznA1JvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/upnrunn.com/wp-content/uploads/2018/04/restaurants_data.png%3Fresize%3D1130%252C602%26ssl%3D1)

类似地，我们可以注册另一条路线来获得单个餐馆项目。我们将让路线匹配任何带有`/restaurants/{id}`的内容，其中{id}是一个整数。检查以下代码。

我们再次使用`get_items_permissions_check`让所有用户都能阅读餐厅。现在，是时候通过添加`get_item`方法来检索餐馆了。

现在，您可以通过向`wp-json/rest-tutorial/v1/restaurants/{id}`端点发送 HTTP“GET”请求来轻松获取餐馆数据。

在接下来的教程中，我将向您展示如何使用 POST、PUT、DELETE 方法注册 route 来更新或删除餐馆。

帖子[如何从你的自定义插件扩展 WP REST API:第 3 部分](https://upnrunn.com/blog/2018/04/how-to-extend-wp-rest-api-from-your-custom-plugin-part-3/)最早出现在 [upnrunn | Build &部署强大的应用](https://upnrunn.com)。