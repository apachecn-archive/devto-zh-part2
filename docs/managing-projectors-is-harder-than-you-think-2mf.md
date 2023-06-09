# 管理投影仪比你想象的要难

> 原文：<https://dev.to/barryosull/managing-projectors-is-harder-than-you-think-2mf>

我们已经在过去的中讨论了投影仪的[骨骼，这次让我们更深入地看看如何管理它们。](https://dev.to/blog/projection-building-blocks-what-you-ll-need-to-build-projections)

简而言之，投影仪是一种接受一系列事件并对其进行处理的东西，将它们投射成所需的任何形状或操作。然而，像任何事情一样，还有更多，更多。这就是这篇文章的内容，我试图讨论你在日常使用投影仪时会遇到的复杂情况和问题。

# 运行模式

先说简单的，先说投影仪的不同模式，以及它们的行为方式。根据我的经验，可以归结为三种模式。

1.  从头开始运行(标准投影仪)
2.  从现在开始运行(Laravel 称之为“反应堆”)
3.  运行一次(特殊情况)

### 1。从头开始运行

这个很简单，从第一个事件开始，然后从那里向前玩。这些投影仪将播放所有历史事件，然后继续处理任何新发生的事件。默认情况下，大多数投影仪将在此模式下运行。

### 2。从现在开始运行

有些放映机不需要历史事件，只关心上映后发生的事件。例如，假设你想发布一个新的电子邮件服务。它的工作是在有`UserRegistered`事件时向新用户发送欢迎电子邮件，但你只希望新用户发生这种情况，现有用户不应该收到任何东西。

这种投影仪模式只处理投影仪发布后创建的`UserRegistered`事件，所以只有新用户会收到邮件。简单明了。这种投影仪模式比“从头开始运行”更罕见，但它仍然是一个必备的。

### 3。运行一次

最稀有的投影仪类型。我只创造了其中的四个，但它们对适应一个生命系统是必不可少的。这些投影仪从第一个事件开始向前播放，但它们只运行一次。一旦他们跑了，就再也不会跑了。

你为什么需要这个？我们用它们来解决困难的迁移问题。假设您需要更新一个域模型，以便它有新的数据。现在，很容易将这种更改添加到代码中，以便新创建的对象拥有数据，但是历史对象呢？这就是运行一次模式发挥作用的地方。您编写一个投影仪，从历史事件中填充丢失的数据，在部署时运行它，添加丢失的数据，然后在发布时它停止运行，再也不会运行。现在，您的所有数据都是最新的。可以把它们看作是通过事件升级现有数据结构的迁移，通常是为发布做准备。

我们已经了解了不同的模式，让我们来讨论投影仪的生命周期，看看模式是如何发挥作用的。

# 投影仪生命周期

投影仪的生命周期分为三个阶段，从开始到结束。

1.  靴子
2.  玩
3.  退休

我们将从第二点开始，玩，因为这是大多数人习惯于思考投影仪的方式。

### 2。玩

最基本的任务，投影仪必须能够播放事件。这是放映机的面包和黄油；当事件被触发时，投影仪将处理该事件。这通常被称为“播放”投影仪。通常你会有一个进程来管理它并记录每个投影仪在事件流中的位置。

这对于活动投影仪来说非常快，但对于新投影仪来说可能有点问题，新投影仪必须播放整个事件流才能跟上，这就是为什么我们有一个单独的启动过程。

### 1。靴子

引导阶段的目标是为发布准备投影仪。当一个“从头运行”投影仪被引入到一个应用程序中时，它需要播放日志中的所有事件。这个过程需要时间，如果你已经将代码发布，任何读取数据的东西都会变成旧的，可能是不可靠的数据。例如，报告中的数字不正确。为了解决这个问题，你应该有一个独特的`boot`流程，在发布前准备好投影仪。

引导应该只发生在部署过程中，最好是在发布代码之前的最后一步。引导仅影响新投影仪，即没有位置记录的投影仪。一旦所有投影仪都被`booted`，您就可以安全地部署新代码，让投影仪的标准`play`系统接管。对系统的用户来说，这是天衣无缝的。

### 3。退休

当然，过一段时间后，你将不再需要一些投影仪，你会想把它们从你的系统中移除。

淘汰投影仪非常简单，包括三个步骤:

1.  删除投影仪代码
2.  删除投影仪控制的投影中存储的所有数据
3.  删除跟踪投影仪位置的记录

顺便说一句，对于第 2 步和第 3 步，我建议使用迁移，这是显而易见的，并且您有完整的变更历史记录。

#### 何时退休

您可能认为应该在不需要投影仪时一次性将其全部淘汰，但这并不是一个好主意。想象一下，您必须将系统回滚到使用退役投影仪的部署。在回滚完成之前，需要重建投影仪，这需要时间，如果回滚很关键，您就没有时间了。这就是为什么我通常把第二步和第三步放几天，只从回购中删除代码开始。

另一个提示，你应该完全退休副作用免费投影机。不应删除触发副作用(例如发送电子邮件)的投影仪的位置记录(步骤 3)，否则您将冒意外重放它们的风险，再次触发副作用。更安全的做法是删除代码和投影数据(步骤 1 和 2)，但保留实际位置记录。

# 投影仪故障

我有一些坏消息，在某个时候你的投影仪会出故障。我知道，它不应该发生，但错误是不可避免的，为它们做好准备是明智的。我见过的一些例子，无效的事件模式、缺失的数据库列、数据库中缺失的数据(关于事件顺序的错误假设)、中断的外部服务等等...你明白了。

投影仪在启动或播放过程中可能会出现故障，您如何处理故障取决于哪种情况。

### 开机失败

当投影仪在启动过程中出现故障时，应立即停止启动过程，并将投影仪标记为`broken`。如果有其他投影仪同时启动，它们应该被标记为`stalled`(稍后会有更多相关内容)。那么你的团队应该注意到这个问题，可能是通过一个 bug 追踪器。不言而喻，“部署”过程也应该失败。

现在是修复损坏的投影仪并触发新部署的时候了。当 boot 第二次运行时，它应该尝试从最后的位置向前播放`broken`和`stalled`投影仪。如果 bug 被修复，那么它们都将成功引导，您可以部署代码了。完成了。如果没有，那么重复这个过程，直到它全部修复。

### 播放失败

Boot 将捕获大多数错误，但有时当投影仪作为实时系统的一部分运行时，您会遇到错误。如果投影仪在播放过程中出现故障，应记录故障，将投影仪标记为`broken`，提醒团队，然后停止播放该投影仪。所有其他投影仪应该保持正常运行，因为一个失败的投影仪没有理由让整个应用程序瘫痪。

投影仪修好后，触发部署，这将导致 boot 尝试向前播放`broken`投影仪。如果它是固定的，那么它将成功地播放事件，你将能够做一个释放。

# 结论

总而言之，投影仪比人们想象的更复杂。如果您想构建一个稳定的、可维护的投影仪系统，请记住以下几点。

1.  运行模式
2.  生命周期
3.  失败

如果你对这三个都有把握，那么你就准备好了。您将能够轻松部署和管理投影仪，在出现问题时解决问题，最大限度地减少您或您的客户的停机时间。

我已经建立了一个放映系统来解决上述问题，但它还没有准备好成为众人瞩目的焦点，它更像是一个将上述内容浓缩成一个单一系统的实验。尽管随意看看吧！[https://github.com/barryosull/the-projectionist](https://github.com/barryosull/the-projectionist)

就这样，快乐投射！