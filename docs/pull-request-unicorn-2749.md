# 拉动请求独角兽

> 原文：<https://dev.to/jimsy/pull-request-unicorn-2749>

这是根据我在以前工作场所的一次演讲改编的。这不是禁止，这只是在一个特定的时间为一个团队工作。最重要的是和你的团队沟通，设定期望值。

## 目标

我们都做代码审查，但我们中的一些人并不真正知道这个过程是为了什么，以及我们试图从中获得什么。这个仪式是为了什么？

### 正确的事情

团队的首要责任是确保他们交付了他们实际同意交付的东西。

这是进行代码评审最常被遗忘的原因。开发人员习惯于将编写一些代码和打开 PR 作为单个原子过程。他们甚至没有真正停下来想一想。扪心自问；这东西真的做了我们同意做的事吗？如果不是为什么？

### 打倒这种东西

我们希望降低投入生产的代码的缺陷率。我们知道我们不能消除所有的错误(至少没有真正英勇的努力是不可能的),但是我们可以通过让几个人思考正在编写和发布的代码来最小化它们。

### 搞出这种事

我们希望确保我们合并到 master 的代码遵循团队当时理解的良好设计原则。好的设计意味着什么？

*   松耦合
*   低圈复杂度
*   用业务语言进行交流
*   你的团队认为好的东西

### 知识转移

我们希望为所有相关人员提供一个学习机会。这就是为什么我们要求每个人都参与进来，而不仅仅是“老年人”充当网关。不要害怕评审别人的代码，我所得到的一些最好的代码评审意见来自于经验不足的团队成员，他们要求我解释我的选择。

## 校长

无论我们是执行代码审查还是请求审查，我们都应该遵守一些原则:

### 假定诚信

每个人都在用他们所拥有的技能和知识尽最大努力。如果有人做了错事，那不是因为他们愚蠢或恶意。

### 协作

一个人可能编写了代码，但是团队负责交付它。这意味着我们必须一起工作，并且从编写第一行代码(或者希望删除)开始就拥有这个过程。)一直到交付生产。

### 善良

你和你的团队讨论过非暴力沟通吗？如果没有，预订一个职业发展研讨会是个好主意。

给出反馈时，尽量使用[“我”陈述](https://en.wikipedia.org/wiki/I-message)而不是“你”陈述。

当你准备好给出反馈时，确保它是*可操作的*、*特定的*和*种类的*(提问)。这意味着:

*   可操作的 -接收者能够理解你想要他们做什么。
*   *具体* -反馈应该足够具体，让接收者能够提出实现结果的具体步骤。
*   这应该是显而易见的，但是很多人在给出反馈时忘记了这一点。

### 参与

所有团队成员都应该定期参加代码评审，不管他们的经验水平如何。不理解某件事没关系——这通常是某件事可以改进的标志。

### 不足为奇

无聊的代码就是好代码。你用聪明的代码智胜的第一个人是你未来的自己。忍住冲动。

## 请求审查

记住这些原则和目标，让我们确保你是最好的拉动式需求者。

让我们从了解如何知道我们准备好打开一个拉取请求开始。在你打开那个 PR 之前需要发生什么？

### 验收标准

作为作者，您认为该特性符合或超过了与产品负责人商定的验收标准，无论是在故事/任务定义中还是在随后的开发过程中与他们的协商中。

### 环保

很多绿点。

不仅仅是满足验收标准，还需要验收测试来证明你已经满足了标准。此外，对于新代码或组件，应该有足够的单元测试。

### 唱白脸

在你的代码或任何相关的工件中不应该有任何林挺或格式错误。在你的 PR 上写评论要求你修改 HTML `div`的缩进或者在你的方法调用上加括号，这不是一个好的利用任何人的时间。

#### 调试完毕

您的代码中没有调试器、todos、fixmes 或其他随机垃圾注释。事实上根本没有注释掉的代码。我们有版本控制——如果需要的话，我们可以把它拿回来。

#### 绿林

代码中没有任何明显的拼写错误。

TLDR；在你点击提交之前阅读差异。

### 主语 vs 宾语

先说写你的 PR，从题目开始。每当我查看我的 bitbucket 仪表板时，我都会看到按主题排列的 pr 列表。主题行最好足以让我知道这个特性是什么，以及它是否需要我的关注。

#### 好

```
[FRT-675] Enable customers to pay their bills via stripe.
[Card 27] Locked out users should be able to unlock their account.
[BUG] Fix 500 error when logging in. 
```

Enter fullscreen mode Exit fullscreen mode

#### 不好

```
Feature/widgets-controller
oops
make the PO happy 
```

Enter fullscreen mode Exit fullscreen mode

### 健康的身体

很明显，你吸引人的主题吸引了我的眼球，我很想继续读下去。我需要充分了解该功能，以便能够判断您请求的内容:

#### 抵制容易的选择

我知道当您创建一个新的 pull 请求时，bitbucket 会“有益地”给您一个分支提交消息的列表。我真的不想看到他们。请看在上帝的份上。

#### 讲故事

我需要知道你想要实现的故事。你不需要粘贴确切的故事，但我需要了解你同意为产品负责人构建的故事是什么。
我还需要知道故事的一些推理，这样我才能提出更好的建议。

#### 谈判

我需要知道在您构建功能时，任何可能改变了功能范围的对话。

*   你有没有和产品负责人谈过，有没有添加或者删除东西？
*   你和你的团队讨论过并决定做一些不同的事情吗？

#### 斯科特，帮我联系一下

如果我可以直接阅读这个故事，那么请确保在 PR 中有一个到原始故事的链接，这样我就可以自己看到我是否有其他未回答的问题。如果没有，添加讨论摘要可能是个好主意。

#### 一图抵千言

如果你已经做了或改变了一些用户界面，那么在截图之前和之后添加一些总是一个好主意，这样我就不必检查你的分支，安装依赖项，启动应用程序来看看它是什么样子。

#### 一个视频抵得上一千张图片

如果你添加了 UI 行为或交互，那么添加一个简短的截屏视频或动画 GIF 总是一个好主意。

我使用 [LICEcap](http://www-dev.cockos.com/licecap/) 是因为它直接将记录筛选为 GIF 文件，几乎没有其他内容。

### 合并

您应该何时合并您的变更？简单:当你有两个滴答时。之后就看你的了。可能会有关于何时合并的项目限制，或者您可能希望添加自己的限制。例如，如果没有反馈，我通常不会合并我批准的变更，因为我担心经验不足的开发人员可能只是在我的工作上盖章。

## 执行审核

让我们把这个翻过来，谈谈审查别人的代码。有几件事你需要知道:

### 有针对性的反馈

试着把你的注释放在尽可能靠近你所注释的代码的地方——在你想要修改的那一行是最好的。

### 他们为什么要在乎？

尽最大努力解释你为什么建议这种改变。是可读性，设计糟糕还是完全错误？

你建议的方法有哪些利弊？

举一个你认为可以改进的例子。

### 拥抱不是打手

记得问类似“你考虑过”或“我觉得”这样的问题，而不是“那是错的”或“你很笨”。

### 说起哑巴...

问愚蠢的问题。尤其是如果你不是很有经验的话。如果你不理解，那么很可能作者也不理解。

### 畅通无阻的大象

有时你可能想提出一个建议或指出一些事情，而不强迫作者把它作为他们改变的一部分。也许你在回顾他们的代码时想到了一些有趣的事情，或者有一个可教的时刻。我们的团队决定使用🐘表情符号象征着这一点。

### 干得好老伙计

记得给好评。当有人做得很好或者做了让你开心的事情时，放下✨，💖,🦄或者其他一些神奇的表情符号。这会让你感觉更好，这是一种合法的友好方式。

### 庆祝

开一个赞同派对。走过去和他们击掌，或者在他们的午餐盒里装满亮片。在你的团队文化中行得通的。

## 感谢

感谢阅读！让我们再次让代码审查变得伟大！