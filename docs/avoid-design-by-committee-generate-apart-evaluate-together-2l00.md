# 避免委员会设计:分开生成，共同评估

> 原文：<https://dev.to/lpasqualis/avoid-design-by-committee-generate-apart-evaluate-together-2l00>

这篇文章最初在 [CoderHood](https://coderhood.com) 上全文发表为[避免委员会设计:分开生成，一起评估](https://www.coderhood.com/avoid-design-by-committee-generate-apart-evaluate-together/)。CoderHood 是一个致力于软件工程人类层面的博客。

[![](img/9224940d78ded51af1ef9295ee714e30.png)T2】](https://www.coderhood.com/avoid-design-by-committee-generate-apart-evaluate-together/)

科技行业因创新而繁荣。构建创新的软件产品需要不断的设计和创造性解决方案的架构。在这种情况下，我不是委员会设计的粉丝；事实上，我认为这与其说是一种策略，不如说是一种疾病。它折磨着没有领导或领导不明确的团队；过程是痛苦的，结果是糟糕的。

可用性问题困扰着委员会设计的软件产品。这种产品看起来像是粘在一起的功能集合，没有统一的视觉或独特的感觉；它们就像洋葱，由一系列松散连接的层构成。他们不能给用户带来情感，因为情感是个人激情的产物。

委员会的决策和设计让人感到安全，因为有一种共同的责任感，而这种责任感根本不存在。一个群体争取一致的愿望压倒了考虑和辩论不同观点的动机。它将创造力和思维能力简化为建立共识的练习。

建立共识是促使一群人想法一致的行为。关于那个话题，[沃尔特·李普曼](https://en.wikipedia.org/wiki/Walter_Lippmann)有句名言:

> 当所有人的想法都一样时，就没有人在思考了。

换句话说，建立共识就是敦促人们谈论，直到他们停止思考，直到他们最终达成一致。没有多少伟大的想法或设计是这样产生的。

我相信伟大思想的种子是作为每个人独特的创造过程的一部分而产生的。如果你强制要求所有的设计都必须在一个小组环境中当场制作和评估，你就没有给内向的创意头脑时间来进入他们最好的专注和创意状态。另一方面，如果你强迫所有的想法在没有集体头脑风暴的情况下孤立地产生，你就错过了收获集体智慧的机会。

这就是为什么我喜欢实现一个混合模型，我称之为“分开生成，一起评估”我将在软件和架构设计的背景下讨论这个模型，但是你可以很容易地将它扩展到任何需要个人创造力和集体智慧的领域。像大多数模型一样，我并不是建议您应该将它应用到每个设计或决策中。它不是银弹。把它看作你工具箱中的另一个工具。我建议它主要用于大中型企业、重大设计挑战、大中型转变和基础架构选择。

## 分开生成，一起评估

我相信创造性过程的力量，这种过程始于一次或多次集体头脑风暴，旨在提出一个问题，评估需求，引出并评估尽可能多的最初想法。在这个过程中，技术领导者引导团队讨论可行的替代方案，但将讨论保持在“方框和箭头”的高层次。想象一下白板上的数据流图或最重要概念的思维导图。讨论设计和架构的人群不应该陷入混乱。

在高级构思会议期间的某个时间点，团队选择一个人作为**想法/提案生成驱动者**。小组会议结束后，该人员负责研究、创建、记录并最终展示更多想法和详细的解决方案选项。如果问题空间很大，小组可以选择几个驱动因素，每个驱动因素对应一个不同的领域。

提案生成驱动程序不必孤立地完成所有工作。他或她可以与其他人合作提出建议，但他或她需要主导对话和想法的产生。

在驱动程序产生数据、想法和建议后，小组再次开会**集思广益，评估和提炼材料**。在这一点上，团队可能会识别新的驱动因素(或者维护旧的驱动因素)，并且这个过程会反复重复。

如前所述，我用一句口头禅“分开生成，一起评估”来指代这个迭代过程请不要把它解释得过于字面化和绝对化。首先，有想法产生作为一个群体发生，但它大多是在一个高层次上。此外，还有一个对小组之外的想法的评估。“分开生成，共同评估”是一个提醒，以避免委员会设计，杂草中的小组会议，以及对所有想法和解决方案的孤立评估和选择。

我用这张简单的序列图综合了这个过程:

[![](img/f47dc2601356bbb57b65378723a9a1f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xdqErXoV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d3c3diriz1pecynk6knx.png)

## 评估

“分开生成，一起评估”的“评估”部分指的是上面提到的两个不同评估阶段中的一个。

**初始评估**是对业务需求的小组评估，随后是一次或多次头脑风暴。主要目标是构建问题，并创建一个可能的高级架构和技术选择和问题的初始列表。第二个目标是选择产生创意的驱动因素。

**迭代评估**是对驾驶员在非小组环境中产生的想法和建议的定期小组评估和细化。

在这两种类型的评估期间执行的一些活动是:

*   集体讨论业务需求和解决方案。
*   建议生成驱动因素的标识。
*   确定后续步骤。
*   审查提案。
*   辩论提议解决方案的利弊。
*   选择高水平的技术方向。
*   争论。
*   同意并提交或不同意并提交。
*   建立沟通策略。
*   估计。
*   汇报，意味着驾驶员向小组汇报并提出想法和建议。

请注意，我们没有试图达成共识。团队评估并承诺一个方向，不管是否有一致的意见。要做到这一点，需要强有力的领导。

## 生成

“分开生成，共同评估”中的“生成”部分指的是一个人的单独或小组活动，他致力于推动提案和详细解决方案的创建，以提交给更大的小组。这些活动包括:

*   研究问题
*   确定研究思路并进行研究。
*   收集信息。
*   确定解决方案。
*   撰写提案、文档、用户故事。
*   估计用户故事。
*   编写/测试/维护代码。
*   实验。
*   报告(指驾驶员向小组报告研究、调查等的结果。)

## 思维导图

为了将来参考，你可以用思维导图记住这个方法的主要思想:

[![](img/c872edaa6b64bf19cefcb8eb60900ed0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uEhzkk9V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/optm39g6la3h98fz0vdv.png)

## 最后的想法

我没有发明标语“分开生成，一起评估”，但我不确定是谁发明的，我在哪里听到的，或者其初衷是什么。我试图找到答案，但谷歌并没有多大帮助。不管怎样，在这篇文章中，我描述了我如何使用它，它对我意味着什么，它解决了什么样的问题。这个模型我用了很长时间，最近才给了它一个贴在脑子里，不用太多必要解释就可以用来描述方法的标语。

* * *

### 如果你喜欢这篇文章，请保持联系！

*   在 CoderHood 上找到我所有的帖子。不要忘记订阅邮件来接收新帖子的通知。
*   在 LinkedIn 上加入我的职业网络。
*   在推特上关注我。
*   加入我的脸书主页。
*   最后，请在 dev.to 上关注我！