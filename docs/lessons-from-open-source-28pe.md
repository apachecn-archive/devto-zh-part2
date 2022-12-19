# 来自开源的教训

> 原文：<https://dev.to/joshghent/lessons-from-open-source-28pe>

<figure>[![](img/95f48bed152100be799618b4f92aa8be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EVoRLANP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AidogazS2GC315Ga9.) 

<figcaption>照片由[钻营资本](https://unsplash.com/photos/uv5_bsypFUM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[未溅](https://unsplash.com/search/photos/software?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

为开源做贡献通常被吹捧为在软件开发社区中获得认可的一个很好的方式，许多人将他们的 [Github](https://github.com/) 档案宣传为各种简历。此外，开源软件开发人员发现他们的编程能力得到了增强，日常工作的动力也得到了补充。然而，除此之外，还可以从对开源的贡献中学到更多的东西。

### 代码所有权

当我第一次接手一个开源项目的维护者时，我发现自己对代码库*应该是什么样子有很好的认识。这是我的孩子，我已经照顾，并试图改善。当我开始鼓励社区加入这个项目时，我开始看到奇怪的新的解决方案，我不会选择这些方案。此外，我对任命任何其他人作为我创建的组织的维护者持谨慎态度。*

我没有专注于交付特性和错误修复，而是专注于我如何看待代码库应该是什么样子。

<figure>[![](img/cc0ec3b9d6f1a46809136466bbaea46d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nnBClEE_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2AKzBNzDDzzdTtEh32.) 

<figcaption>“不对，应该是这个样子！”</figcaption>

</figure>

它突出了一个有趣的点，你不能把方法看得太珍贵。问题可以用许多不同的方法来解决。正如人们所说，剥一只猫的皮的方法不止一种——对于软件开发来说，这再正确不过了。

作为一个开源维护者，你的目标应该是支持和鼓励人们以他们喜欢的方式解决这些问题。代码的细节应该被忽略，相反，重点应该放在它是如何被很好地记录和测试的。

当然，有时某种方法比必要的更复杂。在这些情况下，**讨论**这个人选择这种方式的原因。也许他们尝试了你想的方法，但是不管什么原因都没有成功。不要全力以赴，因为代码变化越多，就越有可能偏向一种解决方案。

### 沟通

开源本质上是对任何拥有 Github 账户的人开放的。因此，偶然发现你的项目并希望为其做出贡献的人可能不是来自同一个时区，也可能不是英语为第二语言的人。这往往会导致沟通失误。因此，最好齐心协力，确保你所说的话没有歧义。此外，不同的国家在他们的语言中可能有某些习俗，虽然可能会冒犯你，但被认为与他人无关。反过来也是如此，所以要注意任何可能不必要冒犯他人的语言。

<figure>[![](img/568f7128cd3dceb65f1b07896d7a4c7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hklMeiRE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/448/0%2A2nwpaiTXHO_QQXR1.) 

<figcaption>情感在网络上并不总能顺利传播</figcaption>

</figure>

当与开源项目的维护者或贡献者交流时，要记住的一件重要事情是，他们是在空闲时间无偿地做这些事情。考虑到这一点，您需要警惕向人们施加时间承诺的压力，或者在合并请求评论中过于挑剔。确保你自始至终都是善良和体贴的。这个原则适用于你的日常工作，当然有时可能会惹怒一些人，但总体来说，积极和鼓励是值得的。

### 写作

除了编写代码，还有一种更重要但很少被想到的写作形式——文档。在开源领域，如果你想让人们使用你的东西，你必须告诉他们如何使用它！

<figure>[![](img/c4285d5321d2a663749c5ee2c98637b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sGgwm5bZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/480/0%2A0Ijnny5zhcXA1nUM.) 

<figcaption>“任意”键在哪里？</figcaption>

</figure>

这提出了一个有趣的挑战，你需要以一种适合你的目标读者的方式来写你的文档。如果您的应用程序更多地面向初学者，那么您的文档也应该面向初学者。不要假设某人已经安装了 [Node](https://github.com/nodejs/node/wiki/Installation) 或 [MongoDB](https://docs.mongodb.com/tutorials/install-mongodb-on-windows/) ，向他们展示如何安装，或者向他们指出他们可以学习的其他指南。磨练文档编写技能的一个好方法是发现一个新的 API 并为其编写文档。深入研究源代码，找出该端点的用法及其输出。因为你是作为一个局外人来参与这个项目的，所以你的文档会很自然地提供给那些读者，并且会为你将来如何编写文档提供一个杰出的基准。

和所有的写作一样，重点是要清晰、简洁和易于理解。

开源软件不仅仅是添加功能和修复错误，它是关于人的。这些经验对于在现实环境中使用是很重要的，并且将被证明是非常宝贵的。如果你正在寻找一个开始贡献的地方，那么[待价而沽](https://up-for-grabs.net/)是很棒的。或者，找一些你已经在使用的软件，如果是开源的，试着在他们的 Github 页面上解决问题。开源为我打开了许多大门，比我做过的任何事情都更能促进我的职业生涯——对你也是如此。

* * *