# 面向其他所有人的领域驱动设计

> 原文：<https://dev.to/barryosull/domain-driven-design-for-everyone-else-a9>

我最近一直在谈论领域驱动设计(DDD)，无论是在聚会上还是与客户，所以我想我应该写下我的想法，看看是否有帮助。

现在，许多人已经从技术角度写了关于 DDD 的文章(链接见末尾)，所以我不打算这样做，相反，我将从非技术角度讨论 DDD。

这是其他人的 DDD。

## 方案总是超限

设计和构建解决方案不是一个简单的问题。它从来不会顺利进行，即使它按时完成(从来不会),解决方案通常是无效的，需要改变，通常是彻底的改变。这导致更多的延迟，更大的预算，甚至更大的问题。

为什么这种情况一直发生？

## 复杂 vs 复杂

建立一个盈利的企业是一个复杂的问题，复杂与复杂是不同的。

比如“税”就很复杂。有很多很多交织的规则和过程，但是一旦你知道如何应用它们(过程)，你就解决了问题。它变得程序化；不需要思考。按照流程走就好了。

创业完全不是那样的，有太多的未知。换句话说，这很复杂。如果你仔细想想，这是相当明显的。如果这样的过程存在，那么每个人都会使用它们。无论如何都不会有任何风险(这篇文章也不需要存在)。

复杂的问题无法控制，[只能管理](https://sloanreview.mit.edu/article/the-critical-difference-between-complex-and-complicated/)。然而，我们仍然试图将业务发展视为一个过程，因为我们**真的**希望它是一个过程。只要看看“敏捷”*和“精益”的流行程度，尤其是大量营销的、面向过程的版本，它们强化了这种错觉(而且它们也不起作用)。简直是一厢情愿。

[![Complicated vs Complex](img/82eb019eee2e6ee2c0d23cb39bf2c3a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W6eErrip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8pqayuxmbcybpku4knwq.png)

DDD 承认这一事实，而不是专注于僵化的流程和硬性规定(即，一个尺寸适合所有的解决方案)，它提出了管理和消除模糊性的技术。秘密不在于遵循一个过程，而在于重复你试图解决的问题。

## 关注(正确的)问题

在 DDD，领域，也就是问题及其产生的知识/活动，是其他一切的驱动力。所有的解决方案都源于问题，所以将核心域放在中心自然会产生更好的解决方案。

例如，假设你的业务是在线新闻。你的“核心”领域(驱动你业务的领域)是内容生成，其他一切都是次要的。通过更快地制作更好的内容，你将发展你的业务。然而，如果你专注于解决调整图像大小的问题(并雇佣一群开发人员来编写软件)，那么你可能不会发展你的业务。

DDD 带来了清晰，通过清晰，我们能够专注。

## 与专家交谈

如果你想了解一个问题，那么你需要与领域专家交谈。领域专家是比任何人都更了解问题的人，他们能够告诉你什么是重要的，什么是不重要的。

在大多数组织中，领域专家不是构建解决方案的人。DDD 有助于弥合领域专家所知道的和构建解决方案的人试图理解的之间的知识鸿沟。

这就是为什么 DDD 的大部分技术与技术无关，相反，他们专注于挖掘复杂性和模糊性的人和方法。如果我们都在同一页上，就更容易前进。

## 建立共同的理解

获得清晰的一个关键方法是建立对问题的共同理解，即领域模型。如果每个人脑子里都有同一个模型，那就没有歧义了。这有助于我们避免解决方案过于复杂，或者更糟糕的是，避免构建错误的解决方案(例如上面的图像大小调整程序)。

## 说话，嗯？

语言是 DDD 的核心。我们用语言来表达我们的想法，探索问题和确定解决方案。如果领域是复杂的，那么语言将是丰富和复杂的，有它自己的微妙之处和细微差别。

问题是，你所在行业的大多数人可能不会说这种语言。相反，他们用自己的语言来解决问题，这很好。然而，当两个或更多的人试图使用不同的语言进行交流而没有意识到这一点时，问题就出现了，例如，相同的单词却有不同的意思。这导致了歧义，也是错误和误解的主要原因。

你加入的人越多，问题就越严重。以典型的指挥链为例。

[![The Chain of Mis-communication](img/cd91e2f665c26764f29d71ab58647ef9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AOG7ui3z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w3g46cxab377f313vcba.png)

这是一个很大的错误沟通的空间。如果你曾经请求过一个特性，却得到了一些不切实际的东西，这就是原因。

解决方案是每个人都与领域专家密切合作，理解业务定义的语言，并找出这些语言边界存在的地方，即当一个词在不同的上下文中使用时。这使得每个人都能够交流(例如，拥有共享的领域模型)，导致更少的孤立、更好的协作和更简单的解决方案。

## 你的解决方案反映了你对问题的理解

你建立的任何解决方案都是你对问题理解程度的直接反映。如果解决方案是好的，那么你了解你的领域，如果是坏的，那么你不了解。这就是为什么快速迭代如此重要，它是关于收紧反馈循环和迭代问题，而不是在第一次尝试时就构建正确的解决方案(这从未发生过)。

通过使用您的解决方案作为反馈，您可以进行进一步的讨论，并确定您是否接近了，这将导致更好的领域模型，从而产生新的解决方案，这将反馈到您对问题的理解中。这是一个循环，它鼓励我们更好地理解我们在做什么，而不是专注于解决方案。

## 反馈越快越好

测试解决方案的速度越快越好。这并不意味着你需要构建软件，这是最昂贵的测试方式。取而代之的是，为什么不用模型甚至纸笔来原型化一个解决方案呢？你会在一小段时间内得到同样的反馈。DDD 鼓励提前发现和迭代，而不是为了写软件而写软件。

[![Feedback loop](img/a81c1a0c512e19abea06e6995310959b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5qupOBav--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/naihh7lrfkjyxiwtays5.png)

这并不是说 DDD 不怎么谈论软件，它肯定会，而且它有很多写作模式，但软件不是核心。DDD 有一句谚语:最好的软件就是没有软件。你看，软件增加了复杂性，所以如果你能避免这种复杂性，你应该这样做。一个好的 DDD 实践者会试图找到现有的问题解决方案，只有当定制软件带来最大价值的时候才会依靠它。

## 自我提升

DDD 是关于理解和重复问题的问题。这是循环的，意味着 DDD 经常被用来了解自己和改进自己。你可以想象这个反馈循环有多快，DDD 的实践者总是在迭代、发现和命名模式和技术。随着时间的推移只会越来越好。

## 结论

简而言之，DDD 把业务和它的领域放在驾驶座上，这是它应该做的。我已经应用 DDD 5 年了，我不得不说我构建有用的解决方案的能力已经大大提高了。它帮助我磨练了理解和解决问题所需的技巧和技能。如果你想更好地构建解决方案，我会全心全意地推荐 DDD。

## 进一步阅读

*   [http://learningforsustainability . net/post/complicated-complex/](http://learningforsustainability.net/post/complicated-complex/)
*   [https://air brake . io/blog/software-design/domain-driven-design](https://airbrake.io/blog/software-design/domain-driven-design)
*   [https://en.wikipedia.org/wiki/Domain-driven_design](https://en.wikipedia.org/wiki/Domain-driven_design)
*   [https://tech beacon . com/get-your-foot-wet-domain-driven-design-3-guiding-principles](https://techbeacon.com/get-your-feet-wet-domain-driven-design-3-guiding-principles)
*   [http://www . Amazon . com/exec/obi dos/ASIN/0321125215/domain languag-20](http://www.amazon.com/exec/obidos/ASIN/0321125215/domainlanguag-20)

旁白:
*这是对资本“敏捷”的挖苦，顾问们把这种独特的方法作为解决所有开发问题的方法来出售，而不是真正的“敏捷”。这很棒，因为它专注于迭代编写软件的问题，适应变化。