# SCBCN 2018:我的两分钱–第 1 部分

> 原文：<https://dev.to/apium_hub/scbcn-2018-my-two-cents--part-1-4g3o>

在 2018 年 9 月的最后一个周末，举办了第六届 Scbcn([Software Crafters Barcelona](https://scbcn.github.io/))。 [Apiumhub](/) 是那里的白金赞助商，并有机会以[的激励演讲](https://dev.to/apium_hub/apiumhub-sponsors-scbcn18-practical-workshops-3chn-temp-slug-3500902)为大会揭幕。此外，Albert Parron——Apiumhub 的前端架构师，发表了关于无控制器架构的 MVP FRP 的演讲。

有几个会议，这些是我从我能参加的会议中得到的一些笔记。

## SCBCN 2018:亮点及注意事项

### **自动化一切，你将赢得[大卫·安东·卢](https://twitter.com/davidantonlou)**

David 给了我们一些工具来自动化开发的不同阶段。他的建议是:(几乎)总是(几乎)将所有事情自动化。

主要原因:如果你将某件事自动化，它肯定会运行得更快，你会节省时间，让你更专注于业务。您还将减少人为错误，并可能简化环境。

**代码自动化**

在开始编码之前，总有一个重复的部分:搭建 app 的骨架。我们不要每次都重复。

*   [龙目岛项目](https://projectlombok.org/)

一个 java 库来创建所有重复的代码(getters/setters/hashcode/equals……)。

在我看来，所有这些特性都是 Kotlin 直接给出的，所以如果你的项目已经是 java 的，就没有必要使用额外的库或者改变什么。像 IntelliJ 这样强大的 IDE 也能为你做一些工作。

*   [spring initialzr](https://start.spring.io/)

一个用 Spring 生成引导程序的工具。你可以在 maven / gradle、java / kotlin / groovy、spring boot 的版本及其依赖项之间进行选择。一旦构建完成，您就可以开始编写应用程序了。

*   杰普斯特

另一个高度可配置的应用程序生成器。在这种情况下，它构建整个应用程序(后端+前端)。要使用的一些工具是 yeoman + spring boot(用于后端部分)+ angular / react(用于前端)+ deployment 和 CI / CD 选项。

**包装**

代码一旦完成，就必须打包..我们经常这样做，所以必须自动化。

*   蚂蚁

它基于一个带有指令的 xml 文件。它没有依赖控件。

*   [胃](https://maven.apache.org/)

仍然基于 xml 文件，但它有依赖控制。

*   【T0 度】T1

它有自己的基于 Groovy 的领域特定语言(DSL)。它是这三者中最强大的。您知道吗，在 gradle 中，您可以执行 build.xml 中定义的 ant 任务。

**测试自动化**

当出现以下情况时，自动执行所有测试:

*   重复测试
*   压力测试
*   长时间执行的测试
*   关键业务部分

您可能不会测试零件:

*   随着不断的变化
*   非常复杂

**开发环境**

每次有新人加入项目时都要进行环境配置，这可能会非常繁琐。通过以下方式实现自动化:

*   [流浪者](https://www.vagrantup.com/)
*   [码头工人](https://www.docker.com/)

在 [Apiumhub](/) ，我们的赌注是 docker，它在构建和部署部分对我们帮助很大。

**自动化部署**

您已经完成了所有工作，最后一步是将其部署到服务器。

*   [可回答的](https://www.ansible.com/)

它为不同的环境保存配置，并帮助部署构建。

### 多度量少痛苦由[莫德斯托圣胡安](https://twitter.com/msanjuan)

在我们这个领域，当我们想要提高我们 app 的性能时，减少没有服务的时间。我们应该根据度量标准来行动，而不是我们的感觉或其他概念。关于这个问题的三个问题是:

1.  我在努力改善/避免什么？
2.  我该如何衡量？
3.  我该怎么解决？

**工具**

*   [格拉法纳](https://grafana.com/)

创建仪表板的平台。很酷的视觉效果。

*   [普罗米修斯](https://prometheus.io/)

监控和创建警报的工具。丰富的指标。

使用 prometheus 收集数据并在 grafana 上显示结果是了解项目指标的绝佳组合。

一个简单的例子:

1.  就在新部署之前，在格拉夫纳设置一个标记
2.  部署
3.  从这一点来看，您可以很容易地跟踪更新是提高了性能还是降低了性能。

*   [AppDynamics](https://www.appdynamics.com/)

它显示了资源的可视化以及它们之间的交互方式。

主要思想是使用度量来改进您的所有流程:

*   检查数据库中最昂贵的查询
*   改善您的 CI / CD 检查需要多长时间
*   观察您的生产环境(ping 错误、错误请求……)
*   避免云意外:查看云费用，看它们是否发生变化
*   避免信息过多！

信息的一个很好的焦点是混合仪表板，如:

*   登录+购买+响应时间+部署

在一个视图中，很容易看出哪些变化会影响您的业务。

### 平行变化由[爱德华多·费罗·阿尔达马](https://twitter.com/eferro)

Eduardo 举办了一个关于并行变更或如何逐步改进代码的优秀研讨会:

*   让我们经常添加小变化(CI)
*   有效地使用遗留代码
*   进化中的建筑

**做改变**

换的时候注意…

*   Ui

    ## -use feature switch

维护多个分支的一个很好的替代方案。

```
- 
```

马丁·福勒的定义

```
- 
```

从他的博客中，有另一篇[优秀文章](https://martinfowler.com/articles/feature-toggles.html)。

*   必须修改视图时复制视图
*   如果视图没有连接到主导航，它可以很容易地更新

    *   在沟通中，要记住一个很好的原则:

稳健性原则([又名粉彩定律](https://en.wikipedia.org/wiki/Robustness_principle))

发送的东西要保守，接受的东西要开明。

只检查你需要的，忽略其余的。

*   关于逻辑或性能

*   抽象分支

同样，在 Martin Fowler 的文章中可以找到更多信息。

“抽象分支”是一种以渐进的方式对软件系统进行大规模变更的技术，它允许您在变更仍在进行中时定期发布系统。

*   新变化后:

    *   数据需要验证
    *   衡量绩效
*   体系结构

*   扼杀者应用

这是马丁·福勒关于这个话题的文章，是的，又一次！

围绕旧的(仍在生产中)创建一个新的系统，直到旧的应用程序被完全扼杀，这样它就可以被删除。

在[的另一篇文章](https://paulhammant.com/2013/07/14/legacy-application-strangulation-case-studies/)中，有一些使用这种技术的详细案例研究。

github 的[科学家](https://github.com/github/scientist)是进行并行变更时的一个有用工具。

Scientist 是一个测试 Ruby 重构、重写和性能改进的库(现在有几个到其他语言的移植)。例如，它运行旧代码和新代码，这样就可以比较两次运行的结果。

github 工程博客上有一篇[文章](https://githubengineering.com/move-fast/)讲述了他们如何使用它来对他们的代码进行重要的修改。

**空地**

第一天的部分时间用于开放空间。任何对特定话题感兴趣的人都可以提出一个非正式的演讲。观众提出了非常有趣的话题，如生产部署、事件风暴、多样性、与分支/主干基础开发合作的经验…

[![](img/e3c421fe37be9b90cda8a82a1b207429.png)T2】](https://apiumhub.com/wp-content/uploads/2018/10/openspaces.jpg)

## 结论:Scbcn 2018

这只是 scbcn 2018 期间可以看到的一小部分。这是一个充满高质量会议和研讨会的美好周末。每年会议都比以前更好，这是由于志愿者的辛勤工作。为下一个版本继续努力！

如果您想了解更多关于 Scbcn 的信息，我强烈推荐您点击[此处](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇有 Scbcn 亮点的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

[在 Java 中使用 Vavr 功能更强](https://dev.to/apium_hub/be-more-functional-in-java-with-vavr-5b4i)

帖子 [SCBCN 2018:我的两分钱-第一部分](https://apiumhub.com/tech-blog-barcelona/scbcn/)首先出现在 [Apiumhub](https://apiumhub.com) 上。