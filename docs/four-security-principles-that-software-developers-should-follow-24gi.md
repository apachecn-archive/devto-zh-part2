# 软件开发人员应遵循的四条安全原则

> 原文：<https://dev.to/robdwaller/four-security-principles-that-software-developers-should-follow-24gi>

开发人员通常对安全性知之甚少，因为他们中的许多人关注安全性的技术方面，而不是涉及人员、资金、风险和业务优先级的更广泛的主题。因此，我们经常看到糟糕的决策、不必要的复杂化和资源浪费。

开发人员在构建或选择安全解决方案时，选择适合其业务或组织情况的解决方案是非常重要的。尤其重要的是，初级开发人员应该了解做出安全决策的更广泛的背景。

## 1。避免教条和绝对主义

在最近的一篇文章中，一位贡献者分享了以下关于 JSON Web 令牌和本地存储的建议。

> 我今天看到的最大的安全违规者是那些在本地存储中存储 jwt(会话数据)的人。许多人没有意识到 jwt 和用户名/密码本质上是一样的。如果攻击者可以获得您的 JWT 的副本，他们可以代表您向网站发出请求，而您永远不会知道。像对待信用卡号或密码一样对待 jwt:永远不要把它们存储在本地存储中。

给出这个建议的帖子很好，绝对值得一读，并且涵盖了许多与 JavaScript 本地存储相关的重要问题。遗憾的是，这种关于 jwt 和安全性的说法是错误的，或者至少缺乏开发人员需要理解的细微差别。

对 jwt 和本地存储采取的立场是绝对的，“不要这样做！!"但是你把 JWT 存放在哪里并不重要，存放在“安全”的地方并不能保证安全。重要的问题是，你在 JWT 储存了什么？您使用 JWT 来做什么或访问什么？

如果这些问题的答案不包含任何个人身份信息，或者包含最少的 PII，那么您可以随意使用这些 jwt。如果相比之下，你对上述问题的回答是，“他们所有的信用卡信息！!"那么您可能应该考虑 JWTs 的替代技术。

举个例子，如果你要实现一个内容付费墙，就像现在许多在线新闻出版物所做的那样，那么存储在本地存储器中的 jwt 将是一个完全可以接受的安全解决方案。你所保护的内容价值很低，没有 PII，所以黑客有兴趣入侵这些内容的可能性很低。不过，JWTs 将会阻止普通的网络用户在不付费的情况下访问内容。安全需求的简单解决方案。

您会注意到，这种解决安全问题的方法不那么教条和绝对。有才华的开发人员有一种变得教条和绝对主义的倾向，可能是因为他们看到的一切都是“糟糕的”或者至少不够完美。这有点像公元前 5 世纪柏拉图对雅典的看法，但就像柏拉图一样，这种方法会导致糟糕的解决方案和糟糕的答案。对于那些试图理解一个主题的人来说，尤其是对那些低年级学生来说，这是没有帮助的。

在处理安全问题时，避免教条、绝对主义和一刀切的说法是明智的。不存在等同于道德上的绝对“不要谋杀”的说法，因为安全涉及更多的细微差别。

## 2。没有所谓的安全

安全的核心有一个很大的讽刺，那就是它并不存在。最近，谷歌 Chrome 在 Twitter 上宣布，他们将把所有使用 HTTP 的网站标记为“不安全”。他们已经在地址栏中将 HTTPS 的网站标记为“安全”。

> 🔐⚠️，这是我们期待已久的时刻！Chrome 将在 2018 年 7 月将所有 HTTP 网站标记为“不安全”。🔐https://t.co/2eV4GuEa2y⚠️
> 
> — emily schechter (@emschec) [February 8, 2018](https://twitter.com/emschec/status/961662132012986368?ref_src=twsrc%5Etfw)

这很奇怪，因为 HTTPS，或通过 TLS 的 HTTP，是一个非常有用的安全增强，但不能保证安全。完全有可能建立一个网站并通过 HTTP 提供服务，它比通过 HTTPS 提供服务的网站更安全。

谷歌在这方面的行为令人惊讶地不负责任，因为它们可能会鼓励普通网络用户在不安全的时候感到安全，从而对他们的在线行动和行为不那么谨慎。这还不包括如何实现 HTTPS 连接的主题，[参见 CloudFlare flexible SSL](https://www.cloudflare.com/ssl/) 。更明智的方法可能是将连接描述为“私有”或“公共”，但“安全”或“不安全”会产生误导。

从来没有任何东西是完全安全的，即使我们取得了所有的技术进步，仍然没有。安全总是与被保护的内容相关。人们花了几千年来建造这样或那样的墙，但没有人能成功建造一个坚不可摧的穹顶。

如果你不相信我，就去问伊朗人。2009 年，美国人，肯定是在以色列人的帮助下，很可能是在英国人的帮助下，入侵了一个名为纳坦兹的伊朗核设施。你可能还记得读过关于[震网病毒](https://en.wikipedia.org/wiki/Stuxnet)的报道，它可能是罪魁祸首。这次黑客攻击的不同寻常之处在于，纳坦兹的设施是空气隔离的，可能是世界上最安全的设施之一。尽管如此，这并没有阻止美国人将病毒带入核设施，并破坏伊朗的核生产过程。

如果你对这个话题和类似的故事感兴趣，我建议你读一下戈登·科瑞拉的书 [Intercept:计算机和间谍的秘史](https://www.amazon.co.uk/Intercept-Secret-History-Computers-Spies/dp/1780227841/)。这是一本很棒的书，它将为你提供安全和黑客主题的背景。

良好的安全包括建立一堵比你所保护的资产价值更高的墙。也就是说，黑客入侵你的系统所付出的代价比他们从入侵中获得的要多。这也意味着您的安全性应该与您所保护的内容相称。例如，不要为了保护你从网络注册表格中收集到的几个电子邮件地址而在服务器上安装空挡，那将是极大的金钱浪费。

## 3。了解威胁

当你筑墙时，了解你面临的威胁是很重要的。安全威胁可以分为四个基本类别:

1.  儿童编剧和自动威胁:查看大多数 WordPress / Joomla 黑客。
2.  **熟练黑客和黑客组织:** Anonymous，LulzSec
3.  **有组织犯罪和次要国家行为者:**黑手党，朝鲜
4.  主要国家行为者:美国、中国、俄罗斯、以色列、英国

大多数开发人员很少处理一级以上的事情。原因有两个，你必须做一些有重大经济价值或重大政治价值的事情才能超越第一层。这方面的例子包括敏感的政府工作、财务工作和大型企业工作。

此外，威胁是多种多样的，不一定与黑客数据有关。例如，您的组织可能比数据泄露更容易受到 DDOS 攻击。作为一名开发人员，重要的是你要考虑你的组织是如何容易受到攻击的，主要的弱点可能不总是集中在财务或 PII，它可能是声誉。对黑客来说，让你的网站离线让你的组织难堪，可能比窃取你服务器上的 PII 更有价值。

2014 年发生的[事件是组织未能正确理解威胁的一个很好的例子。在这种情况下，苹果公司未能正确评估他们在 iCloud 系统上的内容，因此没有实施可以限制损害的安全功能。例如，当一个新的设备或陌生的 IP 连接到一个帐户时，发送电子邮件。伪造是一个边缘案例，因为以前没有人真正考虑过名人数据的价值，但它确实凸显了组织面临的威胁可能并不总是合乎逻辑的。](https://en.wikipedia.org/wiki/ICloud_leaks_of_celebrity_photos)

## 4。实施适当的解决方案

如果您在没有正确考虑您所面临的威胁的情况下实施通用的安全解决方案，您可能不会比完全不实施安全解决方案更安全。

作为开发人员，在实现任何安全解决方案之前，您必须认真考虑您面临的威胁。这样您就可以实施相应的安全措施。不过，相称不仅仅与安全威胁有关，还与你必须花多少钱有关。一个贫穷的国家无法建造中国的长城，但如果它了解威胁并明智地部署资源，它可以保护自己。

世界上大多数人都喜欢嘲笑朝鲜、金正恩和他疯狂的核计划。然而，在疯狂的背后可能有一些逻辑。有一种理论认为，朝鲜已经认识到，美国和西方很乐意对阿富汗、伊拉克和利比亚进行政权更迭，但在伊朗和巴基斯坦问题上要谨慎得多。理论认为，这是因为伊朗可能拥有核武器，而巴基斯坦确实拥有核武器。朝鲜已经认识到了这一点，正急于制造核弹和导弹，以免成为下一个伊拉克。鉴于其有限的手段，它已经评估了威胁并做出了合理的回应，而且它可能成功地保护朝鲜政权免受美国的轰炸。尽管最终核弹可能无法保护他们免受本国人民的伤害。

我并不建议你效仿朝鲜的做法，但是你应该考虑在你的组织资源允许的情况下，什么是适当的安全响应。鉴于我们中很少有人在苹果、谷歌或脸书工作，这一点尤为重要。大多数组织的资源有限，他们无法证明在只能产生边际收益的安全功能上投入大量资金是合理的。在考虑您的安全响应时，您应该从问以下三个问题开始。

1.  我在捍卫什么，它有多大价值？
2.  是谁对我构成了威胁？
3.  我的组织有多少资源来保护自己？

如果这些问题的答案是低价值、有限威胁和低资源，那么基本的就足够了。例如安全地加密密码，不要存储太多的 PII，实施 CSRF 政策，等等，等等。如果答案是相反的，那么你将不得不考虑更高级的安全特性。如果答案是混合的，你可能不得不在某些方面做出妥协。

这里最重要的一点是，没有“一刀切”的安全解决方案。所以教条和绝对主义根本不适用于安全。考虑安全性时，请仔细考虑您要保护的内容，然后对可能的威胁进行分析。在这之后，用你现有的资源建造你的墙。