# Friedrich Lindenberg 为调查记者提供的实体提取、文档处理和知识图表

> 原文：<https://dev.to/blarghmatey/entity-extraction-document-processing-and-knowledge-graphs-for-investigative-journalists-with-friedrich-lindenberg-5dec>

### 总结

调查记者有一项挑战性的任务，即从混合的信息源中识别复杂的人、地点和事件网络。将这些不同的文档、电子记录和研究转化为可搜索和可操作的事实集合是一项有趣而困难的技术挑战。弗里德里希·林登伯格创建了 Aleph 项目来解决这个问题，在这一集里，他解释了它是如何工作的，为什么他要建造它，以及它是如何被使用的。他还讨论了他对该项目的未来的希望以及该系统可能被使用的其他方式。

### 前言

*   大家好，欢迎来到播客。__init__，关于 Python 和让它变得伟大的人们的播客。
*   当你准备好发布你的下一个应用程序，或者想尝试一个你在节目中听到的项目时，你需要一个地方来部署它，所以看看 Linode 吧。借助 200 Gbit/s 的专用网络、可扩展的共享块存储、节点平衡器和 40 Gbit/s 的公共网络，所有这些都由一个全新的 API 控制，您拥有了扩展所需的一切。今天就去[podcastinit.com/linode](https://www.podcastinit.com/linode?utm_source=rss&utm_medium=rss)获得 20 美元的信用，并在一分钟内启动一台新服务器。
*   访问[网站](https://www.podcastinit.com?utm_source=rss&utm_medium=rss)订阅展会，订阅时事通讯，阅读展会笔记。如果你有任何问题、意见或建议，我很乐意倾听。你可以在推特上通过 [@Podcast__init__](https://twtiter.com/podcastinit?utm_source=rss&utm_medium=rss) 或电子邮件[hosts@podcastinit.com](//mailto:hosts@podcastinit.com)联系我
*   为了帮助其他人找到该节目，请在 iTunes 或 T2 的 Google Play 音乐上留下评论，告诉你的朋友和同事，并在社交媒体上分享。
*   在位于[podcastinit.com/chat](https://www.podcastinit.com/chat?utm_source=rss&utm_medium=rss)的新 Zulip 聊天工作区加入社区
*   社区最大的年度聚会 [PyCon US](https://us.pycon.org/2019/registration/?utm_source=rss&utm_medium=rss) 现在开始报名。别忘了拿你的票，我们在那里见！
*   和往常一样，你的主持人是托拜厄斯·小萌，今天我将采访弗里德里希·林登伯格，讨论 Aleph，一种跨文档和结构化数据执行实体提取的工具

### 面试

*   介绍
*   你是怎么被介绍到 Python 的？
*   你能从解释什么是 Aleph 和这个项目是如何开始的开始吗？
*   什么是调查性新闻？
    *   Aleph 如何融入他们的工作流程？
    *   有哪些其他工具可以和 Aleph 一起使用？
    *   在调查性新闻之外，Aleph 还有哪些用处？
*   Aleph 是如何构建的？自从你开始研究它以来，它是如何发展的？
*   Aleph 的主要组成部分是什么？
    *   Aleph 支持哪些类型的文档和数据格式？
*   能否描述一下实体抽取涉及的步骤？
    *   识别和解析存储在 Aleph 中的文档中的实体的最大挑战是什么？
*   你能描述一下从文档上传到作为搜索查询的一部分显示出来的整个系统的数据流吗？
*   部署和管理 Aleph 安装涉及哪些内容？
*   构建 Aleph 最有趣或意想不到的方面是什么？
*   你知道 Aleph 有什么特别值得注意的用途吗？
*   你对 Aleph 的未来有什么计划？

### 保持联系

*   [网站](http://pudo.org/?utm_source=rss&utm_medium=rss)
*   推特上的 [@pudo](https://twitter.com/pudo?utm_source=rss&utm_medium=rss)
*   [得以](https://github.com/pudo?utm_source=rss&utm_medium=rss)在 GitHub 上

### 镐

*   托拜厄斯(男子名)
    *   [机械汤](https://mechanicalsoup.readthedocs.io/en/stable/?utm_source=rss&utm_medium=rss)
*   弗里德里希
    *   电话号码——因为它很有用
    *   py ICU——超级书呆子但很神奇
    *   [sqlalchemy](https://www.sqlalchemy.org/?utm_source=rss&utm_medium=rss)——我最喜欢的 python 包

### 链接

*   阿莱夫
*   [有组织犯罪和腐败举报项目](https://github.com/pudo?utm_source=rss&utm_medium=rss)
*   [OCR(光学字符识别)](https://en.wikipedia.org/wiki/Optical_character_recognition?utm_source=rss&utm_medium=rss)
*   豪尔赫·路易斯·博尔赫斯
*   [布宜诺斯艾利斯](https://en.wikipedia.org/wiki/Buenos_Aires?utm_source=rss&utm_medium=rss)
*   [调查性新闻报道](https://en.wikipedia.org/wiki/Investigative_journalism?utm_source=rss&utm_medium=rss)
*   [阿塞拜疆](https://en.wikipedia.org/wiki/Azerbaijan?utm_source=rss&utm_medium=rss)
*   [信号](https://signal.org/?utm_source=rss&utm_medium=rss)
*   [开放公司](https://opencorporates.com/?utm_source=rss&utm_medium=rss)
*   [打开精炼](http://openrefine.org/?utm_source=rss&utm_medium=rss)
*   [洗钱](https://en.wikipedia.org/wiki/Money_laundering?utm_source=rss&utm_medium=rss)
*   [电子发现](https://en.wikipedia.org/wiki/Electronic_discovery?utm_source=rss&utm_medium=rss)
*   [CSV](https://en.wikipedia.org/wiki/Comma-separated_values?utm_source=rss&utm_medium=rss)
*   [SQL](https://en.wikipedia.org/wiki/SQL?utm_source=rss&utm_medium=rss)
*   [实体提取(命名实体识别)](https://en.wikipedia.org/wiki/Named-entity_recognition?utm_source=rss&utm_medium=rss)
*   [Apache Tika](https://tika.apache.org/?utm_source=rss&utm_medium=rss)
*   [多语言](https://polyglot.readthedocs.io/en/latest/?utm_source=rss&utm_medium=rss)
*   [空间](https://spacy.io/?utm_source=rss&utm_medium=rss)
    *   [播客。__init__ 集](https://www.podcastinit.com/episode-87-spacy-with-matthew-honnibal/?utm_source=rss&utm_medium=rss)
*   图书馆
*   [宇宙魔方](https://github.com/tesseract-ocr/tesseract?utm_source=rss&utm_medium=rss)
*   [跟随金钱](https://alephdata.github.io/followthemoney/?utm_source=rss&utm_medium=rss)
*   [弹性搜索](https://www.elastic.co/products/elasticsearch?utm_source=rss&utm_medium=rss)
*   [知识图表](https://en.wikipedia.org/wiki/Knowledge_Graph?utm_source=rss&utm_medium=rss)
*   [Neo4J](https://neo4j.com/?utm_source=rss&utm_medium=rss)
*   [Gephi](https://gephi.org/?utm_source=rss&utm_medium=rss)
*   爱德华·斯诺登
*   [文档云](https://www.documentcloud.org/?utm_source=rss&utm_medium=rss)
*   [项目概述](https://www.overviewdocs.com/?utm_source=rss&utm_medium=rss)
*   [Veracrypt](https://www.veracrypt.fr/en/Home.html?utm_source=rss&utm_medium=rss)
*   [量子操作系统](https://www.qubes-os.org/?utm_source=rss&utm_medium=rss)
*   [I2 分析师笔记本](https://www.ibm.com/us-en/marketplace/analysts-notebook?utm_source=rss&utm_medium=rss)

前奏和尾声音乐来自《一条鱼的安魂曲》[怪胎范丹戈乐队](http://freemusicarchive.org/music/The_Freak_Fandango_Orchestra/?utm_source=rss&utm_medium=rss) / [CC BY-SA](http://creativecommons.org/licenses/by-sa/3.0/?utm_source=rss&utm_medium=rss)

[![](img/678add3047582999acc68bbc8c3a47bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--icQ4X72O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://piwik.boundlessnotions.com/piwik.php%3Fidsite%3D1%26rec%3D1%26url%3Dhttps%253A%252F%252Fwww.podcastinit.com%252Faleph-with-friedrich-lindenberg-episode-186%252F%26action_name%3DEntity%2BExtraction%252C%2BDocument%2BProcessing%252C%2BAnd%2BKnowledge%2BGraphs%2BFor%2BInvestigative%2BJournalists%2Bwith%2BFriedrich%2BLindenberg%2B-%2BEpisode%2B186%26urlref%3Dhttps%253A%252F%252Fwww.podcastinit.com%252Ffeed%252F%26utm_source%3Drss%26utm_medium%3Drss)