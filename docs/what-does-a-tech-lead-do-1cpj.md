# 技术领导技术领导做什么？

> 原文：<https://dev.to/backendandbbq/what-does-a-tech-lead-do-1cpj>

*最初发表于我的博客:* [你所需要的只是后端](http://allyouneedisbackend.com/blog/2018/08/03/what-does-a-tech-lead-do/)。

[![What Does a Tech Lead Do?](img/b9fb1fc188bb2f9283f27789579300c9.png "What Does a Tech Lead Do?")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EtcLItPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9sfpknhwedy82i11v0oz.png)

在软件开发组织的层次结构中，技术领导是一个相对较新的角色。当我第一次听说这个角色时，我的第一个想法是

> 是 a 那个**软件架构师+团队负责人**？

我不认为这个定义是正确的，但这是一个很好的思考方式。在这篇文章中，我回顾了我在这个职位上 3.5 年的经历，包括:

*   领导 **[大西洋步幅](https://www.stride.com/)** 的团队之一——完整的团队沟通解决方案。在将近两年的时间里，这个团队有了 5 到 10 名工程师。
*   领导 **KPIdata** 一个非盈利组织，该组织开发了评估[基辅理工学院](https://en.wikipedia.org/wiki/Igor_Sikorsky_Kyiv_Polytechnic_Institute)高等教育质量的软件。团队扩大到 10 名核心成员(包括我在内只有 3 名软件工程师)，最终，180 多名个人贡献者帮助我们交付了项目。
*   在 **[视频互联网技术有限公司](https://www.linkedin.com/company/vit-ltd/)** 领导一个由 4 名工程师组成的团队(包括我自己)进行视频管理系统(CCTV)的集成。

请注意，相同的职位在不同的公司可能有不同的职责。

看看这篇博文，了解一下我作为软件系统全职所有者的真实情况。我详细阐述了处于技术领先地位的利弊。

从实践的角度来看，这个职位最关键的技能列表在博客的最后提供。

## 成为全职老板

我在这个职位上发现的第一件事是，现在**我 100%负责**一个工程组织的一个部门。好的一面是，新的章节还没有任何作品。因此，我没有来自以前维护者的任何遗留代码来支持和扩展。那很好。

然而，这不是规则，每个公司都不一样。我认为，更多的时候，你有机会增强现有的软件系统，而不是从零开始创造一些东西。所以，要做好准备，为那些不是你的团队启动和设计的项目负责。

做全职业主意味着什么？

*   你收到的任务都与特定的业务目标相关联。其实任务就是项目。此外，可以部分定义需求。你需要继续前进，找出所有的要求和限制。您希望尽可能地指定项目的预期结果，以防止范围蔓延。理解和定义最终目标是第一步。
*   为了实现目标，你可以在你的时间/预算内做任何对你来说合理的事情。我们将在下一节详细探讨这一点。
*   为实现目标而开发的软件的所有成功(和失败)都与你有关。如果你的系统出了问题或不能按预期工作，那是你的责任和过错。在超额完成目标的情况下——干得好！但是不要忘记表扬你的团队取得的成功。这是人民应得的。

## 工程创意的空间

是的，为了实现工程目标，你可以做任何事情。这是我能够改变或实现的事情的列表。请注意，您应该得到团队的认可，以使变更持久。人们制作软件。快乐的人制造工作软件。

*   **软件开发方法论**很大程度上取决于项目的目标和期限。回答问题以定义:
    *   一次迭代中有多少天？
    *   规划流程是怎样的？应该评估哪些任务？如何估算任务？
    *   在迭代之间，我们是否应该接受需求的变化？
    *   不同类型/优先级的任务有什么规则？示例:无论严重性如何，必须尽快修复*计费*组件的所有错误。
    *   如何向组织的其他人演示？
*   项目的技术堆栈。它可以包括但不限于编程语言、框架、数据存储、库、监控解决方案。有时你有一些由公司政策规定的预定义的预设。对于我们的章节，堆栈如下:
    *   Python 3，异步编程，asyncio
    *   MySQL，弹性搜寻，Redis
    *   自动气象站(EC2、RDS、ElastiCache、S3、SQS、CloudFormation、CloudWatch)
    *   DataDog，Elasticsearch/Logstash/Kibana，ElastAlert，Splunk
*   **软件架构**。你定义软件系统的结构部分。你可以创造新的东西。您可以重用现有的公司内部或第三方服务。如果你是技术主管，设计不同组件之间的接口也是你的责任。祝你玩得开心！
*   **非功能性需求**。这是关于定义*足够好*和*完美*软件之间的界限。我从未被鼓励做出一个理想的商业解决方案。通常，人们*只是*需要一个稳定的解决方案来解决他们的业务问题。解决方案应该足够灵活，让我们能够快速应用新的变化。对我来说，这意味着为工程师设定合理的期望，让企业快乐。示例:
    *   该组件应该对数据库重启有弹性...
    *   ...但是如果 60 秒内无法建立连接，请注意
*   **内部里程碑**。您可以为项目的不同阶段设定团队的关注点，并为此定义可交付成果。
    *   例如，可以对项目路线图进行优化，使系统版本尽快投入生产，以建立 CI/CD 渠道，并确保您的想法基本可行。
    *   另一个例子——你可以让你的团队尽可能的自治(当你们在地理上分散的时候，这是一个好主意)——然后你需要花更多的时间来计划定义独立的工作流。
*   **服务水平指标**。作为技术主管，您负责定义您的软件何时提供所需的服务质量。选择反映业务现实的正确指标集至关重要，因为它为您的团队设定了目标，也为工程改进指明了方向。根据我的经验举例:
    *   **可用性。**服务可以用吗？
    *   **已处理作业的数量。**我们还需要这项服务吗？我们做了多少有用的工作？
    *   **主成分成功率。**-帮助我们看到中间层次的问题。
*   **首次展示时间表**。它包括将软件部署到不同环境的频率。
    *   一旦拉请求被合并
    *   或者每 4 个月发布一次。
*   **沟通。**团队如何沟通每日进度？
    *   每天两次 30 分钟的视频通话
    *   每周一次文字站立*(也许)*
*   **分工**。你的吉拉中的任务是如何分配的？
    *   你把每个任务分配给每个工程师，没有你的书面许可，他们没有任何机会改变(不是很好的策略)
    *   每个人都可以接受任何任务，不管优先级和依赖性如何
*   代码审查政策。谁应该批准一个拉取请求，让创建者将其合并到主服务器？选项:
    *   共识——回答了所有问题，所有默认审阅者都批准了变更
    *   至少应获得 2 名高级工程师的批准才能继续
    *   我可以在最后一次提交 2 小时后批准我的 PR 并部署
*   **龙刚。多长时间做一次？我的建议是每 4 周一次，但我知道有些团队每 2 周一次。顺便问一下，你多久做一次？**

我省略了一些东西，所以请随意添加您的想法作为评论。

## 技术主管的技术水平如何？

我的任务是让团队实施正确的解决方案。

> 你每天不需要写很多代码

在我成为最新团队的技术负责人之前，我在同一领域同一组人中的中级/高级软件工程师职位上工作了 1.5 年以上。对我来说，获得异步编程、关系和非关系数据库、即时消息和高负载系统的实践经验是必不可少的。

为了让你的项目成功，首先你应该**阅读**大量的:

*   密码
    *   你的团队提出的请求。
    *   您的系统重用的解决方案。
    *   您需要与之合作的其他团队维护的第三方服务的代码。
*   技术资料
    *   您可以重复使用的服务的描述(内部和第三方)。
    *   解决方案的实施细节。
    *   他们的已知问题(没有什么是完美的)-了解风险并为他们制定缓解计划。

在阅读完你的**之后，写下**一点:

*   工程建议- [DACI](https://www.atlassian.com/team-playbook/plays/daci) 是一个有用的框架。我喜欢它。
*   提案确定后-设计页面。
*   在最后，一些工作的门票(我的团队运行在~~咖啡因~~吉拉软件)。

写完后，你们**讨论**:

*   就重要的任务与你的队友达成一致。
*   如果您有一个不完整的规范或者没有提供所有的数据来源，请教育您的队友。
*   与其他团队谈判合同。
*   展示您的工作成果，并在公司内推广您的解决方案。

在一天结束的时候，你可能有几个小时来做个人贡献。对我来说，大概是这样的:

*   修补程序。在世界即将爆炸的时候需要修复一些东西。
*   在不编写测试的情况下，对拉请求进行概念验证。之后，请团队中的某个人将其转化为生产级软件。
*   提交数据库或配置更改。
*   调查一个在开发环境中很难重现的怪异 bug。
*   从指标/日志记录解决方案中提取一些数据，以验证实施想法。

我认为一个技术领导者应该有扎实的软件工程实践经验，能够做出并支持合理的决策。

> 在小团队(最多 3 名直接下属)中，我认为仍然有可能做出一些不错的个人贡献。

在写这篇文章的时候，我还没有充分发展我的工程领导技能来为更大的团队做出可持续的个人贡献。

## 成为技术领导者的利与弊

**优点:**

*   你成为项目领域的主题专家。
*   你完全了解软件系统如何工作，以及如何以最小的风险对其进行修改。你现在可以把它复制到其他系统。
*   你成为一名优秀的沟通者，因为你负责理解需求和解释技术解决方案。
*   您在软件开发的各个领域都达到了一定的能力水平(虽然并不总是很高):
    *   **系统设计** -在早期阶段设计你的软件并验证所有的风险。
    *   **运营** -保持您的系统正常运行。
    *   **质量工程** -防止贵公司声誉受损。
    *   **工程管理**——将实施委托给你的团队甚至其他团队。

**缺点:**

*   在一天结束时，你通常没有成就感。你为你的团队创造了一些新的工作，解决了一些障碍，但感觉不像是真正的工作。
*   大型团队没有足够的编码。
*   你是你团队的切入点。您应该能够接受来自多个来源的任务:
    *   你的队友
    *   你的管理
    *   伙伴团队
    *   客户支持团队
    *   其他听说过你的团队的人
*   有时候压力很大，因为责任很大。最终，你应该学会如何处理这一切。

我认为这个职位值得一试，我很高兴我有机会在这个职位上服务多年。我会再做一次。

## TL-启动包

如果你对技术领导的职位感兴趣，并愿意为此做准备，他是我在这条道路的最开始就发现有价值的技能列表:

*   **从你的堆栈中获得编程语言的实际熟练程度**——能够做出好的技术选择，并进行代码审查。项目的正确开始是至关重要的，因此您的编码技能可以极大地帮助您定义结构和基本组件。
*   **与数据存储相关的良好技能水平** -我认为在大多数项目中，你会处理从某处读取或存储的信息。此外，知识是系统设计能力的完美基础。
*   项目管理:在新的多任务环境中组织你的工作和其他人的工作。
*   沟通技巧 -这个职位是关于让其他人做技术工作的。

我相信这 4 个技能足够了，其余的技能可以在项目期间建立在它们之上。我希望这篇博文有助于提高软件团队的技术领导力。

在博客中，当我说“你做某事”时，意思是“你对某事负责”作为一名技术主管，你可以将一些复杂的工程委托给团队中的专家，但能够验证、批准或纠正解决方案。此外，成为决策者并不等于成为独裁者，无视其他人的声音。

**P.P.S.** 在我看来，**团队领导和**技术领导的区别在于职责:

*   团队领导对人负责，而不是对项目负责。
*   团队领导负责人员管理。
*   团队领导不应该做出个人贡献。

**P.P.P.S.** 还有，在我看来，**架构师和技术负责人的区别**:

*   建筑师有更多实际和多样的经验。
*   更广泛、更复杂的系统需要架构师。
*   架构师职位更多的是做最费力的工作，而不是让团队的其他成员做所有的工作。