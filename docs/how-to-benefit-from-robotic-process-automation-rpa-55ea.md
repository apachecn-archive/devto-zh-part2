# 如何从机器人流程自动化(RPA)中获益

> 原文：<https://dev.to/berndruecker/how-to-benefit-from-robotic-process-automation-rpa-55ea>

#### 应用 RPA 不应是一种无人值守的短期止痛疗法，而是嵌入到一种适当的战略中，以实现 IT 环境的现代化。

<figure>[![](img/0acde90549fc2dbc8169a4c8d210e606.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NFdwfrx---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AvP-4O8o9xKsqeB1_.)

<figcaption>[【亚历山大·奈特】](https://unsplash.com/@agkdesign?utm_source=medium&utm_medium=referral)上</figcaption>

</figure>

RPA(机器人过程自动化)目前被大肆宣传。RPA 的优点是当应用程序不提供 API 时，对现有图形用户界面 (UI) **的**自动控制。这是很多关于屏幕抓取、图像处理、OCR 和机器人操纵图形用户界面。****

RPA 工具市场非常多样化。您拥有来自销售低代码解决方案的供应商的一切(不要再这样了！我稍后会对此进行评论)到像“[用 Python](https://automatetheboringstuff.com/) 自动化枯燥的东西这样的代码密集型方法。此外，许多最初为自动化测试用例而构建的 UI 自动化工具也符合 RPA 标准。我们的一些合作伙伴报告说，这些工具(通常甚至不在 RPA 保护伞下销售)以低得多的成本提供了相同的价值。

所有这些工具都允许在自动化工作中集成遗留应用程序。许多公司因为他们自己的历史而与遗留问题作斗争:购买的现成软件从未提供适当的 API 和定制解决方案，在三层架构中构建为孤岛，UI 是主要或唯一的界面。系统集成通常在数据库级别完成，或者使用笨拙的企业应用集成(EAI)工具或批处理作业完成。这种环境需要严格的现代化，但 RPA 可以在此过程中充当短期止痛药。

这篇文章分为两部分:

1.  我将在第二部分更详细地描述 RPA 作为应用程序的包装器的**有效用例。如果您想直接深入了解如何从 RPA 中获得大量价值，请随意向下滚动。**
2.  但是在看到 RPA 的积极一面之前，我总结一下**典型挑战**，并特别强调出于错误原因滥用 RPA 的**危险。鉴于我从客户那里听到的令人担忧的反馈，这一警告非常重要。采用 RPA 的主要动机通常要么是 IT 资源短缺，导致业务部门自行构建(希望没有它也能运行),要么是拒绝投资适当的企业 IT 现代化战略。虽然这可以理解，但却非常危险。**

### 应用 RPA 时的挑战

RPA 通常由业务部门自己引入。以及"*当业务部门在没有 it 部门参与的情况下配置 RPA 时，IT 体系结构、基础架构和安全性方面存在交叉风险* " ( [克服机器人流程自动化的挑战](http://www.isg-one.com/related-case-studies-detail/overcoming-the-challenges-of-robotic-process-automation))。有许多具体的例子。

RPA 工具必须由某人**操作**。大多数解决方案都是在本地安装的，因此您不仅需要自己的服务器，还需要安装 RPA 工具和修补程序等等。需要在最终用户计算机上安装或部署代理。这是业务部门自己很少能做到的。所以它跳了进来，并经常找到一个非常陌生的工具来融入他们的景观。

安全性也可能是一个问题。哪个登录用于控制 UI？一些职员或技术用户？一个问题是，传统应用程序的授权模式通常不是由机器人使用的，因此不支持所需的粒度。你希望你的机器人拥有“上帝用户”权限吗？为了知道哪个机器人做了什么，你甚至需要解决审计方面的问题。

一旦进入生产，你必须组织**变更**的部署。如果您的 RPA 流本质上是[长时间运行](https://blog.bernd-ruecker.com/what-are-long-running-processes-b3ee769f0a27)，需要几天或几周才能完成，您甚至必须计划流的**版本控制**和/或运行实例的 [**迁移**。](https://docs.camunda.org/manual/7.7/webapps/cockpit/bpmn/process-instance-migration/)

但是还有其他更小的问题需要首先解决。你要回答**所有权**的问题:业务部门负责 RPA 流程吗？还是它？特别是“*如果在流程中使用了多个应用程序，前端 UI 中的任何变化，即使不影响处理流程，也会影响 RPA 脚本，从而影响结果*”([在机器人流程自动化领域，该领域的人们迫切需要解决或探索的当前问题/挑战是什么？](https://www.quora.com/What-are-the-current-problems-challenges-in-the-area-of-Robotic-Process-Automation-that-people-in-the-area-are-desperate-to-solve-or-explore))。

<figure>[![](img/51de1b6d2dd1b5dcfa44006c51d69143.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xoCxQojH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/453/1%2ATD0V_ixKXWNddW-UZDKM9g.png) 

<figcaption>多种不同的变化可能导致 RPA 流量的变化</figcaption>

</figure>

借助 RPA，您可以依赖应用程序用户界面的**稳定性。但是通常用户界面变化非常频繁，想想基于网络的用户界面，它们最近几乎每月都在变化，以满足对响应性或移动友好性的新要求。我们甚至看到前端本身很少改变，但在下面使用了不同的框架，导致 RPA 流完全重写的情况。如果操作系统发生变化，也会发生这种情况。这些变化的时机可能完全不受你的控制。与公共 API 相比，任何软件供应商都不承诺用户界面的向后兼容性。所以你必须为增加的和不可计划的维护工作做好准备**，**让每一个投资回报率的计算成为一个完全的猜谜游戏。**

我想将这与适当的 API 的使用进行对比。这些设计用于系统集成，因此解决了向后兼容性和版本控制问题。通常不允许进行重大更改。围绕不断发展的 API 的许多挑战已经得到解决，因此您可以建立在真实生活经验的基础上，这些经验在全球各地的大型 IT 社区和会议中共享。给你两个简单的例子:[宽容的阅读器模式](https://martinfowler.com/bliki/TolerantReader.html)描述了如何为 API 编写客户端，即使 API 随着时间的推移而发展，也不会被触及。和[消费者驱动的合同](https://martinfowler.com/articles/consumerDrivenContracts.html)是一个伟大的方式来自动识别潜在的问题，改变服务供应商之前，他们甚至部署。

所以使用 API 总是比操控图形用户界面更有益！

另一个绊脚石是 RPA 通常以**低代码**进行营销。又来了！几年前，我们已经看到 BPM 解决方案出现了这种情况。承诺是自动化核心流程，而不需要开发人员参与，甚至可能根本不需要 IT 人员参与。低代码爱好者并不认为这种方法是解决 IT 资源短缺的一种方法，而是一种不需要 IT 就可以做得更好的策略。这从来没有成功过，我相信永远也不会成功！在 Sandey Kemsleys 的白皮书“开发人员友好的 BPM ”中可以找到关于为什么的很好的背景阅读[。我看到了一个更有希望的场景，跨学科团队承认你需要两种职业才能成功。](https://camunda.com/learn/whitepapers/developer-friendly-bpm/)

除了公司的核心流程之外，可能还有一些小型和孤立的用例，在这些用例中，业务部门自行应用 RPA 是有意义的。这基本上与业务部门中的大量 MS Excel 或 MS Access 文件相同，或者是建立在像 [IFTTT](https://ifttt.com/) 这样的工具上的孤岛解决方案。RPA 不相上下，只是更贵。这可能没问题，只要它是一个孤立的使用情形，并且是有意识地为 RPA 决定的。但是要记住维护成本。一个很好的做法是，在你的公司里随机询问一些人，他们是否运行过基于 MS Office 的软件，以及他们对它的喜好程度。

**总结**:应用 RPA 就是 [**技术债**](https://en.wikipedia.org/wiki/Technical_debt) **。你不仅要在将来某个时候偿还，还要不断地支付利息(努力)。这使得改变或调整程序变得更加困难，这就是为什么“*机器人可能会使创新更加困难——并且更加缓慢*”([要避免的五种机器人流程自动化风险](http://www.oliverwyman.com/our-expertise/insights/2017/oct/five-robotic-process-automation-risks-to-avoid.html))。因此，在最坏的情况下，RPA 会让您更加远离现代、敏捷的 IT，而这正是您公司未来成功的基础！**

### 滥用止痛药(如 RPA)的危害

即使您意识到这些挑战，您仍然可以证明 RPA 项目的合理性。我经常听到的是:

> “我们的 IT 部门有太多的工作要做，无法投入到这个项目中。但是我们现在必须做点什么…”或者“这个项目的优先级不够高，无法获得 IT 预算，但是我们必须解决这个即时的问题…”

这些经理意识到 RPA 不是最佳解决方案，但这是他们的业务部门能够独立完成的唯一解决方案。本地 RPA 项目可能会让一两个员工的工作快速自动化，从而带来良好的投资回报，项目得以推进。虽然从部门的角度来看，这种行为是可以理解的，但从长远来看，它会给你的公司带来麻烦。

**RPA 是一种短期止痛药，而不是长期策略！**

从日常生活中，每个人都知道:在疼痛的情况下使用止痛药可能会缓解目前的情况，但这不是解决根本原因的策略。如果你不能避免止痛药疗法，确保它至少与一个适当的长期策略携手并进！

RPA 止痛药有两个主要风险。

#### **RPA 会导致维护成本呈螺旋式下降**

IT 资源的短缺通常是由于在维护现有应用程序上花费了太多时间造成的。这通常被称为“让灯一直亮着”，它阻止了你的创新和新项目(参见[如何平衡维护和 IT 创新](https://www.computerworld.com/article/2486278/it-management/how-to-balance-maintenance-and-it-innovation.html))；一些公司将 80–90%的 IT 预算用于维护。

由于 RPA 解决方案需要更多的维护工作，导致 IT 部门更加繁忙。这给下一个项目带来了麻烦，下一个项目可能会采用 RPA，这会导致更多的维护工作等等。

#### **只有拥有强大的技术眼光，才能吸引 IT 人才**

另一个问题是关于你的公司文化和心态。随着当前的数字颠覆浪潮，很明显**大多数公司本质上都变成了 it 公司**,因为它们只处理信息，而不是有形商品。以[的摩根大通和高盛为例，](https://thefinanser.com/2018/04/banks-technology-companies.html/)他们正在*从“部署科技的金融机构”转型为“分配金融的科技公司”* [(提奥多拉·刘](https://twitter.com/psb_dc/status/982641655705690117))。

body[data-twttr-rendered = " true "]{ background-color:transparent；}.twitter-tweet {margin: auto！重要；}

> JMorgan 正在成为一家“分配资金的技术公司”。真的很喜欢这张图表。许多保险、银行等。问我他们应该如何应对数字化转型。这就是你的答案，没办法绕过(RPA 当然不是)。[https://t.co/kP2NjIZHrT](https://t.co/kP2NjIZHrT)
> 
> — [@jakobfreund](https://twitter.com/jakobfreund/status/983586686310461440)

函数 notify resize(height){ height = height？height:document . document element . offset height；var 调整大小= falseif(window . donkey & donkey . resize){ donkey . resize(height)；调整大小= true}if (parent && parent。_ resize iframe){ var obj = { iframe:window . frame element，height:height }；父母。_ resize iframe(obj)；调整大小= true} if(window . location & & window . location . hash = = = " # amp = 1 " & & window . parent & # window . parent . postmessage){ window . parent . postmessage({ sentinel:" amp "，type: "embed-size "，height: height}，" * ")；} if(window . WebKit & . window . WebKit . message handlers & . window . WebKit . message handlers . resize){ window . WebKit . message handlers . resize . postmessage(height)；调整大小= true}返回调整大小；}twttr.events.bind('rendered '，function(event){ notify resize()；});twttr.events.bind('resize '，function(event){ notify resize()；});if(父&父。_ resize iframe){ var maxWidth = parse int(window . frame element . get attribute(" width "))；if(500 < maxWidth){ window . frame element . set attribute(" width "，" 500 ")；}}

在这种环境下，全球几乎每家公司都必须实现 it 架构的现代化，并建立一支强大的工程队伍。但是工程师可以在他们想工作的地方工作，这是一场人才大战！你认为一个顶尖的程序员想在哪里工作:在那些相信 IT 是他们的核心和支柱的地方——还是在那些试图避免真正的工程而去追求 RPA 和低代码的公司？

只有接受它的公司才能走在市场的前面，并长期生存下去。使用 RPA 止痛药可能会妨碍你。

不仅大公司如此。我知道一些小公司，在那里单个团队在整个企业中从根本上推动适当的自动化程序。这些公司表现出强烈的愿景，并公开谈论这一点，即使它们非常小、非常偏远或在保险等相当保守的行业工作，也能够吸引 IT 人才。

#### **但我无法影响我们的战略，该如何是好？**

许多项目可能没有选择，因为它们被生活在过去的高层管理者所困。在这种情况下，我的建议是:意识到 RPA 的缺点；小心使用，保持在局部，并尽快更换。或者，您尝试为 RPA 解决方案增加预算，但获得了自己决定所有细节的承诺。然后，您可以拿钱雇佣外部 IT 专家，进行适当的自动化。这需要勇气——但我们已经看到这带来了巨大的成果。

### 正确应用 RPA，考虑关注点分离

但是，让我们开始看到事情积极的一面，将注意力转向 RPA 的最佳点。成功的 RPA 的线索是尽早涉及它，并将解决方案分解成适当的部分，RPA 在其中扮演一个角色: **RPA 是为了与 UI 对话，而不是为了自动化业务流程本身。这仅仅是关于适当的分解，否则你将会得到一个将 UI 特有的东西和工作流逻辑混合在一起的意大利面条。**

您能在以下 RPA 流程中发现业务流程吗？

[![](img/c88a794d9924dcad63d8636b133574b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7v5xi0Sq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARE7KQprY2VoHNtMlKgk8yQ.png)

将这个流程与下面只关注业务流程的流程进行比较，请记住，到目前为止，我们只看到了快乐之路:

[![](img/3a61637df1f98569a759e6eb1af5d7c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8CX7kIIw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/850/1%2AhJ9iu0XOlmv2VEVjugXI7Q.png)

每当集成没有 API 的应用程序时，此工作流都可以调用 RPA 流:

[![](img/a3af12f80efa13feb85901c6103672a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h7g30Yjx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/914/1%2AprmGNLCToLeTQ1w7kh0FeQ.png)

尽管可见性有所提高，但这对于缓解开头提到的挑战有着巨大的优势。

例如，底层业务流程的变化通常独立于所涉及的应用程序的用户界面。这是双向的:如果您推出某个核心应用程序或简单的操作系统的新版本，UI 中的细节可能会发生变化，您需要调整 RPA 流程，但业务流程保持稳定。或者您想要优化您的业务流程，但是一个应用程序的集成可能一点也没有涉及到。

<figure>[![](img/5062d37d5738ac83919a74846033ff56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--46FOwnMz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/453/1%2AuCDBg6KgnbzO4is_5xm2zA.png) 

<figcaption>不要做一个泥巴大球，因为它需要在一个大范围内做很多改变</figcaption>

</figure>

通过分离业务流程逻辑和 RPA 流，您可以避免通常所说的[大泥球](https://en.wikipedia.org/wiki/Big_ball_of_mud)。这种方法保持了业务流程的可见性，并且不同的方面可以按照各自的速度独立地变化。

它还允许根据业务流程的自动化比率来发展业务流程。因此，您可能会开始引入工作流引擎，但即使开始时没有 RPA，也只是进行简单的人工任务管理:

[![](img/e6d3816aaaa7ac9f96e8a4f6e34dad61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mM5AYdB8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AC8BGh7iEcSmJiUwO6RGMWg.png)

现在您引入了 RPA，但是您可能希望保留**人工任务管理作为后备**，以防 RPA 流中出现错误。这使您可以专注于自动化 80%的案例，并将异常发送给人工:

[![](img/5e6df0f0761334f3b69c49254e45ef84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OAM_Lb6m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkRRTJcnnlPhvG2-5W85wGg.png)

只要有可能，您可以用真实 API 的适当集成来替换 RPA:

[![](img/fa7b5eed2c4b6ae4012a6316d1463f97.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xaIlZL6V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARNdUEial5Mh_D-80Wh4EAw.png)

你可以根据具体情况来决定。CRM 可能是一个石器时代的工具，需要通过 RPA 来集成，但是您已经可以从您相对现代的 ERP 系统中利用一个真正的 API。

### 结合 RPA 和工作流自动化

为了区分这些问题，除了 RPA 工具之外，您还需要一个工作流引擎或工作流自动化平台。工作流工具可以直接执行如上所示的模型(在 [ISO 标准 BPMN](http://www.bpmn.org/) 中)。您可以在[机器人流程自动化(RPA)和 Camunda BPM:在 BPMN 工作流程中使用 UiPath 机器人的演示](https://blog.camunda.com/post/2018/08/integrating-uipath-rpa-with-camunda/)和 [RPA 和 Camunda BPM:通过使用外部任务集成软件机器人](https://blog.camunda.com/post/2018/08/workfusion-rpa-with-external-tasks/)中找到使用开源平台 [Camunda](https://camunda.com/) (免责声明:该公司是我共同创建的)的工作示例——这些博客帖子展示了不同的集成工具和策略。

在上面概述的场景中，工作流引擎处于主导地位，每当它需要与不提供适当 API 的资源集成时，都会调用 RPA。因此，工作流引擎调用 RPA 工具的 API，例如通过 REST。这要求您的 RPA 工具提供一个 API。大多数都是这样，您应该明确地将此作为 RPA 评估列表中的一个必备标准！

[![](img/0cd5b2d8897701fa68c9f678029ad3de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FEZqst2w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/711/1%2Aswf3B3ylczageMXXLRcJPQ.png)

看集成时的一个基本区别是，你可以有有人值守或无人值守的机器人。无人值守的机器人完全靠自己运行——可能是在一台没有连接显示器的机器上。该处理可以通过 API 调用轻松启动。对于有人值守的机器人来说，任务、工作或工作项目被创建、调度，并且在他的计算机前的人可以稍后以有人值守的方式来拾取并执行它。细节取决于手头的工具，但从技术角度来看，集成通常非常容易，我很高兴与您讨论具体的场景。

一些合作伙伴报告说，他们也致力于为他们的机器人开发合适的领域驱动的 API。因此，从工作流的角度来看，您无法区分是调用一些真正的服务 API 还是机器人——因为两者都可以提供相同的 createSalesOpportunity 方法。

关于将工作流自动化和 RPA 结合在一个工具中的营销手册，有一个简短的警告，最常见的原因是低代码思想。实际上，我看不出核心支持 RPA 的工作流自动化平台有什么优势。这些问题是非常不同的，我倾向于一个最好的架构。这使得人们可以使用适当的工作流自动化平台，甚至可能在公司的更大范围内使用，并在任何有意义的时候使用 RPA，作为一种补充，以便在没有适当 API 的情况下集成软件，或者作为本地问题的孤岛解决方案。

### 总结

要取得成功，您必须承认 RPA 是一种过渡技术。尽可能通过适当的 API 进行集成。确保使用短期止痛药 RPA 不会阻止你制定正确的战略，该战略涉及对 IT 在公司中的重要性的承诺。你应该接受拥有更多的书呆子比低代码更好！

在正确的设置中，RPA 可以成为您工具箱中的一大亮点。它的用例是集成不提供 API 的遗留应用程序。将 RPA 工具与工作流引擎相结合，以实现底层业务流程的自动化。这是一个非常强大的组合，可以实现具有巨大优势的一流体系结构:

*   围绕工作流自动化的成熟方法、最佳实践和工具。
*   自动化业务流程的统一方法，包括 API 或 RPA 集成以及人工任务管理。这使得业务流程得以发展。
*   RPA 允许在没有适当的 API 的情况下集成工具，并避免集成遗留系统的死角。

如果您将 RPA 的使用限制在这个最佳点，那么遵循它的最佳做法将会很容易。这有助于解决部署、治理和灵活性方面的诸多挑战。

*感谢*[*Jan Stamer*](https://medium.com/@remast)*和*[*bene dikt Uckat*](http://www.linkedin.com/in/benedikt-uckat)*广泛的反馈帮助改进了本文。*

Bernd Ruecker 是 [Camunda](http://camunda.com/) 的联合创始人和开发商倡导者。我对开发人员友好的工作流自动化技术充满热情。*关注我的* [*Twitter*](http://twitter.com/berndruecker/) *或关注我的* [*简讯*](http://eepurl.com/cDXPRj) *。像往常一样，我喜欢收到您的反馈。下面评论或者[发邮件给我](http://bernd.ruecker@camunda.com)。*

* * *