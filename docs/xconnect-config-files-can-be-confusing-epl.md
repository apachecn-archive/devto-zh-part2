# xConnect 配置文件可能会令人困惑

> 原文：<https://dev.to/jermdavis/xconnect-config-files-can-be-confusing-epl>

目前，我正在进行我的第一个 Sitecore 9 项目，在做一些配置工作时，我被一个恼人的混乱所困扰。如果你正在调整 xConnect 的工作方式，请注意，希望你能避免犯和我一样的错误…

xConnect 引入了一个概念，即分析模型中的一些字段在默认情况下是“私有的”,并且开箱即用，Solr 不会对它们进行索引。因此，如果您希望能够通过姓名或电子邮件搜索联系人，您需要更改配置，以便将这些字段添加到索引中。(当然，如果用户希望被遗忘，这意味着你现在有一个额外的 GDPR 相关的头痛，但这是另一天的主题)

这是 xConnect 预料到的场景，并且有[一个有用的文档页面来解释如何做](https://doc.sitecore.net/developers/xp/xconnect/xconnect-search-indexer/enable-pii-indexing.html)。您所要做的就是打开一个配置文件，编辑一行，然后重建您的索引。听起来很简单，对吧？

但是这里有一个陷阱，[这是我首先想到的…](https://media.giphy.com/media/ZVqIU1UUkk57q/giphy.gif) 文档提到了两个需要修改的配置文件的路径:

[![](img/944c9176b888ceacfacfde421f31a4e6.png)T2】](https://jermdavis.files.wordpress.com/2018/07/indexingconfig.png)

文件的措辞很重要，当我最初读的时候，我忽略了它的意义。这里所说的“`c:\path\to\indexer\App_data\...`”实际上并不意味着您的安装等同于“`c:\inetpub\wwwroot\yoursite.xConnect\App_data\...`”下的“config”文件夹。那个文件夹是存在的，当我看到它的时候，我的思维进入了自动驾驶状态，因为有问题的配置文件也在那里:

[![](img/e32b1f14ede1532f9d2deeeade822650.png)T2】](https://jermdavis.files.wordpress.com/2018/07/wronglocation.png)

我花了一些时间编辑这个文件，并且越来越困惑为什么我的修改没有效果，直到永远有用的[玛蒂娜·韦兰德](https://twitter.com/mhwelander)戴上她的[精神调试帽子](https://vignette.wikia.nocookie.net/btimg/7/7c/Doc-mindreader.jpg/revision/latest?cb=20080916000813)并指出它实际上是这个文件描述的第二个位置，这是重要的一个。

虽然确切的路径在您的安装中可能会有所不同，但您需要更改 xConnect 网站文件夹中“ **`App_Data\jobs\continuous`** ”文件夹下的路径。

一旦你这样做了，一切都会好的…