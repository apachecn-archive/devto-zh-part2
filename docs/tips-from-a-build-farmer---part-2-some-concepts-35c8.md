# 来自 Build Farmer 的提示-第 2 部分:一些概念

> 原文：<https://dev.to/dmerejkowsky/tips-from-a-build-farmer---part-2-some-concepts-35c8>

*最初发表于[我的博客](https://dmerej.info/blog/post/tips-from-a-build-farmer-part-2-some-concepts/)。*

*注意:这是“建造农民系列[技巧的第二部分。](https://dmerej.info/blog/post/introducing-tips-from-a-build-farmer/)*

在本文中，我们将定义一些我们将在本系列的其余部分使用的概念。

但是首先，让我们看一些 Java 代码！ <sup id="fnref1">[1](#fn1)</sup>

# 水果和苹果

```
class Fruit {
  // ...
}

class Apple extends Fruit {
    private final String type;

    public Apple(String type) {
        this.type = type;
    }
}

class Foo {

    void func() {
        List<Fruit> fruitList = new ArrayList<Fruit>();
        Apple golden = new Apple("golden");
        fruitList.add(golden);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

很明显`Apple`类并不是真正的苹果。它是一堆数据和方法，通过一个`type`字符串来识别一个苹果。我们可以说苹果类是真实苹果对象的一个*隐喻*。

但是注意，`fruitList`是*不是*水果清单！它是一片内存，包含某种类型的对象，如果你忘记了这一点，你将会遇到令人讨厌的编译失败，比如这个:

```
Apple firstFruit = fruitList.get(0);

foo.java:21: error: incompatible types: Fruit cannot be converted to Apple 
```

Enter fullscreen mode Exit fullscreen mode

你当然可以用泛型来解决这个问题，但这不是我的观点。

我的观点是，作为程序员，我们整天都被隐喻包围着，有时并没有意识到这一点。毕竟，我们所做的只是编写文本，最终转换成 0 和 1 的序列，并在一块硅片上运行。

我们创建抽象和封装，以便对我们编写的代码进行推理，我们经常用隐喻来这样做。

我们也非常关心命名。例如，你可以争论 Apple 类的`type`字段应该被命名为`variety`,因为这个词对于任何开发人员来说已经有几个意思了。

但是隐喻不仅仅被开发人员使用。

# 你为什么要在乎

如果你读过敏捷或 XP，你会发现有一种叫做*系统隐喻*的*编程实践*。

它伴随着一堆其他的学科，比如测试驱动开发、可持续和平和集体代码所有权。你可以在维基百科上找到[整个列表。](https://en.wikipedia.org/wiki/Extreme_programming_practices)

在程序员中，我们有时不会过多考虑隐喻，因为我们仍然可以通过查看*实际代码实现*(或注释)来理解名称的含义。

当我们需要与团队之外的人交流时，比如产品负责人、营销或销售，事情就会发生变化。当我们与他们谈论我们系统的特征或组件时，我们使用隐喻。

因此，想出好的比喻是商业和技术团队之间有效沟通的唯一方法。我们必须想出一个大家都同意的通用词汇。

这意味着，除其他外:

*   避免用同一个词描述不同的事物
*   确保每个单词对每个人来说都意味着同样的事情。

无论如何，这就是为什么我想谈谈隐喻。我将在下面列出的概念同样可以用于团队内外的人之间的交流。

# 概念

这些概念以自上而下的方式列出。所以如果遇到不懂的术语，就继续读下去！

## 持续整合

*持续集成*，或简称 CI，是指在*构建场*上运行*脚本*的任何情况。

## 词体

*脚本*通常是用一种“脚本语言”编写的(因此得名)。

他们可以访问部分或全部源代码，可以做很多事情:编译、运行一些测试、生产交付品、部署新服务器等等 <sup id="fnref2">[2](#fn2)</sup> 。

他们可以:

*   在某些事件发生时自动运行(如创建合并请求)
*   在特定时间自动运行(例如，每天上午 9 点)
*   由手动操作触发(如运行单独的程序或使用 Web 界面)。

## 建造农场

一个*建造农场*由两个东西组成。一组*跑者*，和一名*协调者*。

可能只有一个运行者，协调者可能和运行者运行在同一台机器上，这没有关系；重要的是跑步者和协调者做不同的事情。

## 跑步者

运行程序是运行脚本的任何机器。仅此而已。

## 协调员

协调员:

*   监听上面列出的*触发器*，
*   在运行者之间分派脚本在一个或几个机器上的执行，
*   汇总这些执行的结果。这些可以是测试报告、日志、文件等。…

请注意，如果您使用的是 Jenkins，协调器称为*主节点*，而运行器称为*节点*或*从节点*。

## 工作

一个*作业*是一个脚本加上一个*配置*。该配置至少包含触发器的规范，但也可能包含键和值的映射。

## 打造

一个*构建*是一个作业的执行。

通常，构建有一个*号*，而作业有一个*名*。

页（page 的缩写）s:我知道“build”在编程界有很多其他含义，但我没有更好的含义。

## 失败与成功

构建有**只有一种方式**到*成功*，多种方式*失败*。(脚本无法运行，编译失败，除了一个测试之外，所有测试都通过了，等等。).

推而广之，当最近一次构建失败时，我们说一个作业正在*失败*。如果最后一次构建成功，我们说作业是*稳定的*。因此，作业可以*从稳定变为失败*，或者*回到稳定*。

根据协调器的不同，作业可以有其他状态，如“已跳过”、“正在启动”或“已取消”。

旁注:Jenkins 为构建分配颜色，例如当编译正常但一些测试失败时，就分配“黄色”。对我来说，黄色版本只是另一种失败的版本。<sup id="fnref3">T33</sup>

## 通知

当一个任务到达某个状态时，你可以使用一个*通知*来将事件传达给一些人。

例如，如果由拉请求触发的构建失败，协调者可以向其作者发送电子邮件。

## 步

我们看到脚本可以完成许多不同的任务。剧本通常会经历一系列的步骤*，就像我们在《T2》中的例子一样，剧本很吓人:*

 **   获取代码
*   编译所有内容
*   运行测试
*   生成归档文件

通常，其中一个步骤的失败会导致脚本立即终止(但并不总是如此)。

此外，一系列步骤可能因工作而异。例如，您可能希望“部署到生产”步骤只针对某些触发器运行:P

## 神器

一个*工件*是由一个或几个步骤产生的文件。工件可以跨作业交换，在某些情况下通过协调器*，但是也可以使用其他机制。*

## 交货

一个*交付*是一个可以被你的客户使用的工件。交付通常部署到下载页面。

## 部署

*部署*是一种特殊的工作，包括:

*   将一些文件上传到远程位置
*   更新一些在别处运行的代码

在第二种情况下，您可以:

*   将源文件直接上传到服务器。例如，你可以为用 PHP 编写的网站这样做。
*   运行一些可翻译的剧本
*   构建 docker 映像并上传到容器注册中心，然后要求集群中的一些节点使用新的 Docker 映像
*   …或任何其他技术

因为部署只是工作，所以他们*也*只有一种成功的方式，有许多种失败的方式…

## 环境

一个环境就是一组进行某种部署的机器。

例如，您可以决定:

*   主分支上的推送触发的每个部署都将进入一个称为“生产前”的环境
*   由以“v”开头的标记触发的每个部署都将进入一个称为“生产”的环境。

顺便说一下，GitLab CI 对环境有一个[极好的支持。](https://docs.gitlab.com/ee/ci/environments.html)

# 结论

这就是我将要使用的词汇。希望你会觉得有用。

下次我们将回答第一个问题:“我应该使用什么编程语言来编写 CI 脚本？”。

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  如果您愿意，可以直接跳到概念部分，但是我想稍微谈谈开发实践。 [↩](#fnref1)

2.  请注意，我在 CI 中包含了连续交付(CD)。您的里程可能会有所不同。 [↩](#fnref2)

3.  另外，我是色盲 [↩](#fnref3)*