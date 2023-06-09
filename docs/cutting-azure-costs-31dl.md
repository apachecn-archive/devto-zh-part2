# 削减 Azure 成本💰

> 原文：<https://dev.to/azure/cutting-azure-costs-31dl>

我是 DDD 悉尼会议的组织者之一，这是一个在悉尼举行的非营利性会议。作为一个非营利组织，我总是在考虑如何削减我们的成本，因为每一分钱都很重要，今年我决定考虑如何削减我们的 Azure 成本。

为什么我们需要这样做？在过去的几年里，我们已经开始获得运行网络资产的复合成本。我喜欢让所有前几年的网站继续运行的想法，因为它让人们看到我们这些年来作为一个事件所做的事情，而且当我们与赞助商交谈时，它也是一种有用的营销工具。

在过去的两年里，DDD 悉尼网站使用了一个 ASP.NET MVC 应用程序，它是 DDD 墨尔本网站的一个分支。因为我喜欢简单性，它被部署为运行标准层的 Azure AppService，所以我们得到了自定义域(然后它由 Azure 表存储支持，用于会话提交和投票)。这很好，每月运行费用约为 15 澳元，由于对银行的不满，这笔费用将记在我的个人信用卡上(我也懒得把这笔费用还给我们的商业实体 SydDev Inc .)。

但是我们的网络业务开始增长，今年将会有第三个应用服务运行，我想用 Hugo 做一个博客，就像我的博客一样，那就是 forth AppService。现在我们每个月要花费 60 美元，这是我无法承受的，而且作为一项活动，我们的花费开始变得更加昂贵。

是时候削减成本了！

## 趋静

实际上，DDD 悉尼网站的存在是一种静态的体验，在很长一段时间内，网站上没有什么真正的变化，我们有几周的会议提交(我们今年使用 Sessionize)，然后投票。没有什么事情*真的*需要一个动态 web 服务器来运行。因此，我想把它搬到一个静态的网站，但我也没有时间(读:懒惰)，所以我想用最少的努力做到这一点。

便利的是，DDD 珀斯团队正在使用 React 建立一个新网站，我欣然接受了使用它的想法。但他们打算在 AppService 中运行它，这看起来有些过头了，所以我们只使用服务器端渲染并生成一堆 HTML 页面(并在顶部进行反应，这样就有一些动态的东西了)。

## 在哪里主持？

现在我有了一堆静态资产，我可以在哪里托管它们呢？我想**而不是**在某个地方开一个新账户，这样使用 Azure 就有意义了(它已经设置好了，为了安全，它与我们的 O365 集成在一起，等等。).我知道我可以将所有这些文件上传到 Azure Blob 存储，打开匿名认证，瞧，你就有了一个静态网站...运行在一个相当可怕的网址。

## 使网址漂亮

所以你已经在 Azure Blob 存储中运行了你的站点，但是 URL 不是很好，它肯定不是`dddsydney.com.au`，所以现在我们需要做一些事情来修复它。

我的第一个想法是 [Azure 函数代理](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies?WT.mc_id=devto-blog-aapowell)，它们是一种简单的方法，可以用一个漂亮的 URL 来显示一条路由，并且它支持 SSL +自定义域，所以，赢了！

但事实证明事情没那么简单。我无论如何也想不出如何在代理定义中做一些适当的通配符映射来映射所有的页面。我唯一的解决方案是为每个页面创建一个不太理想的代理，尤其是当博客被加入进来的时候。

我接下来想到的是 [Azure CDN](https://azure.microsoft.com/en-us/services/cdn/?WT.mc_id=devto-blog-aapowell) 。现在我在 Azure CDN 方面没有**零**经验，事实上我找不到任何我认识的有 Azure CDN 经验的人(和我一起工作的，或者从外部了解的，我肯定有人在使用它😛)，所以没有像现在这样的时间来学习它了！

事实证明，Azure CDN 实际上只是另外两家 CDN 提供商威瑞森和 Akamai 的幌子，但被集成到了 Azure 门户网站中...算是吧。我将需要使用高级层，因为我需要创建自定义规则，这意味着我在幕后使用威瑞森。

我看到了这篇文章，这篇文章讲述了我所需要的基本知识。

使用 Azure CDN 时，有一些问题，或者至少是需要注意的事情，首先是强制 HTTPS 的规则排序。你需要确保在 URL 重写之前完成你的强制 HTTPS。这花了我一段时间来整理，每次你改变一个规则都要花几个小时，所以最好早点开始。

以下是我们的规则(对于博客而言):

```
<rules schema-version="2" rulesetversion="15" rulesetid="753098" xmlns="http://www.whitecdn.com/schemas/rules/2.0/rulesSchema.xsd">
  <rule id="1210941" platform="http-large" status="active" version="5" custid="84F94">
    <description>HTTP -> HTTPS</description>
    <!--If-->
    <match.request-scheme value="http">
      <feature.url-redirect code="301" pattern="/8084F94/blog-dddsydney/website/\d*/(.*)" value="https://%{host}/$1" />
    </match.request-scheme>
  </rule>
  <rule id="1210878" platform="http-large" status="active" version="0" custid="84F94">
    <description>Wildcards</description>
    <!--If-->
    <match.always>
      <feature.url-user-rewrite pattern="/8084F94/blog-dddsydney/((?:[^\?]*/)?)($|\?.*)" value="/8084F94/blog-dddsydney/$1index.html$2" />
      <feature.url-user-rewrite pattern="/8084F94/blog-dddsydney/((?:[^\?]*/)?[^\?/.]+)($|\?.*)" value="/8084F94/blog-dddsydney/$1/index.html$2" />
    </match.always>
  </rule>
</rules> 
```

Enter fullscreen mode Exit fullscreen mode

接下来要注意的是，设置 SSL 需要花费**时间**，说真的，在所有 CDN 节点上部署一个证书大约需要 8 个小时，但美妙的是它是免费的，我不必管理证书到期或任何事情！

不要忘记，您将经常需要清除缓存，这可能需要一段时间，这将导致最初的一些缓慢。

## 结论

在过去的两个月里，由于我们已经使用 Azure CDN 在 blob 存储中将 3 个网站和博客作为静态网站运行，因此总共花费了 3.26 美元。是的，我对此很满意！

现在，当然，我可以在 AWS 中使用 S3 桶，或使用 CloudFlare 作为 CDN 提供商，或 GitHub pages，或，或，或...但它们都需要一系列新账户，而我们不想管理这些账户。DDD 悉尼的成本很低，因为我们把东西保持得很轻，我通过不同平台增加的开销越多，我们不得不推进的成本就越大，因此也就越昂贵。所以最终在 Azure 中设置好一切对我们来说是好事，真的，一旦我知道我在做什么，事情就没那么复杂了。