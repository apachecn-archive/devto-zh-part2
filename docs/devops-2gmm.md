# 去了最新 DevOps 事例学习会

> 原文：<https://dev.to/okash1n/devops-2gmm>

> 原报道幻灯片被嵌入后更容易看到
> [https://oka sh1n.tech/2018/09/25/jdd study-3-devo PS /](https://okash1n.tech/2018/09/25/jddstudy-3-devops/)

由 JDD 先生主办的[JDDStudy #3 最新 DevOps 事例学习会！ 启动和格罗斯阶段各自的开发团队努力的 DevOps 的现在。 因为去了](https://techplay.jp/event/687946)，所以报告

# Summary

> 在产品开始时精益的时机、经过市场调整后使其光泽的时机，在各个阶段开发体制、运用体制、运用方法都不一样。
> 为什么会变成那个 DevOps 呢？
> 邀请对 DevOps 很讲究的 3 家公司，从工具选定和决策的背景，到实际运用的感想进行询问。 使开发更有效率！ 想的人，一边听最新 DevOps 事例，一边一起讨论吧！

# DevOps on Merpay Microservices -株式会社 Merpay 高木润一郎先生

[关注@tjun](https://twitter.com/tjun?ref_src=twsrc%5Etfw)
[![image.png (3.8 MB)](../Images/99e62ec9ac9f248b57e0861b3d4fcdb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lcUNE6JX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/20/30651/55d67b4a-b52d-4c42-8df4-de40c57b9dd6.png)

## 幻灯片

*   [20180920 DevOps on Merpay 微服务-扬声器平台](https://speakerdeck.com/tjun/20180920-devops-on-merpay-microservices)

## SRE 是什么来着？ ？

*   [谷歌云平台博客:SRE vs. DevOps:竞争标准还是密友？](https://cloudplatform.googleblog.com/2018/05/SRE-vs-DevOps-competing-standards-or-close-friends.html)
*   [devo PS–Matt Klein–Medium 的人类可扩展性](https://medium.com/@mattklein123/the-human-scalability-of-devops-e36c37d3db6a)

## Merpay 的体系结构

[![image.png (264.8 kB)](../Images/f746bb3243c6a84d0340f1bc9e69aff2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0ASOufKE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/20/30651/77b451cc-9f8e-4c90-b089-1e5673cf5d86.png)

*   大部分是用 Go 写的
    *   有类似 Go microservice template 的代码
*   用 gRPC 进行交流

## Merpay API 网关

[![image.png (240.8 kB)](../Images/bca79bd1821a032ae5635df0200f9703.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O9GowhLu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/20/30651/8b339a85-b941-45dc-b9d1-a0dc3066f930.png)

*   Merpay 方面也得到改善
*   CI/CD CircleCI ->集装箱注册-> Spinnakerで自動デプロイ

## 运用机制的通用化

[![image.png (277.5 kB)](../Images/f3fb18174fb751a96d01aeffe8fb9c7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xNa0h2td--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/48248bce-685c-44a8-a652-265ca878a92c.png)

*   在 Microservices 中，各服务团队成为 Owner 进行运用的技术选定也是自由的
*   尽管如此，为了支持 SRE 和其他团队的成员，需要通用化
*   运用机制的通用化在 DevOps 中很重要
    *   一旦制定了大家喜欢的结构，即使 SRE 想支持也做不到。
    *   监控和记录工具通用化

### 规则的通用化

SLO(objective)を服务水平決める

*   SLO :用于确定服务是否处于受信任状态的指标
    *   例:用 Gateway 测定的 API 的 Request 成功率为 99.99%
    *   没有指标的话，就无法判断要改正什么，什么时候紧急应对

### 工具的通用化

*   日志:堆栈驱动程序& big query，度量: Datadog，错误通知: Sentry，值班通知: Pagerduty
*   用 Microservice starter-kit 自动生成书写服务的 Team 和 Project 等

## Feelings

毕竟在日本是 SRE 先驱的煤炉的关联公司，在今天的发表中，我认为 SRE 做得最接近谷歌教科书的印象 SRE 团队的特点是以帮助开发者 DevOps 的存在为目标。虽然通用化这个词是个大词，但是 Merpay 的通用化这个词 毕竟是最低限度的部分，并不是什么都通用化，那最低限度是什么呢？

> **运用机制**的通用化

制作服务并进行缩放，最终就是运用服务，瞄准一定会发生的运用，由 SRE 担保每个 Microservice 的为了顺利运行 DevOps 的结构部分，这种态度今后也会引入到自己的团队中。

# IT 基础设施持续改善的实践- red hat 株式会社中岛伦明先生

[关注@irix_jp](https://twitter.com/irix_jp?ref_src=twsrc%5Etfw)
[![image.png (455.5 kB)](../Images/39db0193573d1c6ee62b910f6a984792.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJJjQMzp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/4a36fbd9-67f2-4b6c-93dc-759ff1e4b04c.png)

[![image.png (399.0 kB)](../Images/c19d1de09eb804cb6b49f420e53ab556.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vTNmvH-g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/7c1bd487-ad3f-4b41-949a-10643f254424.png)

## 对 IT 基础设施自动化的关注

*   Talk about success.
    *   QCD 得到改善，没有人不喜欢
*   众说纷纭
    *   想让眼前的工作自动化的人
    *   以整体最佳化为目标的人；以部分最佳化为目标的人
    *   自动化后就没有工作了，很高兴的人，很为难的人
    *   想改变至今为止的做法的人，不想改变的人
    *   包括周边在内进行思考的人，集中于重点进行思考的人

## 提高 IT 基础设施效率的三位一体

[![image.png (292.6 kB)](../Images/b04115242896e8af0f17c5c1d21d7dfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SDNV6RTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/c99f1062-1f5e-438d-85e6-d50815ec7248.png)

## 推进自动化时的烦恼

*   用 Ansible 等使眼前的工作自动化
    *   其实没那么难
*   实际努力的话会出现的烦恼
    *   这真的起作用了吗(结果达到了预期的效果)？
    *   在生产环境中运行不会产生影响吗？
    *   这个 Playbook 在别的环境下也能工作吗？
    *   这个 Playbook (半年前做的东西)现在能直接动起来吗？
    *   我升级了 Ansible，这个能工作吗？ ？

大部分是伴随着自动化的执行·再利用时间的经过的**关于自动化的正确性**的烦恼

## 什么是自动化的正确性

[![image.png (336.4 kB)](../Images/742f7be6e2656099e73d09ad187d9f11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XjWRHAo2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/330b70dc-87fe-4451-b71c-da73c09f40f2.png)

*   关于自动化正确性的烦恼
*   步骤书和规格书是用于在作业前验证担保的东西
    *   即使自动化了，关于设计工程的疑问和课题也越做越多

## TDD 性的基础设施的方法

[![image.png (386.2 kB)](../Images/e47cf9286ff18d2654e7ee172b073fdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xy1iq165--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/d2b16a22-78b2-4edd-8de0-ff5326c7a502.png)

## 如果可以自动验证自动化的正确性

*   能够高精度地计划和工作变更
    *   可以尽早向变更带来的影响派验证(测试有多少次错误等)
        *   不做什么时候做
*   技术诀窍的积累和尺度
    *   越运用**越不依赖别人**质量越提高
        *   高可靠性的防止再次发生作为**代码**积累
    *   准备横向展开

能够安全且低成本地应对变化

## 应对变化

[![image.png (349.2 kB)](../Images/f9b0ddf39c286f6b727db673be3712c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uKHAuuL4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/010a81b3-be1f-41c3-b250-ba04038b4067.png)

*   以应对变化为前提构建、运用系统更合理

## Feelings

首先，发表的感觉就像在教授这所大学听课一样(我大学还没毕业呢)
虽然和高木先生的话一样，是以发生变化为前提来建立基础的，但我再次认识到，通过用 IaC 来实施 TDD 的方法，建立越是运用质量就越高的机制，这一部分正是应该用 SRE 和 QA 来实施的部分

[使用基础设施 CI 实践指南 Ansible/GitLab 实现基础设施改善循环](http://amzn.asia/d/cKYtvQF)一书最近发售了！

[![image.png (409.9 kB)](../Images/1eac22587f4c29971931dd4fcb5e8e3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LnQXDJGN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/7d02453c-a9d2-401f-a0d9-52c49b728225.png)

# 作为 DevOps 实施者的 SRE 的存在和作用-株式会社研究人员北野胜久先生

[关注@胜久 __](https://twitter.com/katsuhisa__?ref_src=twsrc%5Etfw)
[![image.png (457.7 kB)](../Images/1cdbe1b812c31832fd7864f8b3668ae9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--opVKZKN7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/21/30651/b18d232e-29b8-445c-8ac2-fec5a6a46597.png)

## 幻灯片

*   [DevOps 実装者としてのSREの存在と役割/ class SRE 实现 DevOps - Speaker Deck](https://speakerdeck.com/katsuhisa91/class-sre-implements-devops)

## Feelings

2010 年 3 月创业的[株式会社 studistors](https://studist.jp/company/)先生和 2011 年 3 月创业的本公司[asobe 株式会社](https://www.asoview.co.jp/)一样，开始在光泽阶段进行 SRE，所以已经在某种程度上对混乱的服务进行了杠杆化

说起来，我参加这个学习会的是北野写的报道[SRE 是什么？ 与至今为止的系统运用和 DevOps 有什么不同？ 因为读了](https://codezine.jp/article/detail/11002)之后用 Twitter 等取得了联系，然后在他的 Tweet 上看到了。因为这篇文章比这次的幻灯片更详细，所以那边也一定要注册会员才能全部阅读，但是我想读这篇文章，所以注册了 w

### 类 SRE 实现 DevOps - SREはDevOpsの実装

*   DevOps
    *   是为了把 Dev、Ops、其他整个团队卷入一起推进工作的文化运动和方法
*   SRE
    *   软件工程师设计 Ops 的实践，实现 DevOps 的作用
    *   不像 DevOps 那样以文化变化为前提

### 为了安装包括 SRE 在内的 DevOps

无论是 SRE 还是 TDD，更多的是各论 IaC、Kubernetes、分析基础，如果是工程师的话是**大家都想做的**，对吧？ 但是，很多人觉得好不容易提出来了也不能顺利进行吧？
关于这一点，有一个说法是**数据和显示研究结果的**

[![image.png (1.5 MB)](../Images/8bae2c5916e00b762808be8ba96468da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B4JVYuKm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/22/30651/a8575b9c-4ace-4fe0-a731-2a663512b086.png)

我从上一份工作开始就一直在考虑提案的方法，但是我最近发现，对于没能顺利提出的提案，很多时候只会撒下这种让人困扰的事情，这不是更好吗？相反，顺利进行的时候，是充分提取数据的时候。 因为感觉到了，所以感觉到了“咔嗒”的一声。然后最近开始觉得在 DevOps 上再加上 Biz 这样的想法，也就是说提出考虑了经营视线和其他团队以及运用的提案是最重要的，但是我想最近再好好总结一下写报道。 itepaper 好像每年都会在[这里](https://puppet.com/resources/whitepaper)出现(发表中介绍的是[2014 state of devo PS report–thank you|puppet](https://puppet.com/resources/whitepaper/2014-state-devops-report/thank-you))

### 为了队伍的规模

[![image.png (903.5 kB)](../Images/4bd56cb29bcc0f393d8a6c6b70eca885.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kQj_vrJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/22/30651/906639d0-2a26-470b-8208-c8cc30b38484.png)

为了服务的规模，团队和每个人都必须规模，为此要做什么呢

*   输出公司内部学习会和外部学习会，进行**储备**
    *   很快在周一分享给开发团队
*   跨越开发团队的回顾实践
    *   [读了《恺撒·杰尼斯》后试着建立了回顾部–研究开发博客–medium](https://medium.com/studist-dev/kaizen-journey-c13c88cbef61)
*   后现代主义
*   在推特上收集信息
    *   **Twitterは仕事**

实际上，如果没有通过 Twitter 或 Facebook 联系北野的话，就不会参加这个学习会，而且我想北野也没有注意到在后述的联欢会上交换信息。嗯，据说北野还主办了一个叫[SRE Lounge](https://sre-lounge.connpass.com/) 的学习会，这个月已经结束了

# CI/CD 自动化与 DevOps 抽象的尝试- Japan Digital Design 株式会社 Takuya Noguchi 先生

[关注@tn961ir](https://twitter.com/tn961ir?ref_src=twsrc%5Etfw)
[![image.png (555.5 kB)](../Images/15b65101e93ace5e594ea6682528a363.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gloOEhcL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/22/30651/79af7e66-b5ef-41cf-9940-508bf89eb89b.png)

## 幻灯片

*   [JDD devo PS-演讲台](https://speakerdeck.com/tnir/devops-in-jdd)

主办 JDD 的野口给我的第一印象是来了一个了不起的人啊，据说这位是 Gitlab 的核心工程师(在日本是一个人)

## 介绍了什么是 DevOps 的文档

*   [有效 DevOps—O'Reilly 电子书|微软 Azure](https://azure.microsoft.com/ja-jp/resources/effective-devops/)
    *   可以从 MS 上免费下载哦
*   [每天超过 10 次部署:Flickr 的开发和运营合作](https://www.slideshare.net/jallspaw/10-deploys-per-day-dev-and-ops-cooperation-at-flickr)

## Feelings

[据说 Japan Digital Design 株式会社](https://www.japan-d2.com/)先生从 MUFG 集团切出 Fintech 部分，有意识地从外部和其他行业积极采用。有的人每周只来 23 天，有的是远程办公，工作时间也完全不同 在做 DevOps 的公司中，总之通过消除 GUI 进行编码，创造了以后其他成员可以回头的状态，这一点让人印象深刻。当然这不是异步工作方式也是必要的，但正因为是异步工作方式，所以更是必要的这里

关于文件化和编码，最近有想的地方，特别是障碍应对应该被储备起来。基本上服务不是一个人做的，运用和变化障碍等于大家**一定会发生**。这是今天所有发表的共通之处。 也就是说做好准备的动作对于包括 SRE 在内的 DevOps 来说是必须的，而且考虑到这些，我觉得自然而然地就能看到在提出提案时所需要的立场了

# sincere meeting

在精神饱满的 JDD 副社长的干杯上还举行了联欢会

[![image.png (506.1 kB)](../Images/d3c154d3e25b38446103664aff71c8c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3au0t2ui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/22/30651/6c4a75e3-7387-4961-9fbc-0513a4424ad9.png)

菜也很好吃

[![image.png (497.0 kB)](../Images/4967e84aa505e869aaec92e4aaaf2c0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--13ceTDqn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esa-upload.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/7996/2018/09/22/30651/a065bc15-3ca7-4d88-9479-06fa8095505f.png)

我想这一定和每个月在 Sansan 举办的公司内部联谊会是同一家餐饮公司吧。上个月去表参道. rb 的时候也吃过，但是吃了这个饭团就会想起 Sansan (虽然还是辞职两个月左右)

在恳谈会上，我和野口、北野、JDD 的工程师直接对话，还和其他很多公司的人进行了交流

## 番外篇联谊会上的对话

在联欢会上和煤炉的[@ktykogm 先生](https://twitter.com/ktykogm)进行了交谈，其中有几个特别值得参考的地方

### 自动化的标准

*   总之每天用的东西最好自动化
    *   完全自动化对人类来说还太早
*   后勤业务也将通过 zapier 等实现自动化

### 如何让 open door-za pier 等扎根于非工程师呢？

Open Door 这个故事特别有意思。这么说来，公司内部学习会、意见交换会、自由讨论什么的都不是听了之后决定时间和会场好好开，而是抱着这个时间在这里，随时可以随时来回家的态度开的会。 T0】比如说在教非工程师用 Zapier 实现固定业务等的自动化的时候，也可以悠闲地打开 3 个小时左右，所以 5 分钟左右就可以了，就像来听我说的那样，在公司内部广播，在人来之前正常工作，到有人来的时候停下手来告诉你这样的事情 据说通过降低来传播新事物，有心理障碍是不能传播的。居然举办了这样的学习会，据说 2~3 个月就在非工程师那里也扩展了 Zapier 上每天的业务自动化(虽说是再高潜力的煤炉职员也太厉害了)

当然，据说不仅是学习会，关于开发业务、新功能及其他服务的事情，不管是非工程师，在煤炉也有很多公司都是抱着随时来的想法召开的。最近本公司各种部门卷入的对话也在增加，但引入 Open Door 后更是如此 因为我想，所以我想马上带回去

### 用煤炉交流

降低心理障碍似乎还是很重要的，据说在煤炉，在社交平台上，特别是经理等地位较高的人不带头的事情，都在社交平台上投下了。确实，即使是以前的 Sansan，也是从 Workplace 的个人团体等进行的半闲聊式的对话中产生的

*   创造可以说的文化很重要

是的，因为害怕的话就不想来公司了，顺便说一下，据说这个也在研究中出现过(果然数据很重要- )

# 整体感想

各种各样的发表和恳谈会上的对话都有共同点，组合在一起更能理解，饭也很好吃，总的来说是个很好的学习会。听说 11 月份也会以 DevOps 系列的主题进行 JDD Study，所以我想再去