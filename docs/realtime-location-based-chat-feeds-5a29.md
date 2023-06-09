# 构建基于位置的实时聊天源。

> 原文：<https://dev.to/ekeneeze/realtime-location-based-chat-feeds-5a29>

# 我造了什么

一个移动应用程序，允许用户使用谷歌地图小工具共享他们的位置。单击“共享位置”按钮，包含用户当前位置的地图小部件将在中央聊天框中实时呈现给所有连接的用户。为了识别每个用户的位置，我将提供的用户名放在地图标记上，指向他们的位置。

[![alt google images](img/db6b77326cf5ba6ec83526a080f01eca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V5wx5fOt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zhbrqoqw6x0j5z7b9zu0.jpg)

## 有用性

嗯，我可能在那里做了很长时间的工作，但是这对任何人有什么用呢？。让我们考虑这样一种情况，也许你的家庭成员都注册了这个应用程序，你们可以实时共享位置，例如，如果有人在公园里失踪或其他一些严重的案件，如绑架，你所要做的就是点击一个按钮，它会向你的家庭成员广播你的当前位置，然后他们会实时接收你的位置。就是这么简单。也许没什么大不了的，但它确实是一个有用的工具。

### 演示链接

[下载 apk 并试用该应用](https://drive.google.com/file/d/1VMMz9qKeWPMH1GdT_90_9iTJtmdHLQli/view?usp=sharing)

### 链接到代码

[在 Github 上查看项目](https://github.com/Kennypee/pusher-location-feeds/tree/dev)

### 我是如何建造的

为了更好地解释我是如何构建这个应用程序的，我想先告诉你它是如何工作的。当应用程序启动时，它会显示一个针对`username`的输入，以获取特定用户的名称。

一旦提供了用户名，用户就可以点击屏幕上的`Share Location`按钮，该按钮将向我们的服务器发送一个请求，其中包含用户提供的用户名和他/她当前的经度和纬度。然后，服务器将使用用户的`username`、`longitude`和`latitude`进行响应，我们将使用它们来创建一个 Google 地图小部件，并将一个大头针放在用户的当前位置。

这将把提供的用户的`username`放置在地图标记上。这个小部件现在呈现在中央布局上，每个连接的用户都可以看到其他用户以前共享的位置。

如果你已经熟悉 Pusher，我打赌你已经可以想象我是怎么做的了。但是如果情况不是这样，我将提供一些简要的解释来阐明这一点。

## 技术位

这将是一个用 Kotlin 构建的 Android 应用程序，用于监控 Node.js REST API 的活动。每次点击 API 的端点，Pusher 将发布一个带有一些信息(用户共享的位置)的事件到一个通道`feed`

首先，我创建了将与我们的 REST API 通信的 Android 客户端。在客户端，我定义了一个`LoginActivity`类和布局来处理当用户在启动应用程序时提供用户名时登录应用程序，不需要认证。

然后，为了将来自服务器的用户位置小部件呈现在应用程序的中央布局中，我使用了`Recyclerview`，它使我能够访问一个`adapter`类，该类充当我的服务器和客户端之间的连接。

但是首先，为了将来自服务器的响应呈现到应用程序的视图容器中，我创建了一个模型类，作为定义内容结构的模式。之后，我在 Adapter 类中从这个模型创建了一个数组列表，然后用它来填充我的 recyclerview 对象。

为了发出请求并从服务器获得响应，我使用了`retrofit`。首先，我在项目中初始化 Pusher，并在我的`MainActivity`类中设置它。然后使用 retrofit，对我来说很简单，发送一个带有用户提供的用户名、经度和纬度的请求到服务器，并得到一个响应，即一个包含用户当前信息(经度、纬度和用户名)的 JSONObject。

然后，我将这个响应传递给适配器，并在我为其建模的布局文件上显示它。最后，我呈现了包含 recyclerview 上的数据的布局文件。

然后在服务器端，
首先，我创建了一个 Node.js 服务器，并用我的 pusher 凭证对其进行了配置，这样，无论何时到达我们的 API 端点，我们都会向 Pusher 通道`feed`发布一个事件。然后将响应作为对象发送回去，这样我们就可以在客户端的活动提要中显示它。

Pusher 事件有一个`eventChannel`，允许它们与一个特定的主题相关联，一个`eventName`，用于标识事件的类型，还有一个`payload`，您可以将任何附加信息附加到其中并发送给客户端。

在我的例子中，当我的 API 的端点被调用时，我向推送通道`feed`发布了一个事件。然后将响应作为一个对象发送回来，并从那里将数据传入我的适配器并显示它

# 结论

就这样，伙计们。希望通过扩展，有人会从这里扩展应用程序，并构建更壮观的东西。这个项目可以在 Github 上找到，我也链接了 apk 供下载。如果你有一个 android 设备，下载并给它一个旋转。最后，在你退出之前，点击心形图标。