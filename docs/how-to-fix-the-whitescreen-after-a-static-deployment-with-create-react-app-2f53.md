# 如何使用 create-react-app 修复静态部署后的白屏

> 原文：<https://dev.to/a_reiterer/how-to-fix-the-whitescreen-after-a-static-deployment-with-create-react-app-2f53>

这是部署静态 React 应用程序后最令人讨厌的情况之一:在所有这些工作之后，您终于为生产部署了您的应用程序。但是当你访问这个网站时——而不是你刚部署的应用程序——你什么也没看到。

一个空白的白色屏幕。

原因可能是多方面的。例如，您的 JavaScript 中的某个关键错误，谁知道呢？

要找出问题所在，打开浏览器的控制台并检查错误信息总是一个好主意。

## `Loading failed for the <script> with source ...`

在“白屏情况”下，你可能会看到一个非常常见的事情，那就是你的应用无法加载 JavaScript 包——这就是我们今天要解决的问题。

[![](img/1deb95fd1b2631a45c799ef6c640251f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D_oDr4LR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.andreasreiterer.at/wp-content/uploads/2018/11/mainjsnotfound.jpg%3Fresize%3D660%252C84%26ssl%3D1)

现在让我们看一下文件实际存放在哪里。在我的例子中，我把它们放在一个子目录 *react-app* 中，所以正确的 URL 是*[【https://www.andreasreiterer.at/react-app/static/js…】](https://www.andreasreiterer.at/react-app/static/js%E2%80%A6)*

如果您检查您的*index.html*的 DOM，您将会看到，JavaScript 包的链接是绝对 URLs 来自您的应用程序的根目录:

```
<script src="/static/js/1.3aec9dfa.chunk.js"></script><script src="/static/js/main.72f93e60.chunk.js"></script> 
```

这就是我们问题的根源。你的应用程序不知道自己的位置来建立绝对网址。

## Webpack 的*公共路径*设置

在你的 Webpack 配置中有一个`publicPath`设置来告诉一个应用它的根路径是什么。在我们的例子中，那将是 https://www.andreasreiterer.at/react-app/的 *[和 T4 的](https://www.andreasreiterer.at/react-app/)*

如果设置正确，它将从该 URL 中获取如上链接——您的应用程序将像预期的那样加载。

如果您使用 *create-react-app* 创建了 React 应用程序，您可能无法访问您的 webpack 配置。(除了你已经弹出，但这是不相关的)

_ **那么，我们将这些信息放在哪里呢？** _

我来告诉你秘制酱:它叫*“主页”*，它住在你的 *package.json* 里

## 指定“主页”

如果你使用 create-react-app，你就不必处理 Webpack 配置。(哪个好看😜)取而代之的是——**弹出或者不弹出**——我们只需要在我们的*包中指定*“主页”*即可*

[![](img/60b39d07e47656f6bf7faf236ba34b9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---meJEF1b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.andreasreiterer.at/wp-content/uploads/2018/11/homepage_package_json.jpg%3Fresize%3D660%252C90%26ssl%3D1)

create-react-app 如何设置其 webpack 配置，这将使用正确的应用程序基本 URL 替换`publicPath`。(如果你想了解更多关于`publicPath`设置的信息，请看一下 [Webpack 文档](https://webpack.js.org/configuration/output/#output-publicpath)

现在我们已经告诉你的应用程序它是基本 URL，再次运行`npm run build`并将应用程序复制到你的网络空间，以找到你的应用程序并运行。

**注意**:如果您在静态部署中遇到了`react-router`的路由问题，[这篇文章](https://dev.to/a_reiterer/how-to-fix-browserrouter-for-react-apps-on-apache-43jd-temp-slug-7791315)可能对您有所帮助。

## 包装完毕

下次在部署 React 应用程序后出现白屏时，请记住今天学到的步骤:

1.  检查浏览器控制台是否有错误
2.  应用程序是否链接到错误的捆绑包？
3.  更新你的 *package.json* 中的“主页”设置
4.  重新构建您的应用程序，并将其放在您的网络空间中
5.  成功！

### 不要错过我的下一篇帖子！

你喜欢这篇文章吗？注册我的[时事通讯](https://www.andreasreiterer.at/dev-newsletter/)，让我未来的博客文章直接发送到你的收件箱。

* * *

*图片由[安德鲁关](https://unsplash.com/photos/lTUyP3RaLpw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上*[T5】上下](https://unsplash.com/search/photos/white-screen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

帖子[如何用 create-react-app](https://www.andreasreiterer.at/fix-whitescreen-static-react-app/) 修复静态部署后的白屏首先出现在[的 Andreas Reiterer](https://www.andreasreiterer.at) 上。