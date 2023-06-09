# 如何从自定义插件中扩展 WP REST API:第 4 部分

> 原文：<https://dev.to/upnrunnhq/how-to-extend-wp-rest-api-from-your-custom-plugin-part-4-4elh>

**这是“如何从你的定制插件扩展 WP REST API”系列的第 4 篇，共 5 篇**。如果您错过了之前的教程，请查看以下列表:

在上一个教程中，我们已经创建了一个控制器类来获取餐馆项目和单个餐馆项目。控制器类有助于轻松处理端点的功能。您可以通过阅读关于控制器类的 REST API 手册来了解更多关于控制器类的信息。

在本教程中，我们将编写一些控制器方法来更新或删除单个餐馆项目。确保你已经安装了 WP 餐厅列表和我们在上一个教程中创建的自定义插件。

首先，我们将在现有的路由中添加一个删除方法，在这里我们可以发送 HTTP 请求来删除一个餐馆项目。

我们刚刚添加的 delete 方法接受一个参数来选择是否绕过 trash 并强制删除。然而，我们还必须添加两个缺失的方法`delete_item`和`delete_item_permissions_check`。

现在，我们的`delete_item`方法已经准备好了，它负责删除一个餐馆项目。我们将在`delete_item_permissions_check`方法中编写逻辑来检查给定的请求是否有权删除餐馆项目。

您已经注意到我们正在使用`get_restaurant`方法，让我们也添加这个方法。

最后，万事俱备，只需发送一个简单的 HTTP `DELETE`请求就可以删除一个餐馆项目。

让我们向`wp-json/rest-tutorial/v1/restaurants/{id}`端点发送一个 HTTP `DELETE`请求。如果餐馆被成功删除，您将得到以下响应。

当使用 [Postman](https://www.getpostman.com/) 发送请求时，确保你已经安装了用于基本认证的[基本认证](https://github.com/WP-API/Basic-Auth)插件。

您也可以设置参数`force=true`来永久删除项目，而不是移动到垃圾桶。

在接下来的教程中，我们将学习如何通过发送 HTTP POST 方法来创建和更新餐馆项目。

帖子[如何从你的自定义插件扩展 WP REST API:第 4 部分](https://upnrunn.com/blog/2018/10/how-to-extend-wp-rest-api-from-your-custom-plugin-part-4/)最早出现在 [upnrunn | Build &部署强大的应用](https://upnrunn.com)。