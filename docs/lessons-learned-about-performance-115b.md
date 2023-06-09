# 关于绩效的经验教训

> 原文：<https://dev.to/leoat12/lessons-learned-about-performance-115b>

性能是所有开发人员和工程师心中的一个世界。工具、库、框架和技术中使用的许多东西都有良好的性能，这是它成功的原因之一。不仅仅是开发、构建和部署时的性能，更重要的是，客户感受到的性能。这是我本周学到的一课。我不自觉地意识到这个事实，但第一次看到它发生是一次我从现在起都不会忘记的经历。

故事很简单，在我的公司，我们正在使用 Spring Boot 2.0 开发一个 web 应用程序，同时也在学习 Spring Framework 如何做事情，这是一个从 JAX-RS 和纯 Java EE 到 Spring Boot 2.0 的学习过程，这方便了很多事情，但你仍然必须知道 Spring Boot 如何能帮助你。虽然我们会犯错误，不得不重新构建，但这很棒。正是在这样一个时刻，教训来了。

该项目已经接近尾声，我们正在进行最后的测试，但客户仍然认为网站加载速度缓慢。老实说，以我的标准来看，它并没有那么慢，其他有类似目的的网站表现相似甚至更差。尽管如此，我接受了调查和解决问题的挑战，提高了绩效。谷歌 Chrome DevTools 首先用于拯救分析其性能。

首先，我使用 Lighthouse 来审计站点，并对性能差的地方有一些想法。在那里，我发现性能不佳的主要原因是资源的加载:主要是 CSS 和 JS。服务器的回答非常快，但页面加载时间太长。在“网络”选项卡上，你可以看到每个资源的时间分配，以及 Lighthouse 所建议的一切。所以我去看代码，看了两件事:

*   缩减资源
*   缓存资源

作为后端开发人员，我首先关注的是资源的缓存策略。当时什么都没有，我承认这是我和团队的错误，但是现在我们知道了这个问题，最好用应该解决的方式来解决它。我开始研究 Spring 是如何处理这个问题的，我再次意识到这个框架有多棒。

Spring Framework 有许多不同的功能来处理这个问题，从缓存本身，像自动将适当的头放入所有资源，到对所有资源进行版本控制。有一篇文章非常有帮助，作者是 Baeldung，但它是针对纯 Spring 框架的，虽然有时基本上是相同的东西，但你需要将其翻译成 Spring Boot 方式，否则会发生一些错误。当你看到事情太复杂的时候，你很可能是做错了。不幸的是，我不记得把我带到解决方案的所有细节，我在事后有了文本的想法，但最终的配置如下:

```
spring.resources.cache.cachecontrol.max-age=365d
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/** 
```

Enter fullscreen mode Exit fullscreen mode

Spring Boot 应用程序的第一个参考是可用的应用程序属性[。第一行告诉我们资源将被缓存的时间，在本例中是 365 天。第二行启用内容版本控制策略，它基本上将文件的散列附加到 HTML 中所有资源的名称上。最后一行告诉了将应用版本控制的路径，它们都是。](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)

之后，你必须告诉什么样的 URL 版本是需要的。也许有一个更自动的方法来做到这一点，但这是我找到的解决方案，如果你知道一个更好的方法，请评论。首先，根据控制器的建议，我注入了一个 [ResourceUrlProvider](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/resource/ResourceUrlProvider.html) 的实例，它负责为每个资源构建新的 url。

```
@ModelAttribute("urls")
    public ResourceUrlProvider urls() {
        return this.resourceUrlProvider;
    } 
```

Enter fullscreen mode Exit fullscreen mode

之后，你只需要在 URL 上使用类的方法`getForLookupPath()`就可以了。它将验证它是一个资源，进行哈希运算并附加到文件名中。如果文件改变，它的散列值也会改变，迫使浏览器重新加载它。

```
<script src="<c:url value="${urls.getForLookupPath('/js/jquery.js')}"/>"></script> 
```

Enter fullscreen mode Exit fullscreen mode

这项日常工作改变了客户和经理对应用程序性能的看法。加载时间从大约 10 秒减少到最多 2 秒，甚至更快，具体取决于页面。我想我从来没有被那样表扬过，这就是教训。以对客户和管理层清晰的方式展示你的开发结果总是好的。对于后端开发人员来说，这并不总是容易的，它主要是关于性能。因此，要经常寻找提高自己表现的方法。这并不容易，网络上的许多解决方案都是最简单的，但并不总是最好的，所以尽量保持批判性，衡量性能，看看是否有更好的方法。

那件事之后，我加强了对表演的关注。希望从现在开始能这样不断进步。:)