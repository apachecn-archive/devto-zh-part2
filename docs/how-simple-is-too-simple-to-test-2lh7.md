# 多简单才算简单到无法测试？

> 原文：<https://dev.to/bosepchuk/how-simple-is-too-simple-to-test-2lh7>

我相信你和我一样花了很多时间思考如何写出更好的软件。如果您已经尝试过单元测试最佳实践，您无疑会在某个时候问这个问题:有多简单是简单到无法测试的？

嗯，今天我在我的一个项目中发现了一个错误，表明**可能没有下限**。看看这个:

[![suspicious code listing](img/66380552f41e560f7956685eecd0d212.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uthte4Ls--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qvj80szd9htlbzlkkpnn.png)

这个方法对$pcIDs 的内容进行一些检查，如果出错，就会抛出一个异常。简单吧？当然了。它只有 15 行代码，包括签名和括号。

你会相信这段代码有错误吗？让我给你一个提示:

[![first hint about suspicious code](img/bc49a4f92a1898ab8dcf16cd456fa115.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RqQ_s80T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dgsvx4gji12u35cgqq6c.png)

你看到问题了吗(慢慢来，我等着)。

等待...

等待...

等待...

等待...

等待...

想明白了吗？需要更明显的暗示吗？

[![second hint about suspicious code](img/2eb37a66ed643e6bc53c2e86c249e22b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EdlrxXeE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ek8i9wifc8p16usckdpc.png)

### 发生了什么事

看到那里发生了什么吗？我们希望$pcID[0]和$pcID[1]是输入数组中仅有的值。它们应该是大于 0 且不相等的整数。乍一看，这种方法似乎工作正常。但是经过更仔细的检查，您可能意识到 ValidateID::run()返回一个 bool，但是这段代码不检查返回值。相反，它假设如果值无效，ValidateID::run()将抛出异常。

所以，**validateProductCategoryIDs(array(' cat '，' dog '))；不会抛出异常**，这不是我们想要的行为。

### 单元测试最佳实践

那么，有多简单才算简单到无法测试呢？我开始接受这样一个观点，几乎没有什么事情是简单到无法测试的。

有一句话我很喜欢，但我不记得是谁写的/说的了:

> 为你关心的任何行为编写测试。

确实如此。这与一个关于医院里的电脑病毒的讲座非常吻合(顺便说一下，这个讲座很棒)。讲师们认为医生和护士总是向计算机系统输入不正确的数据。其中一些错误会杀死病人。但对我们来说重要的是- ***他们不知道他们犯了一个错误*** 。一名护士用数字键盘向输液泵输入药物剂量时犯了一个[错误，他们大约有 4%的时间没有注意到](http://rsos.royalsocietypublishing.org/content/4/4/160903)！这看起来不是很高的错误率吗？

所以，如果护士在没有注意到的情况下搞砸了简单的数字输入任务，那么程序员在没有注意到的情况下在他们的代码中插入愚蠢错误的频率有多高呢？我想答案大概是很多。

我在这篇文章中展示的代码是精心编写的。作者为它写了一个全面的手工测试计划，但是没有单元测试。作者使用我们的[代码审查清单](https://smallbusinessprogramming.com/code-review-checklist-prevents-stupid-mistakes/)审查了代码。然后，我还使用我们的代码审查清单审查了代码，并执行了手动测试计划。而且，在所有的 QA 之后，我们仍然错过了这个非常明显的错误。

### 那么我们今天是如何捕捉到这个错误的呢？

我的同事为一个调用 validateProductCategoryIDs()方法的方法编写了单元/集成测试。他实际上没有注意到这个问题，但当我对他的新测试进行代码审查时，我注意到数组(“猫”、“狗”)类型的输入没有导致异常，我认为这很可疑。

总而言之， ***我们需要结合代码审查的自动化测试来捕捉这个错误*** 。尽管这是一个简单的错误，但是无论是手工测试、单元测试还是代码审查都不足以引起我们对这个错误的注意。我们只是通过结合几种质量保证活动才发现了这个错误。

顺便说一下，这正是 Steve McConnell 在 [Code Complete](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670/) 中推荐的——多种类型的 QA，因为每种类型捕捉不同类型的错误。

### 关于干净编码实践的一些想法

我们永远也不会知道最初编写这段代码的程序员脑子里到底发生了什么，但是我可以自信地告诉你，这不是一个有意的错误。

如果你做一点根本原因分析，你会注意到我给你看的代码中有两个验证方法。一个返回 void 或抛出异常，另一个返回 bool。这太让人困惑了。

#### 不要对返回不兼容类型的方法使用容易混淆的相似命名系统

拥有返回不兼容类型的 validateXXX 方法可能是一种代码味道。我愿意和你打赌，这有助于我们在代码审查期间没有发现错误。

我们也许可以通过将 validateXXX 方法的一些名称改为“verifyXXX”来减少将来出现同类错误的可能性。这将明确验证方法返回 bool，验证方法在遇到不喜欢的数据时抛出异常。我们的 IDE 具有强大的重构支持，因此我们可以快速安全地进行这种更改。

#### 用静态分析检查捕捉这种潜在的错误

我的首选解决方案是使用集成到我们的 IDE 中的静态分析检查，以帮助我们在编码时找到这类问题的所有实例。我不确定在我们的项目中开启这种检查有多容易，但是我确信你可以看到它的吸引力。

### 外卖

即使是最好的程序员也会犯错。通过仔细的代码审查和良好的干净代码实践，您可能会发现其中的一些问题。但是如果你想要真正低的缺陷率，你需要结合多种 QA 方法。您的单元测试最佳实践应该包括测试您关心的任何行为，甚至是那些看起来太简单而不能包含错误的代码。

*同意还是不同意？我很想在评论中听到你的想法。*

喜欢这篇文章吗？请在下面“点赞”。