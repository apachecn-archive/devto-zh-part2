# 别人的代码和故意的谬误

> 原文：<https://dev.to/dangolant/other-peoples-code-and-the-intentional-fallacy-5djd>

很多时候，当我阅读其他人写的代码，甚至是我自己很久以前写的代码时，我陷入了一种窒息，一种呆若木鸡的感觉，删除或更改现有的代码感觉是错误的。我正在重构的代码已经被测试过了(希望如此)，已经被评审过了(希望如此)，并且已经在生产中无错地运行了(希望如此)足够长的时间，以至于没有人会记得为什么有些事情会以这样的方式工作。尽管一切都在源代码的控制之下(希望如此),但是锁定并不想移除一个解决方案是很自然的。毕竟有效！如果它一直在生产中工作，并且是以某种方式编写的，那么一定有一个*原因*它是以那种方式编写的，即使这个原因对读者来说没有任何意义。

对吗？(错误)

<figure>

[![Me, trying to figure out my own BS](../Images/3e1dfc26083173cd5df37fa038cf7711.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UvI9CIkd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/Cxj3KTY05FHQQ.gif)

<figcaption>Me, trying to figure out my own old code</figcaption>

</figure>

我花了比我愿意承认的更长的时间来理解这一点，但是有很多代码是有效的，已经被测试过，已经被审查过，但是它们是按照它的方式编写的，*没有特别的原因*。同样，你或我可能会编写下面的简洁代码:

```
function(x){
    return somethingElse(someOperation(x));
    // this is obviously a contrived example
} 
```

Enter fullscreen mode Exit fullscreen mode

而不是这个更具描述性的块:

```
function(x){

    var y = someOperation(x);

    var z = somethingElse(y);

    return z;

} 
```

Enter fullscreen mode Exit fullscreen mode

其他人可能会拆分变量声明，或者不必要地将事情分解成函数，或者*没有*在应该的时候将事情分解成函数，因为他们只是想完成工作。我经常为优先考虑期限和目标而不是简洁和清晰而感到内疚，在某种程度上*我应该*。然而，在一天结束时，稳定的、经过测试的、可工作的代码比“优雅的”代码、干净的代码，或者已经被审查和修改得死去活来，但是从未被发布的代码要好。此外，尽管我们尝试和优化、测试、审查，并且通常对所有事情都全力以赴，但是软件开发的某些部分只是简单地归结为偏好。这不是家具，我们可能不*想要*回来修理，但是如果有必要，我们可以。

<figure>

[![Some stuff really doesn't matter](../Images/e3097a6032f1ba6e8af18864d369e97f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5z3H1gvc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/52f51a96e4b0ec7646cd474a/5328b57de4b067106916ef7f/56b3bc3b01dbae7de44ff861/1504623390271/starbelly2.jpg%3Fformat%3D500w)

<figcaption>Some stuff really doesn't matter</figcaption>

</figure>

当我们不可避免地*回来修复旧代码时会发生什么？如前所述，我个人很难删除或重写旧代码。不是因为我太懒，或者因为我不理解它，而是因为我认为它之所以被写成这样是因为*有理由*，有*有计划*，由更精通问题的人*写成*。根据我的经验，这种感觉往往是错误的。*

我自然倾向于想给每样东西起个名字，这让我开始寻找一个好的描述来描述这种模糊的感觉，这种对现有代码的巨大价值和明确意图的归属。最近我发现了它，这要归功于我听的一个很棒的播客，叫做*无弹夹*。

我们一直在谈论的感觉与一个叫做*有意谬误*的概念非常吻合，这个概念来自文学理论。由 W.K. Wimsatt 和 Munroe Beardsley 提出的故意谬误是这样一种观点，即“作者的设计或意图既不能也不适合作为判断文学艺术作品成功的标准”。在文学批评的背景下，他们的观点是作者的意图不能从文本中重建，作品不言自明，不能被逆向工程以获得作者可能试图表达的任何隐藏点，或者书面作品被省略。此外，他们认为，即使是关于一件作品的笔记，或作者在该作品写作时的同期日志，仍然只是获得作者意图的次要来源，而作为最终产品的作品总是主要来源。文本可能是一种不完美的媒介，但当我们想要判断一件作品的意图、意义、价值和影响时，它是我们唯一拥有的媒介。当谈到代码时，这些想法可能不完全相同，但我们可以看到一个押韵方案开始形成，对吗？

<figure>

[![Me, trying to make a point](../Images/b7ca86bf053ffaf9531e6edbf0c4035e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lDLlWksK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.com/images/bbe6cd1dd6a8cd80f6776238b2319925/tenor.gif%3Fitemid%3D8620816)

<figcaption>What I look like trying to make this point</figcaption>

</figure>

当评估一段代码时，通常不可能从代码本身推导出实现细节的意图。也就是说，我们当然可以尝试推导出作者的意图，因为我们看到了数据的开始和结束状态、被调用的方法等等，但是*为什么*某些事情被做*某种方式*经常会丢失。

注释——如果有的话——可以阐明一些“ *why* ”的问题，但是注释通常是在代码近视症中写的，这种近视症是在你长时间埋头做某件事情以至于忘记了总体目标之后出现的。他们还可以围绕一个有根本缺陷的方法来记录和编纂意图，或者在存在一个更简单的解决方案时相信一个过度紧张的解决方案。毕竟，我们并不总是在完美的条件下头脑清醒地编写代码。注释也带来了错误传达作者意图的风险，或者在代码更新时没有更新，导致更糟糕的问题。

<figure>

[![Time to figure out what this 2 year old comment means](../Images/4c44fd66d34e9e4b04e4ee05baf4922f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--opJB0PuW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://confessionsfromageekmind.files.wordpress.com/2015/02/the-imitation-game.jpg%3Fw%3D730%26h%3D406)

<figcaption>Time to figure out what this 2 year old comment means</figcaption>

</figure>

文档可能会有所帮助，但记录实现细节并不是一个好主意，因为这会导致脆弱的文档，需要像代码一样频繁地更新。文档最好用于描述一段代码所遵循的契约，而不是关于为什么某些事情要以某种方式完成的问题。答案可能是这样的“那是在凌晨 2:00，一个奇怪的 bug 突然出现，如果我用其他方法，我需要完成它，我们再也没有碰过它”。这些答案可能不是最佳实践或我们想要解决的问题，但我认为任何从事生产代码库工作的人都见过类似的情况。

如何避免这类问题的问题最好留给更有经验的开发人员去解决，关于这个主题有很多文章。我唯一的建议是将编写清晰、干净、集中的代码放在首位。我发现 [lambda 服务和面向服务的架构](https://dev.to/dangolant/creating-serverless-functions-with-python-and-aws-lambda-dli)很有帮助，但是没有一种方法是万能的。

我更广泛的观点是，代码一旦写出来，就只能被它的功能和读者对它的理解所质疑。即使你能接触到原作者，也不能以任何可靠的方式通过作者意图的镜头来质疑它。相信你自己，如果代码在一些努力后没有意义，那不是对你智力的控诉。这意味着有机会理解和改进你的代码库的一部分。如果它是故意以某种方式编写的，那么你自己总是会遇到这种限制。在那一点上，你可能有机会*克服那个限制*，并且永久地改进你团队的工作产品。你对你的代码库拥有和原作者一样多的所有权，如果你被明确要求重构或更新某些特定的功能，你可能有*更多的权力去改变事情*。当您这样做时，我建议您尽可能地深入了解以前的实现。如果你害怕继续使用看起来不透明的功能，那就很难解决越来越大的问题。请记住，尽量让代码比刚进来时更清晰，因为有一天有人可能会问你为什么要以某种方式实现某个东西，你可能会意识到*真的很难理解作者的意图😄。*

> 如果你喜欢这篇文章，请随时在 dev.to 上推荐它，分享它，并给我一个关注！
> 
> 此外，如果你喜欢这个话题，并希望看到类似的东西，或者只是想保持联系，请在 Twitter 上关注我！还有，在推特上大声喊出来给我的朋友 [@thejaredwilcurt](https://dev.to/thejaredwilcurt) 和 [@milkstarz](https://dev.to/milkstarz) 做笔记。