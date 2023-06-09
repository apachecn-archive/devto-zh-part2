# 如何将 Sentry 添加到您的{N}ativeScript 应用程序

> 原文：<https://dev.to/cendekia/how-to-add-sentry-to-your-n-ativescript-app-1acj>

在本帖中，我将尝试展示如何向您的{N}ativeScript 应用程序添加 Sentry。正如你们中的一些人可能知道的那样，我们对我们的应用程序进行错误跟踪是多么重要，尤其是对于准备部署到生产中的应用程序。

与我们作为开发者可以通过从浏览器“检查”来轻松跟踪问题或错误的 web 应用不同，一旦移动应用 IOS/Android 发布到市场，除非用户报告，否则很难跟踪用户是否发生了任何崩溃或错误。

哨兵是最好的错误跟踪者之一，正如他们网站上所说:

[哨兵|错误跟踪软件——JavaScript、Python、PHP、Ruby，更多](https://sentry.io)

> Sentry 是一个开源的错误跟踪平台，提供完整的应用程序逻辑、深入的上下文以及整个堆栈的实时可见性。

好了，介绍到此为止，让我们来做技术方面的事情。我将使用已经构建好的来自{N}的[杂货示例应用程序](https://github.com/NativeScript/sample-Groceries),因此我们可以专注于如何实现 Sentry。

[本地脚本/样本-杂货](https://github.com/NativeScript/sample-Groceries)

让我们开始编码:

```
\> git clone [git@github.com](mailto:git@github.com):NativeScript/sample-Groceries.git
\> cd sample-Groceries 
```

在这次回购中，他们已经提供了 javascript 核心或 angular js 代码，虽然我更喜欢 angular js，但哪一个最适合你取决于你。

```
\> git checkout angular-end 
```

请按照说明在您的机器上安装{N}。

[本地脚本文档](https://docs.nativescript.org/)

<figure>[![](img/1ccb2c9d8ac1e2cd84590815703fa204.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0JQi4OWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/1%2APf2g7HDIOFQzZGEp_sZXAw.jpeg) 

<figcaption>截图#1</figcaption>

</figure>

在运行应用程序之前，请确保将您的 PC/笔记本电脑连接到您的 Android 手机，或者您也可以使用模拟器。

然后，使用{N} CLI 命令运行应用程序，如下所示:

```
\> tns run android 
```

…一边喝咖啡，一边等待构建过程。

你应该会看到**杂货**应用的登录界面，就像*的截图#1 一样。*

为了使用 Sentry，我使用了由 **danielgek 开发的很棒的 **nativescript-sentry** 插件。**

> 但是，Sentry 几个月前刚刚发布了新的更新，结果它让这个包看起来被否决了，幸运的是有人已经修复了

[通过 kvnvelasco Pull Request # 17 Daniel gek/nativescript-sentry 更新 android 依赖项和通用配置以使用最新的 sentry 版本](https://github.com/danielgek/nativescript-sentry/pull/17)

不幸的是，该软件包的作者现在似乎很忙，(在这篇文章发表时，他仍然没有合并修复)。所以我已经为您手动打包了它，您可以将它下载到您本地计算机上:

[native script-sentry-1 . 5 . 0 . tgz](https://drive.google.com/open?id=1WPZT29TXJYow8WsiWWaIjcD3D9bgHOQA)

将包添加到 approot 项目:

```
\> tns plugin add \<directory\_path\>/nativescript-sentry-1.5.0.tgz 
```

当原始存储库更新了修复后，您应该运行以下命令:

```
\> tns plugin add nativescript-sentry 
```

下一步，请注册 sentry.io 以获得*令牌*来访问他们的服务，这将是这样的:

```
https://\<your-token\>@sentry.io/\<your\_project\_id\> 
```

在 app.module.ts
中添加以下代码

```
**...  
import { SentryModule } from 'nativescript-sentry/angular';**  
...

@NgModule({
...
 imports: [
**SentryModule.forRoot({dsn: 'https://\<your-token\>@sentry.io/\<your\_project\_id\>'}),**
 ...
... 
```

像这样: