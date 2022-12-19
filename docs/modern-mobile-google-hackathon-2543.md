# 现代移动谷歌黑客马拉松⚡️

> 原文：<https://dev.to/mattc/modern-mobile-google-hackathon-2543>

[![](../Images/77204e6b32b4dddbf9c6c40b3e432ba6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vRuofU8n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AC65vTduiQbtXOVNXZg-Olg.jpeg)

9 月 11 日星期二，我、我的同事和一个客户被邀请去伦敦参加现代移动谷歌黑客马拉松。大约一年前，我们开始了与客户的网站速度工作，我们实施了:

*   惰性装载
*   资源提示
*   非样式文本的闪现(FOUT)，
*   将所有资产添加到 CDN
*   HTTP/2
*   速度曲线仪表板
*   嵌入式 SVG 精灵
*   关键 CSS

因此，就性能而言，我们处于一个相当好的状态，但像许多网站一样，第三方脚本是魔鬼，它会对我们的页面负载产生巨大影响！😒

然后，我们开始研究制作一个渐进式网络应用程序。这是一个有趣的项目，我们正在与谷歌就什么是正确的实施进行联络。

谷歌真的在推动网站向网站速度/渐进式网络应用/加速移动页面发展。页面加载 **3 秒**后， **50%** 的用户放弃网站。如果我们能让我们的网站在 3 秒内加载，那么我们就减少了人们离开的机会。我们都讨厌缓慢的体验。无论是在餐馆等待服务还是在等晚班公交车。这真的让我们很恼火。同样的原则也适用于网站。如果一个网站需要更长的时间来加载，用户可能永远不会回到你的客户的网站，因为他们记得第一次的体验是多么糟糕。

**任务**

黑客是为了能够和我的同事、谷歌工程师和移动专家一起创造一些令人敬畏的东西。整整一天，我们在 AMP & PWA 上进行了灯光讲座，为每个人提供了一些改进原型的好建议。灯光会谈结束后，我们继续建造我们的原型。

**我们的解决方案**

我在客户网站上的目标是获得网站速度，AMP & PWA 实施。我希望这是最快的事情。目前，我们已经对网站进行了大量的速度改进&我们已经为一个离线页面做了一个基本的服务。我们面临的问题是:

*   60%-70%的加载时间是由跟踪脚本引起的
*   图片是页面权重的一个重要组成部分，它会增加登陆页面的权重——WebP 正在酝酿中
*   页面上有很多影响交互时间的 JavaScript 代码分割正在进行中

过去，我们为一些登录页面实现了 AMP，但我们没有从速度差异的影响中获得太多价值。我们当时的问题是，我们无法在网站上缓存任何资产。这意味着我们在 AMP 页面上的启动速度非常快，但当您从非 AMP 页面导航到该页面时，转换速度会比较慢，因为没有缓存任何资产。

AMP 发布了一个名为[***AMP-install-service worker***](https://ampbyexample.com/components/amp-install-serviceworker/)的新功能，这个功能非常容易实现:

*   首先，我们在头部添加脚本。

[![](../Images/fd9b6a1f6da517b7475bb054fbb28aed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VQJ6F6Gp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ACYgXjMVeZZGuu2nVgI5yvg.jpeg)

*   然后我们在页面中添加组件。

[![](../Images/2a21abf6fa7225531ca52709794743c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0gw75Ii7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A4fFS3qU8PepgB9VeRpb8wQ.jpeg)

您可能已经注意到组件上有几个道具，它们的基本意思是:

*   **src** —您的域中服务工作者的路径。
*   **data-iframe-src** —服务人员所在域的路径。这是因为当它被缓存在 Google CDN 上时，页面知道服务人员需要被定位到哪里。
*   **layout="nodisplay"** —隐藏组件。

该组件将对我们的 AMP 页面做的是，它将下载我们的服务人员，该服务人员位于客户站点上，因此当用户从 AMP 导航到非 AMP 时，它将加快页面之间的转换。

[![](../Images/a098d6e7cff346575e9837092c849ae0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3Igppbcn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-w_ul0CHWHOM0bguXe3TQQ.jpeg)

**结果**

目前，在我们的登录页面上，我们的得分很高，但性能很差:

<figure>[![](../Images/93042460fa4e5b3d000e321d7551eaee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zx8DtTt7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ar2osoBuU3FJgg7GWwOXjGA.jpeg) 

<figcaption>当前登陆页面</figcaption>

</figure>

在我们去年构建的旧 AMP 登录页面上，性能得分很高，但 PWA 和最佳实践得分很低:

<figure>[![](../Images/bb32b55b0d4ae90671371ca477f608e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ntN0lBiR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMDbqevDfy44nqJ6rbcbaWw.jpeg) 

<figcaption>老 AMP 登陆页面</figcaption>

</figure>

然而在我们制作的原型上。在我们的图片上添加服务人员并转移到 WebP，使我们的得分都达到了 88+。我认为我们可以做得更好

<figure>[![](../Images/400ae3fbf8328ebcee95b1ddfdd149f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x4-z7FQ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APnloVQasUbY_m3AMxocM0A.jpeg) 

<figcaption>新 AMP 登陆页面</figcaption>

</figure>

我们如何保持高分？

随着时间的推移，所有项目都会老化。因此，网站速度也随之提高。我们需要能够保持代码的质量与第一次发布时的标准相同。

**速度曲线监控**

[SpeedCurve](https://speedcurve.com) 是我们在过去 6 个月中采用的一款性能工具，我们非常喜欢它！您可以在发布时触发 SpeedCurve 运行 WebPageTest。然后将之前的版本和最近的版本进行比较，看看你发布的版本是否对网站产生了影响。

**灯塔-建造后的 CI**

下一步将是添加 [Lighthouse-CI](https://github.com/andreasonny83/lighthouse-ci) 来运行 Lighthouse 审计，一旦站点被部署到一个临时环境中。通过这样做，您可以在我们发布之前检查性能是否受到影响。

**结论**

总的来说，我们和谷歌的移动专家一起度过了美好的一天，还见到了一些非常优秀的开发者。讨论他们在 PWA & AMP 方面的工作真的很有趣，看到每个团队采取不同的策略。我向所有对 AMP & PWA 感兴趣的人强烈推荐这个活动，我要感谢谷歌的邀请。

* * *