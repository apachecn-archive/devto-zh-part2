# 调试 IIS 重写规则

> 原文：<https://dev.to/davidwengier/debugging-iis-rewrite-rules-4jbi>

在我工作的地方，我们有大量的 ASP.NET 网络应用程序运行在我们网站的不同部分，这样我们就可以有一些代码隔离和范围控制，而不仅仅是一个巨大的整体项目，将所有东西混合在一起。从技术上来说，这没什么太令人兴奋的，但营销部门也需要能够将整个网站作为一个整体呈现给访问者，以及谷歌机器人，因为这种甜甜的 SEO 果汁(以及更容易的导航和其他不那么愤世嫉俗的原因，我敢肯定)。我们实现这一点的方法是大量使用 IIS URL 重写引擎，它允许我们创建一组规则，接受传入的 HTTP 请求，并将它们路由到不同的 web 应用程序或不同的虚拟路径，或者完全阻止一些请求。

网上有很多关于设置这些功能的文档和示例，我当然不会声称自己是它们全部功能的专家，但我知道的一件事是，虽然它们在工作时非常出色，只是坐在那里愉快地做着自己的工作，没有抱怨，但当添加新功能时，它们是否工作有时会显得有点神秘。此外，因为我们使用它们将许多不同的应用程序和 URL 整合到一组连贯的公共 URL 中，所以让它们在本地快速运行最终会导致对本地环境的请求被重定向到真实环境，没有真正的方法知道这是因为规则运行良好，还是仅仅因为它们最终失败了。

幸运的是，有一种方法可以调试规则，或者至少可以从引擎中注销，尽管有点隐蔽。

## 不是所有的失败都是失败

答案在于 IIS 失败请求跟踪功能，以及可以配置它来跟踪成功请求和失败请求一样容易的事实。可以通过 IIS 管理器访问该功能，也可以在应用程序的`web.config`文件中指定配置。

[![Failed Request Tracing](img/bb3c663b070e316184bb06a9c63a1f54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_T5ya79O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wengier.cimg/posts/FRT.png)

该模块本身有一个很好的向导来指导你建立一个新的规则，但是要以我想要的方式调试重写，这有点不直观，所以我将详细说明我做了什么。

第一步很简单，选择想要跟踪的文件名。在 MVC 和 WebApi 的现代时代，这感觉有点过时，因为文件名有点无聊，所以最好的，当然也是最容易的，只是在“所有内容”上留下这个选择。

第二个屏幕是真正神奇的地方:

[![Failed Request Tracing Step 2](img/ce49ec5baf93591225225367be81ff74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zzfhT68Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wengier.cimg/posts/frt-step-2.png)

这里的第一个输入选项是指定应该跟踪哪些 HTTP 状态代码，这就是我们翻转“失败”标题的地方。通过在这里指定一个成功的代码(即 2xx 或 3xx ),我们可以跟踪成功的请求，而不仅仅是失败的请求。

根据您希望进行多少日志记录，您可以将范围缩小到您希望跟踪的状态，例如，只指定 301 来跟踪永久重定向，或者您可以扩大范围。我认为从尽可能宽的范围开始，并为这个值指定`200-399`是最好的，这样即使您添加了一个新的永久重定向规则，您也可以得到日志，即使您的规则出了问题，并且请求失败到了不同的重写规则。

如果你试图跟踪的请求到达了你的站点并导致了错误或坏的 URL，你可能也想把`404`和`500`添加到列表中。

[![Failed Request Tracing Step 3](img/81207dd185c9aeec3e41751ecb6aaa15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EtIOuA5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wengier.cimg/posts/frt-step-3.png)

第三个屏幕允许选择您想要跟踪的 IIS 模块，因此为了将一些干扰排除在日志之外，最好取消选中除`Rewrite`和`RequestRouting`之外的所有模块。让冗长保持冗长，主要是因为说“冗长冗长”很有趣。

就这样，您已经配置好了。您还可以在 web.config 文件中使用以下配置来配置所有这些内容的等效内容:

```
<tracing>
    <traceFailedRequests>
    <add path="*">
        <traceAreas>
        <add provider="WWW Server" areas="Rewrite,RequestRouting" verbosity="Verbose" />
        </traceAreas>
        <failureDefinitions timeTaken="00:00:00" statusCodes="200-399" />
    </add>
    </traceFailedRequests>
</tracing> 
```

最后，通过点击右边栏中的“编辑站点跟踪…”并勾选“已启用”复选框，确保该功能本身已启用。如果已经启用了，那就太好了，但是这个屏幕对于获取日志文件的路径还是很有用的，默认情况下是`%SystemDrive%\inetpub\logs\FailedReqLogFiles`。

## 查看日志文件

失败请求跟踪模块记录到 XML 文件中，我绝对不推荐查看原始格式的 XML 文件。幸运的是，IIS 还为您生成了一个 XSL 文件，它很好地将日志格式化成某种模糊可读的格式。我发现查看日志最简单的方法就是在 Internet Explorer 中打开 XML 文件(是的，我知道)，因为它会自动找到并应用 xsl 文件，而 Chrome 不会。

正如我们所要求的，日志本身非常详细:

[![Failed Request Tracing Sample Log](img/4b7999d912957e855d22b5ef14fe4a1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CxHAndkb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wengier.cimg/posts/frt-output.png)

您将看到重写配置中每个规则的输出，您可以看到输入值和匹配的模式。您可以看到每个 on 是否成功，尽管值得注意的是您需要自己应用`negate`值，因此一个否定的规则可能会说“Succeeded: false ”,您必须记住这意味着您编写的规则实际上是匹配的。

希望在浏览完文件后，你能弄清楚到底发生了什么，虽然我个人发现在文件中搜索你认为可能有问题的规则比浏览所有规则更容易，但是我来自一个有很多规则的代码库。