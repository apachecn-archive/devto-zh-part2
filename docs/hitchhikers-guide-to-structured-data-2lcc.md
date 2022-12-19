# 结构化数据的搭便车指南

> 原文：<https://dev.to/asaaki/hitchhikers-guide-to-structured-data-2lcc>

一次小小的旅程，探索语义网的构建模块，以及它们如何让我们的生活变得更加轻松。

> 有一种理论认为，如果有人确切地发现了宇宙的用途以及它为什么会在这里，那么它将会立刻消失，取而代之的是更加奇异和令人费解的东西。还有一种理论认为这已经发生了。”
> 
> —银河系漫游指南

[![Example of Structured Data for HTML; Person and Place: 'Douglas Adams was born in Cambridge'](../Images/b66090a0753ea381b601943f9bed7d02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Bh1DK-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/code-snippet.png)

当我开始使用电脑时，我从未想过它们是否能帮助我做事，以及如何帮助我做事——以及这是否会反过来改善我的生活。我自然而然地认为，我们发明了工具、机器、设备、电话、电脑等等，因为我们想让我们的生活变得更好，并通过让复杂和特别复杂的任务变得更容易和更有用来减少对繁琐任务的思考。

在 90 年代末，我不仅了解到而且体验到了计算机是相互连接的。有一种叫做互联网的东西，大部分是以万维网的形式做广告的:大量或多或少相互链接的文件。大多贴满了华而不实的动画，说这个地方正在建设中。这个标签让我们觉得和突发新闻或股市行情一样重要。

[![Pammie Jay's Webpage (geocities)](../Images/c96aeb013b2c58d0b0d37d2f455a1d41.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vsL7BNdr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/pammiejay.png)

啊，是的，美好的旧互联网。开始时相当静态，主要是文本，但它变得更丰富，更通用，动态，并包括图像，音频和视频。除了静态网站之外，我们还开发了网络应用程序、应用编程接口，让互联网成为一个人与人、与机器互动的地方。

### 如今，我们的冰箱“马文”可以通过推特告诉我们，他感到非常空虚和悲伤

作为一个孩子，我读了大量的书，当然大部分是小说，但我记得我经常去我们村里的图书馆，借一些书给我解释这个世界。但是我消耗了太多，我不能把所有的信息都记在脑子里，现在我的知识并不比几十年前多多少。我记得点点滴滴，但更重要的是，我通常记得我需要在哪里寻找实际数据来填补空白。

[![Google in 1997](../Images/40e0db3b46ec1853665f4a81ab14a520.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dTbHyylA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/google-1997.png)

我从一开始就伴随着谷歌成长。虽然旧的手动维护的目录是探索数字世界的良好起点，但搜索引擎让我的生活变得更加轻松。现在，我甚至不再需要在脑子里保存信息片段的索引和图表，我可以转移到提出正确问题的技能上。

但是大多数通用搜索引擎都有一些问题。建立索引最简单的方法是在互联网上爬行，抓取发现的每个页面的全文，并将其与关键词联系起来。后来，当有人搜索这个关键字时，他们会得到一个巨大的结果列表。算法变得越聪明，你能更好地表达你的查询，列表就越相关和精简。

但是如果你想一想，你已经可以问非常具体的问题，大多数搜索引擎从一开始就不能真正“理解”它。

### “作者道格拉斯·亚当斯写过哪些书？”

如果你在 10 年前甚至 5 年前尝试过这种方法，并且运气好的话，那么第一页上的一些结果会把你指向一篇关于他的出版物的文章。如果你不使用谷歌或 WolframAlpha，情况仍然很令人失望。

今天，我对这种工作方式印象深刻:

[![Google.com search results for 'What are the books the author Douglas Adams has written?'](../Images/82baa5b9cf08459c6dcb0cee1a4baabe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mXsdfX5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/douglas-adams-books.jpg)

你也可以看到，第一个有机的结果是关于维基百科上的作者，只有第二个接近我问的。但是有书封面的顶端酒吧正在摇动它！(我第一次用 Google.de 搜索，结果只有第三个结果。)

我在这里要做一个大胆的声明:给你这样具体的和上下文相关的信息仅仅基于一些全文搜索索引是不可能的；我相信这是由结构化数据驱动的(并且可能存储在图形数据库中…也许)。

我的内部 QuickCheck 程序将范围缩小到了**“Douglas ADAMS books”**，但这不是重点(尽管解析搜索框的输入本身可能是一次不错的冒险)。

我们可以进一步分解我们的查询，发现它由一个元组组成:**“Douglas ADAMS”**和**“books”**。第一个描述了一个人的名字，第二个描述了一个实体类型，显然我们想找到这个人和他写的书之间的联系。由于人们很少提到他们读过哪些书，查询解析器已经可以在这里做出有根据的猜测，并把一个**“已经写了”**(也可以“已经出版”)作为关系类型。

或者用一种非常简单的伪查询语言来表达:

```
SELECT * FROM books WHERE author = "Douglas Adams" 
```

当然，首先您需要从数据库中解析一个实际的作者/个人实体:

```
SELECT id FROM people WHERE name = "Douglas Adams" 
```

然后，您意识到您的人员表/数据库可能有多个条目，您会尝试根据职业或爱好进行过滤。也许你已经将它们按受欢迎程度进行了排名，并会选择排名最高的条目。另一个选择是解决每个人的完整查询，并检查谁写了最多的书(如果有的话)并使用这本书。

无论采用哪种方法，您都需要以结构化的方式存储数据。如果你想建立一个搜索引擎，一个知识数据库，或者任何一种查询和显示这些信息的应用程序(但不是存储所有的信息)，你需要依靠其他资源以机器可消费的方式提供它们。

[![Wordle cloud for 'Semantic Web'](../Images/2411a0b87529aede52ed0b2ba5294205.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XSushyLf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/semantic-web-wordle.png)

结构化数据和语义网的概念并不新鲜。如果你使用过任何博客软件，那么你也使用了结构化的一些方面:类别和标签。

根据你的需要，你甚至引入了你自己的分类法来描述和区分你的文章。举个例子:你是一个电影评论家，根据情绪、目标观众和类型对你的评论进行分类；所有这些维度帮助你描述你的数据，使它更容易被发现，支持消费系统，并直接或间接地给你的读者更多的上下文和元数据，通常以一种更快和更容易的方式，所以他们可以决定他们是否想更深入地进入你的博客或看别的地方。

脸书和 Twitter 上的文章预览/卡片也由结构化数据驱动。当你查看一些 HTML 页面的源代码时，你可以发现一些有趣的元标签，它们的名称或属性类似于`"og:title"`、`"twitter:creator"`或`"article:author"`。它们用于填充预览的各个部分。老实说，当一篇博文有漂亮的封面图片和描述时，你很可能会点击它，不是吗？

[![Twitter Cards](../Images/4813a602fc8392529b1684bfb5c364ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QbilcTYY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/twitter-cards.png)

我们花了几年时间引入更多的数据，或者利用结构化数据的全部潜力，但是现在这个概念是网络的基础。即使人工智能在崛起，如果机器能够以结构化的方式处理数据，它们也能做得最好。

我认为艺术是以一种令人愉快的、人类可读的方式显示内容，并不引人注目地用机器可消费的语义信息丰富它。这样，我们就可以构建能够理解所有数据的界面和应用程序，然后以一种不同的、更容易理解的方式将其可视化。

如果你想看看结构化数据是什么样的，那就去维基数据吧:

[![Wikidata.org page for Q42: Douglas Adams](../Images/5d3f9b9fa1bd7ccae4db1634a6dde9ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C38NnHF1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/wikidata-q42-douglas-adams.png)

这些实体页面非常正式，并且不是阅读一个人生活的最佳页面，但它们通过描述这个实体是关于什么以及它与其他实体有什么样的关系，包含了非常有价值的信息。

此类数据旨在供其他服务使用。例如，维基百科文章右侧的信息框大部分都补充了维基数据信息。

有些人已经开发了工具来很好地可视化这些数据:

[![wikidata graph builder: Q42, P800](../Images/5fcfe63416589ef62d24ee08a08ab0ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8OQPzU5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/04/hitchhikers-guide-to-structured-data/wikidata-graph-q42-p800.png)

### 您如何在自己的项目中使用来自维基数据的结构化数据？

你已经为你的国际读书俱乐部建立了一个网站。它带有一个知识库，已经支持 42 种语言。虽然使用 Contentful 管理作者和书籍的内容很容易，但您意识到本地化有时很痛苦，因为您很难弄清楚每个人的名字的翻译是什么。

#### 不要慌！

您的一个朋友想出了为 Contentful 创建一个 UI 扩展的主意，您可以在其中查找 wikidata 并自动填充名称和其他常见属性的翻译字段。她甚至为你开发了第一个原型:

[![Contentful UI extension example for wikidata.org integration](../Images/075a1432123835ac48d545df9aadb099.png)](https://github.com/asaaki/cf-wikidata-ui-extension-example)<small></small>

你真的喜欢它，并开始考虑如何自动化许多其他步骤。例如，该扩展可以获取所有已知的书籍，为每本书创建草稿，并将它们链接回作者条目。由于 wikidata 还提供了其他 Wikimedia 服务的链接，您也可以将它们与知识库文章一起存储。你整合了很多到其他网站的链接，可能想创建某种类似 Twitter 卡片的预览框。

有了维护良好的结构化数据，可能性是无限的。所以，走出去，探索，并建立真棒的东西。

> 奇怪的是，当这碗矮牵牛花落下时，他唯一想到的是，哦，不，不要再来了。许多人推测，如果我们确切地知道为什么牵牛花碗会认为我们会比现在更多地了解宇宙的本质。”
> 
> —银河系漫游指南

***再见，感谢所有的鱼！**T3】🐟*