# 用 Python、AWS 和 art 构建一个 Twitter 机器人

> 原文：<https://dev.to/vboykis/building-a-twitter-art-bot-with-python-aws-and-art--74p>

> ![unknown tweet media content](../Images/5d26144f39f349c5937fd54993b92b91.png)![SovietArtBot profile image](../Images/0399aafc047bfb33b60c5410aa5bba72.png)Soviet artbot@ Soviet artbot![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)【青年博物学家】
> 塞尔吉·格里戈列夫，1948 年11:16AM-2018 年 2 月 15 日[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=964096054089134081)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=964096054089134081)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=964096054089134081)

在我的博客上的原始帖子。

**TLDR:** 我做了一个推特机器人， [@SovietArtBot](https://twitter.com/SovietArtBot) 它每 6 小时用 Python 和 AWS Lambdas 发一次维基亚特社会主义现实主义类别的画。查看机器人的[网站和代码在这里](https://veekaybee.github.io/soviet-art-bot/)。

这篇文章概述了我为什么决定这么做，我做的架构决定，关于机器人如何工作的技术细节，以及我对机器人的下一步计划。

**目录**

*   为什么要建造一个艺术机器人？
    *   [技术目标](#technical-goals)
    *   [个人目标](#personal-goals)
    *   [为什么是社会主义现实主义](#why-socialist-realism)
*   [将一个项目分成几个部分](#breaking-a-project-into-chunks)
*   [需求和设计:高层 Bot 架构](#requirements-and-design-high-level-bot-architecture)
*   [开发:从 WikiArt 中提取绘画作品](#development-pulling-paintings-from-wikiart)
*   [开发:在本地处理绘画和元数据](#development-processing-paintings-and-metadata-locally)
*   [开发:使用 S3 和兰达斯](#development-using-s3-and-lambdas)
    *   [开发:调度λ](#development-scheduling-the-lambda)
*   [部署:Bot Tweets！](#deployment-bot-tweets)
*   接下来去哪里？
    *   [测试和维护](#testing-and-maintenance)
*   [结论](#conclusion)

# 为什么要造美术机器人？

通常，当你开始成为一名数据科学家或开发人员时，人们会给你善意的建议“选择一个项目并去做”，作为学习你需要的技能的一种方式。

这种建议可能很难，也很模糊，特别是当你没有很多经验可以借鉴，甚至无法根据你知道的多少来判断什么是可行的，以及整个过程应该如何工作。

通过详细写出我的过程，我希望它能帮助更多的人理解:

1)一个软件项目从头到尾的步骤。

2)推出一个“足够好”的最小可行项目并迭代现有代码以添加功能的过程。

3)选择一个你喜欢做的项目。

4)社会主义现实主义艺术的快乐。

## 技术目标

最近，作为数据科学工作流的一部分，我做了更多的软件开发，我发现:

1)我非常喜欢做数据科学项目的分析和开发工作。
2)数据科学家熟悉的开发技能越多，他们就越有价值，因为这最终意味着他们可以构建生产工作流的原型，并比等待数据工程师更快地将模型投入生产。

我最近的一个目标是能够进行端到端的完整软件开发项目，专注于理解现代生产最佳实践，尤其是在云中。

[![high-level](../Images/f78cb0164b075decb663ede250293c01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TwOXl7QM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/squadgoals.png)

## 个人目标

但是，一个仅仅关于“云架构交付”的项目真的很无聊。事实上，我刚读完最后一句就睡着了。当我做一个项目时，它必须有一个有趣的、具体的目标。

为此，我对 Twitter 作为一个开发平台非常感兴趣。我最近写道，我们可以修复互联网的最重要的方法之一就是[离开 Twitter。](http://blog.vickiboykis.com/2016/11/20/fix-the-internet/)

说起来容易做起来难，因为 Twitter 仍然是我在互联网上最喜欢的地方之一。这是我获取大部分新闻的地方，我在这里发现新的博客帖子，参与关于数据科学的讨论，在这里我结交了很多在现实生活中认识的朋友。

但是，Twitter 非常嘈杂，最近已经到了有毒的程度。Twitter 有系统的方法来解决这个问题，但我决定尝试自己解决这个问题，开始使用 [#devart](https://twitter.com/search?q=%23devart&src=typd) ，这是一个标签，人们在这里发布经典艺术作品，并附上自己的技术相关说明，以打破紧张的内容。

能够很好地陈述一个技术问题，并将其归因于一个视觉隐喻，然后与其他人分享，这些人也欣赏这个视觉隐喻，并发现它很有趣和相关，这是一种极其宣泄的方式。

液体错误:内部

液体错误:内部

液体错误:内部

液体错误:内部

液体错误:内部

液体错误:内部

液体错误:内部

液体错误:内部

有时你只是想用能打动你的艺术打破单调乏味的文字。原来不止我一个人。

液体错误:内部

随着我发布更多的#devart，我意识到我喜欢看原始艺术，就像喜欢理解标题一样，我喜欢像 [Archillect](https://twitter.com/archillect?lang=en) 、 [Rabih Almeddine 的](https://twitter.com/rabihalameddine?lang=en)和[苏联视觉效果](https://twitter.com/sovietvisuals?lang=en)这样的账户，他们都在推特上发布了许多美丽的视觉内容，至少有一些解释。

我决定要造一个机器人，可以在推特上发布画作。特别是，我对社会主义现实主义艺术作品感兴趣。

## 为什么是社会主义现实主义

社会主义现实主义是在俄国革命后发展起来的一种艺术形式。随着俄罗斯君主制的瓦解，社会界限被打破，人们开始尝试各种新的艺术形式，包括未来主义和抽象主义。我之前在这里写过[关于这种转变。](http://blog.vickiboykis.com/2015/06/reddit-was-amazing/)

随着布尔什维克巩固权力，他们建立了一个机构来控制他们认为在新政权下可以接受的教育和文化价值，政府为可以接受的苏联艺术制定了新的标准。

在审视社会主义现实主义艺术时，很明显，其根本目标是宣传共产主义。但是，仅仅因为这些作品是明目张胆的宣传，并不影响我对这种类型的喜爱，因为它确实代表了真实的人在现实生活中所做的事情。

液体错误:内部

这些人在工作、睡觉、大笑、皱眉、争吵，并表现出我们在艺术中不常看到的真实情感。他们是可联系的、人道的，并将我们的人性反射给我们。我也非常喜欢这种艺术类型的一点是，女性被描绘成在做一些事情，而不是坐着不动来迎接艺术家的目光。

> ![unknown tweet media content](../Images/e123ee72ae7340985d8f5c99ecc0e5d2.png)![Vicki Boykis profile image](../Images/ee3566a457d22ec4b003fde0777a8674.png)Vicki boy kis[@ vboykis](https://dev.to/vboykis)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)“年轻的、理想主义的数据科学家收割他们的第一批模型进行酸洗”
> Tetyana Yablonska，1966 年00:09AM-06 2017 年 10 月[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=916092943575986176)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=916092943575986176)

所以，我决定做一个推特机器人，每隔几个小时发布一个作品。

下面是最终结果:

> ![unknown tweet media content](../Images/5d26144f39f349c5937fd54993b92b91.png)![SovietArtBot profile image](../Images/0399aafc047bfb33b60c5410aa5bba72.png)Soviet artbot@ Soviet artbot![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)【青年博物学家】
> 塞尔吉·格里戈列夫，1948 年11:16AM-2018 年 2 月 15 日[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=964096054089134081)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=964096054089134081)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=964096054089134081)

传统软件开发有几个步骤:

1.  要求
2.  设计
3.  发展
4.  测试
5.  部署
6.  维护

# 把一个项目分成几块

这是很难接受的。当我第一次开始时，我列出了需要做的所有事情:设置 AWS 凭证、角色和权限、版本控制、编写实际代码、学习如何下载带有请求的图像、如何按照计划制作 bot tweet，等等。

当你从上往下看的时候，它是压倒性的。但是在《一只鸟接一只鸟》中，安妮·拉莫特写道，这是我最喜欢的关于写作过程(但实际上是关于任何创作过程)的书之一，

> 30 年前，我的哥哥当时 10 岁，正试图写一份关于鸟类的报告，他有三个月的时间来写，第二天到期。我们在博林纳斯的家庭小屋，他坐在厨房的桌子旁，几乎要哭了，周围都是活页纸、铅笔和未打开的关于鸟类的书，被眼前巨大的任务弄得动弹不得。然后我父亲在他身边坐下，用胳膊搂着我哥哥的肩膀，说:“一只鸟一只鸟地说，伙计。一只鸟接一只鸟。”

这也是我对软件开发的看法。一次做一件事，直到你完成那件事，然后继续下一件事。因此，考虑到这一点，我决定混合使用传统瀑布方法中的上述步骤，并将它们与敏捷概念相结合，即通过对这些步骤进行大量小而快速的循环来更接近最终结果。

# 需求与设计:高层 Bot 架构

我从我的需求开始逆向开发这个应用程序:

Twitter 上的一个机器人，按照时间表从某种数据库中提取绘画图像和元数据，可以是 [cron](https://help.ubuntu.com/community/CronHowto) 或类似的东西。

这帮助我搞清楚了设计。由于我将把发布到 Twitter 作为我的最后一步，将数据放在云中的某个地方是有意义的。我也知道我最终会想要整合 AWS，因为我不想让代码和数据依赖于我的本地机器。

我知道我还需要版本控制和持续集成，以确保在我开发机器人时，它在我的本地机器上是稳定的，在我推出代码时，它在 AWS 上也是稳定的，所以我不必在 AWS 控制台上手动编写代码。

最后，我知道我会使用 Python，因为[我喜欢 Python](http://veekaybee.github.io/2017/09/26/python-packaging/) ，还因为它通过 Twython API(感谢 Timo 通过 Tweepy 给我指出 [Twython](https://twitter.com/tkoola/status/963480840574590978) ，这已被否决)和 AWS 通过 [Boto 库](https://twitter.com/vboykis/status/963224376056434688)很好地连接到 Twitter。我首先会从一个网站上获取这些画和关于这些画的元数据，这个网站上有很多不受版权约束的优秀的社会主义现实主义绘画。然后，我会对这些画做些什么来获取名字、画家和标题，这样我就可以把这些都发布出去了。然后，我会在 AWS 中完成剩下的工作。

所以我的高层流程是这样的:

[![high-level](../Images/9d2fab13f24b4a792698c15acc600923.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TyTKU4Ir--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/high-level-flow.png)

最终，我会完全重构对本地机器的依赖，将所有东西都推给 S3，但是在弄清楚 JSON 返回哪种元数据之前，我不想在 AWS 上花任何钱。

除此之外，我对我需要的工具没有具体的想法，并且随着我的中期目标变得更加清晰，我做出了设计和架构的选择。

# 开发:从维基上拉画

现在，开发工作开始了。

WikiArt 收藏了你能想到的各种风格的令人惊叹的、分类良好的艺术品。它做得如此之好，以至于[一些研究人员也将](http://www.lamsade.dauphine.fr/~bnegrevergne/webpage/documents/2017_rasta.pdf)目录[用于他们关于](http://cs231n.stanford.edu/reports/2017/pdfs/406.pdf)[深度学习](https://arxiv.org/pdf/1605.09612.pdf)的论文。

有些日子，我只是去浏览一些新的东西，迷失在一些艺术中。(如果你喜欢它们，请[捐赠](https://www.wikiart.org/en/donate)给它们。)

WikiArt 还有两个方面对项目很重要:

1)他们对社会主义现实主义艺术有一个明确的分类，并有很多作品。从社会主义现实主义的角度来看，500 幅作品并不算多(如果我想一天发不止一张图片的话)，但已经足够好了。

2)每件作品都有图片、标题、艺术家和年份，这对于正确地在 Twitter 上发布作品非常重要。

我的第一步是看看是否有通过 API 访问网站的方法，这是目前通过编程从网站获取任何类型内容的最常见方法。WikiArt 的问题在于，从技术上来说，它没有现成的公共 API，所以人们求助于真正创造性的方式来抓取网站。

但是，我真的真的不想抓取，特别是因为该网站有无限的滚动 Javascript 元素，在大多数人使用 Python 抓取的工具 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) 中抓取这些元素很烦人。

所以我做了一些调查，发现 [WikiArt 确实有一个 API](https://docs.google.com/document/d/1Vxi5lQnMCA21dvNm_7JVd6nQkDS3whV3YjRjbwWPfQU/edit#!) ，即使它不是官方的，而且在这一点上，有点过时。

它有一些关于 API 速率限制的重要信息，告诉我们你多久可以访问一次 API 而网站不会生气和被踢出去:

> API 调用:每 2.5 秒 10 个请求
> 
> 图像下载:每秒 20 个请求

更重要的是，如何通过基于 JSON 的[查询参数](https://en.wikipedia.org/wiki/Query_string)访问特定的类别。然而，他们拥有的文件大部分是艺术家级别的:

`http://www.wikiart.org/en/salvador-dali/by-style/Neoclassicism&json=2`

所以我不得不做一些尝试和错误来找出我想要的正确链接，它是:

`https://www.wikiart.org/en/paintings-by-style/socialist-realism?json=2&page=1`

就这样，我准备好提取数据了。

我首先使用 Python [请求库](http://docs.python-requests.org/en/master/)连接到站点，并提取两件东西:

1)一个 JSON 文件，其中包含所有的元数据
2)所有的实际绘画作为`png/jpg/jpeg`文件

# 开发:本地处理绘画和元数据

我拿回来的 JSON 是这样的:

```
{  ArtistsHtml:  null,  CanLoadMoreArtists:  false,  Paintings:  [],  Artists:  null,  AllArtistsCount:  0,  PaintingsHtml:  null,  PaintingsHtmlBeta:  null,  AllPaintingsCount:  512,  PageSize:  60,  TimeLog:  null  } 
```

Enter fullscreen mode Exit fullscreen mode

在绘画数组中，每幅画看起来像这样:

```
{  "id":  "577271cfedc2cb3880c2de61",  "title":  "Winter in Kursk",  "year":  "1916",  "width":  634,  "height":  750,  "artistName":  "Aleksandr Deyneka",  "image":  "https://use2-uploads8.wikiart.org/images/aleksandr-deyneka/winter-in-kursk-1916.jpg",  "map":  "0123**67*",  "paintingUrl":  "/en/aleksandr-deyneka/winter-in-kursk-1916",  "artistUrl":  "/en/aleksandr-deyneka",  "albums":  null,  "flags":  2,  "images":  null  } 
```

Enter fullscreen mode Exit fullscreen mode

我还通过从 JSON 返回`response.raw`并使用`shutil.copyfileobj`方法下载了所有的图像文件。

我决定不再在本地进行任何处理，因为我的目标是最终将所有东西都转移到云中，但是我现在已经有了可供我测试的文件，这样我就不再需要点击 WikiArt 和让网站超载了。

然后我用 [boto 客户端](https://github.com/boto/boto3)将 JSON 和图像文件上传到同一个 S3 桶，这样你就可以写:

```
def upload_images_to_s3(directory):

    for f in directory.iterdir():
        if str(f).endswith(('.png', '.jpg', '.jpeg')):
            full_file_path = str(f.parent) + "/" + str(f.name)
            file_name = str(f.name)
            s3_client.upload_file(full_file_path, settings.BASE_BUCKET, file_name)
            print(f,"put") 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，这里的`.iterdir()`方法来自非常棒的`pathlib`库，是 Python 3 的新特性，它比 os 更好地处理文件操作。点击[查看更多信息。](https://github.com/arogozhnikov/python3_with_pleasure)

# 开发:使用 S3 和兰达斯

现在我在 S3 有了我的文件，我需要某种方式让 Twitter 阅读它们。为了以固定的时间间隔做到这一点，我决定使用 AWS Lambda 函数(不要与 Python lambda 函数混淆，这是一种完全不同的函数)。)因为我已经熟悉了 Lambdas 和它们的功能- [参见我之前在 AWS 上的帖子【T1-】,它们是我可以使用的工具，而不需要很多准备时间(架构决策的关键组成部分)。)](http://veekaybee.github.io/2018/01/28/working-with-aws/)

Lambdas 是代码片段，您无需了解运行它们的机器的任何信息就可以运行它们。它们是由 AWS 生态系统中的其他事件触发的。或者，它们可以在一个类似于 cron 的时间表上运行，这非常适合我想要做的事情。这正是我所需要的，因为我需要安排机器人每隔一段时间发布一次。

Lambdas 看起来[像这样](https://docs.aws.amazon.com/lambda/latest/dg/python-programming-model-handler-types.html)用 Python:

```
def handler_name(event, context): 
       return some_value 
```

Enter fullscreen mode Exit fullscreen mode

`event`是您决定做什么来触发函数，上下文[设置与 AWS 交互和运行函数所需的所有运行时信息](https://docs.aws.amazon.com/lambda/latest/dg/python-context-object.html)。

因为我想让我的机器人在推特上发布艺术作品和它周围的一些背景，所以我需要一种方法来发布图片和元数据，通过匹配图片和元数据。

为此，我需要创建[键-值对](https://en.wikipedia.org/wiki/Attribute%E2%80%93value_pair)，一个通用的编程数据模型，其中键是`image`属性的文件名部分，值是`title`、`year`和`artistName`，这样我就可以将这两个匹配起来，如下所示:

[![high-level](../Images/186eef45a44dc34ae16257811bfebe3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MX-3FVC_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/file_match.png)

总而言之，我想让我的 lambda 函数做几件事。我为那个部分写的所有代码都在这里。

1)打开 S3 存储桶对象并检查元数据文件的内容

在 lambda 中打开一个 S3 桶通常是这样的:

```
def handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key'] 
        download_path = '/tmp/{}{}'.format(uuid.uuid4(), key)
        s3_client.download_file(bucket, key, download_path) 
```

Enter fullscreen mode Exit fullscreen mode

其中事件是从 Lambda 传入的 JSON 文件，表示触发事件已经发生。因为我们的触发器是一个定时事件，所以我们的 [JSON 文件](https://github.com/veekaybee/soviet-art-bot/blob/master/soviet_art_bot/event.json)没有关于那个特定事件和桶的任何信息，我们可以排除该事件，以便创建一个通常打开给定桶和键的函数。

```
 try:
        data = s3.get_object(Bucket=bucket_name, Key=metadata)
        json_data = json.loads(data['Body'].read().decode('utf-8'))
    except Exception as e:
        print(e)
        raise e 
```

Enter fullscreen mode Exit fullscreen mode

2)拉出元数据，拉进一个字典，以文件名为键，元数据为值。我们可以把它放到一个`defaultdict`中，因为默认情况下它们是有序的(从 3.6 开始，所有的字典[都将被排序为](https://mail.python.org/pipermail/python-dev/2016-September/146327.html)，但是我们在这里还是谨慎行事。)

```
indexed_json = defaultdict()

    for value in json_data:
        artist = value['artistName']
        title = value['title']
        year = value['year']
        values = [artist, title, year]

        # return only image name at end of URL
        find_index = value['image'].rfind('/')
        img_suffix = value['image'][find_index + 1:]
        img_link = img_suffix

        try:
            indexed_json[img_link].append(values)
        except KeyError:
            indexed_json[img_link] = (values) 
```

Enter fullscreen mode Exit fullscreen mode

(顺便说一下，一个我之前不知道的真正有助于文件名解析的整洁的 Python 字符串实用程序是(rsplit)
[[http://Python-reference . readthedocs . io/en/latest/docs/str/rsplit . html](http://python-reference.readthedocs.io/en/latest/docs/str/rsplit.html))。)

3)随机选择一个文件名来发布推文(`single_image_metadata = random.choice(list(indexed_json.items()))`)

4)在推特上发布图像和相关元数据

Twitter 使用了几个 Python 库。我最初开始使用 Tweepy，但令我难过的是，我发现它不再被维护了。(谢谢提示，[提莫。](https://twitter.com/tkoola/status/963480840574590978))

所以我转而使用了 [Twython](https://twython.readthedocs.io/en/latest/) ，它有点复杂，但是是最新的。

实际上最后发出推文的最后一段代码在这里:

```
twitter = Twython(CONSUMER_KEY, CONSUMER_SECRET, ACCESS_TOKEN, ACCESS_SECRET)

    try:

        tmp_dir = tempfile.gettempdir()
        #clears out lambda dir from previous attempt, in case testing lambdas keeps previous lambda state
        call('rm -rf /tmp/*', shell=True) 
        path = os.path.join(tmp_dir, url)
        print(path)

        s3_resource.Bucket(bucket_name).download_file(url, path)
        print("file moved to /tmp")
        print(os.listdir(tmp_dir))

        with open(path, 'rb') as img:
            print("Path", path)
            twit_resp = twitter.upload_media(media=img)
            twitter.update_status(status="\"%s\"\n%s, %s" % (title, painter, year), media_ids=twit_resp['media_id'])

    except TwythonError as e:
        print(e) 
```

Enter fullscreen mode Exit fullscreen mode

这是利用了 Lambda 的临时空间:

> ![Vicki Boykis profile image](../Images/ee3566a457d22ec4b003fde0777a8674.png)Vicki Boykis[@ vboykis](https://dev.to/vboykis)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)TIL 那 AWS Lambda 函数有微型文件系统，你可以用它作为临时存储([stackoverflow.com/questions/3564…](https://t.co/egCKwu6GJB))。21:03PM-03 2018 年 1 月[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=948661197011914757)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=948661197011914757)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=948661197011914757)

将文件从 S3 拉入 Lambda 的`/tmp/`文件夹，并通过文件名将它与元数据匹配，此时元数据是键值格式。

`twitter.upload_media`方法上传图像并获取媒体 id，然后通过`twit_resp['media_id']`将该 id 传递给`update_status`方法。

仅此而已。图像和文本已发布。

## 开发:调度λ

第二部分是配置功能。按计划运行。有两种情况会触发 Lambdas:

1.  正在发生的事件
2.  一个定时的时间表。

事件[可以是任何事情](https://docs.aws.amazon.com/lambda/latest/dg/invoking-lambda-function.html),从文件放入 S3 桶，到轮询 Kinesis 流。

预定事件可以用 cron 编写，也可以按固定速率编写。我开始编写 [cron rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) ，但是由于我的机器人没有任何具体的要求，只要求它每六个小时发布一次，这个固定的速率对我来说已经足够了:

[![high-level](../Images/102fad5dcf341a73c2027ee818d72b4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D5eqI_Dn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/cron-rule.png)

最后，我需要打包 lambda 进行分发。Lambda 运行在没有预装很多 Python 库的 Linux 机器上(除了 boto3，我之前使用的 Amazon Python 客户端库，它将 Lambda 连接到 AWS 生态系统的其他部分，以及 json。)

在我的脚本中，我有很多库导入。其中，Twython 是一个外部库，需要用 lambda 打包并上传。

`from twython import Twython, TwythonError`

# 部署:Bot 推文！

所以我根据这些说明打包了 Lambda，第一次是手动打包，通过上传一个 zip 文件到 Lambda 控制台。

而且，就是这样！我的两个一次性脚本已经准备好，我的机器人已经启动并运行。

[![high-level](../Images/4194e3329471e2ce57c388087347bd48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3xYvEhoC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/lambda.png)

[![high-level](../Images/b1bf4a92f5cd9ae1aff99de1446cdea6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--soYZPFmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/cronedjob.png)

这是我最后的流程:
[![architecture](../Images/d95a0a691b686a86742915b2f439f369.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VVtJfylZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/veekaybee/veekaybee.github.io/master/images/architecture.png)

# 下一步去哪里？

关于苏联艺术机器人，我还有很多想了解的。

最重要的第一步是调整代码，这样就不会有一幅画每周重复一次以上。这似乎是推特粉丝不生气的合适时间。

同时，我想把重点放在测试和维护上。

## 测试和维护

我第一次完成整个流程时，从一个本地 Python 项目开始，这个项目是我在 PyCharm 中开始的，并在 [GitHub](https://github.com/veekaybee/soviet-art-bot) 上进行版本控制。

> ![unknown tweet media content](../Images/09268a23774866c209b7ddd99ffedc6e.png)![Vicki Boykis profile image](../Images/ee3566a457d22ec4b003fde0777a8674.png)Vicki boy kis[@ vboykis](https://dev.to/vboykis)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我，试图解释我使用 PyCharm，使用 Sublime Text，使用 Jupyter 笔记本进行开发的案例。2018 年 1 月 18 日上午 02:26[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=953815826393595904)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=953815826393595904)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=953815826393595904)

因此，当我对流程的任何部分进行更改时，我的执行流程将是:

1.  在本地运行 Wikiart 下载功能
2.  用`python-lambda-local`测试[λ“本地”](https://medium.com/@bezdelev/how-to-test-a-python-aws-lambda-function-locally-with-pycharm-run-configurations-6de8efc4b206)
3.  压缩 lambda 并上传到 Lambda
4.  在 Lambda 代码中出错
5.  拉上拉锁，再跑一次。

这对我来说真的不是一个理想的工作流程，因为我不想每次都必须手动重新上传 lambda，所以我决定使用 Travis CI，它与 GitHub 集成得非常好。。问题是涉及到很多设置:virtualenvs、同步到 AWS 凭证、设置允许 travis 访问 lambda 的 IAM 角色和配置文件、设置测试 Twitter 和 AWS 环境以测试 Travis 集成，等等。

目前，机器人正在生产中工作，当它工作时，我将继续在我的[开发分支](https://github.com/veekaybee/soviet-art-bot/tree/dev)中自动化部署的越来越多的部分。([这篇文章](https://joarleymoraes.com/hassle-free-python-lambda-deployment/)对压缩 lambda 特别有帮助，我的[部署脚本在这里。](https://github.com/veekaybee/soviet-art-bot/blob/dev/lambda_deploy.sh)

这两个完成后，我想:

1)重构 lambda 代码以利用`pathlib`而不是 OS，这样我的代码就标准化了(应该是一个很小的变化)

2)来源多画。WikiArt 很棒，但是只有 500 多幅社会主义现实主义类别的作品。我想找到更多高质量元数据和大量艺术品收藏的来源。那么，我想

3)创建一个前端，任何人都可以上传社会主义现实主义作品，供机器人发布推文。这可能比定制一个刮刀更容易，并且允许我众包数据。作为这个过程的一部分，我需要一种方法在内容到达我的最终 S3 桶之前对其进行筛选。

这导致:

4)浏览当前收藏，确保所有艺术品都是相关的和 SWF 的。看看是否有办法通过编程来实现。

并且:

5)机器学习和深度学习潜在的可能性:寻找一个分类器，过滤掉含有裸露/有问题内容的艺术品，并弄清楚如何决定“有问题”是什么意思。可能与 AWS 合作，或者建立我自己的 CNN。

其他机器学习机会:

*   用#devart 进行 Mash，看看这个机器人是否可以根据绘画内容为绘画创建有趣的标题

*   按流派从艺术品中提取颜色，看看它们在不同流派和年代之间有什么不同

# 结论

软件开发可能是一个漫长、令人疲惫的过程，涉及到许多移动的部分和决策制定，但是如果您将一个项目分解成字节大小的块，您可以继续工作，以防止自己被手头的整个任务淹没，这将变得更加容易和有趣。当然，另一部分是，它必须对你来说是有趣的和有趣的，这样你才能完成所有的疯狂，最终得到一个有趣的成品。