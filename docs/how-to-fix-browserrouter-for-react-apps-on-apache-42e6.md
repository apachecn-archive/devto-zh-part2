# 如何修复 Apache 上 React 应用程序的浏览器

> 原文：<https://dev.to/a_reiterer/how-to-fix-browserrouter-for-react-apps-on-apache-42e6>

在学习 React 的同时，大多数人在本地开发和测试他们的应用。我的意思是…它们只是用于学习目的的演示应用程序，对吗？没有人想看到这样的东西，那么为什么还要考虑在某个地方部署应用程序呢？

但是有一天，你试图将一个 React 应用程序部署到一个 web 服务器上，这样你就可以把它展示给外面的人看:“看！这是我做的！”。对我来说也是如此。

### 将静态 React 包部署到 Apache web 空间

我已经有了一个网络空间，所以很明显我要把它放在那里。因为我使用了`create-react-app`，我所要做的就是运行`npm run build`并将我的应用程序的`build`文件夹的内容复制到上述网络空间。

是啊！经过这么多艰苦的工作，我刚刚部署了我的第一个 React 应用！

我开始点击几条路线，一切似乎都很好，直到我刷新页面或试图直接访问一条路线。我得到一个 404 错误。

[![](img/9dd15f83f172a67078458a251a789b09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4i7olYr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.andreasreiterer.at/wp-content/uploads/2018/11/oopsnotfound.jpg%3Fresize%3D660%252C92%26ssl%3D1)

“但它在本地很管用——为什么在我的网络空间里却不行？”

你听起来熟悉吗？

### 为什么 BrowserRouter 不工作？

一些研究提出了几种解决方案。其中之一:

*“使用 HashRouter 而不是 browser router”*

好的，这很简单。但是因为我想让应用程序支持浏览器的历史，这是没有选择的。所以让我们深入挖掘一下，看看错误的原因。

当你直接访问应用程序的路径时，例如通过*[【https://myapp.com/route/123】](https://myapp.com/route/123)*阿帕奇会尝试将该 URL 映射到公共文件夹中的一个文件。在这种情况下，它会寻找显然不存在的 */route/123.html* ，因此会出现 404 错误。

为了避免这种情况，我们必须告诉 Apache 将所有请求重定向到我们的*index.html*，这样我们的应用程序就可以为该 URL 执行一些路由魔法。

### 修复应用的路由

现在，这里是如何最终修复路由。为了告诉 Apache 将请求重定向到我们的应用程序所在的 index.html，我们必须修改 T2。htaccess 文件。如果应用程序的文件夹中还没有这样的文件，只需创建它。

然后，请确保您放入了这 4 行，它们将神奇地使您的路由工作。

```
Options -MultiViews
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.html [QSA,L] 
```

在我们放了那个*之后。htaccess* 文件放入与 index.html 的*相同的目录，Apache 会将每个新请求直接重定向到你的应用程序。*

### 奖励:将 React 应用程序部署到子目录中

如果你把你的应用程序部署到一个子目录中，这样就可以通过*[【https://myapp.com/the-app】](https://myapp.com/the-app)*访问，你很快就会注意到另一个问题。每次点击一个新的路径都会将网址转换成类似于 https://myapp.com/route-abc 的——重新加载后会再次断开。但是有一个简单的解决方法:

*BrowserRouter* 有一个叫做`basename`的道具，在这里你可以指定你的子目录路径:

`<BrowserRouter basename="/the-app">`

从现在开始，每一个像`/contacts`这样的路由都会产生一个像*【http://myapp.com/the-app/contacts】T2 这样的 URL。*

### 包装完毕

今天，您学习了如何使用 *BrowserRouter* 为部署到 Apache 的 React 应用程序修复路由。你知道了为什么在开始时会出现 404 错误，并且你可以通过将所有请求重定向回 index.html 的*来克服这个错误。*

下次当您在 Apache 上遇到 React 应用程序的路由问题时，请回到本文，尝试将重定向脚本放到*中。htaccess* 文件。

你知道在 Apache 上托管静态 React 应用程序的更多问题吗？在底部留下评论。

### 不要错过我接下来的帖子！

注册我的[时事通讯](https://www.andreasreiterer.at/dev-newsletter/),让我以后所有的博客文章直接发送到你的收件箱！

* * *

*照片由[斯特夫·韦斯特海姆](https://unsplash.com/photos/ZGH6xd3usAs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/server?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

帖子[如何修复 Apache](https://www.andreasreiterer.at/fix-browserrouter-on-apache/) 上 React 应用的浏览器首先出现在[的 Andreas Reiterer](https://www.andreasreiterer.at) 上。