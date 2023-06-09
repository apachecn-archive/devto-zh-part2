# 使用 MongoDB 图表减少 ETL 的需求

> 原文：<https://dev.to/kenwalger/reducing-the-need-for-etl-with-mongodb-charts-3d59>

我们所知道的数据库已经存在了 40 多年。当它们首次出现时，企业通常将数据保存在不同的系统和不同的格式中。这些决定有各种各样的原因。这些独立的数据存储的副作用之一是需要组合在一起才能执行数据分析。这导致了 ETL(提取、转换、加载)的长期实践。

ETL 是一个从一个起始数据源提取数据的过程，*以某种方式转换数据，然后将数据加载到另一个数据存储中。听起来很简单，但事实上，在幕后有很多工作要做，有很多步骤和决策要做。这些额外的步骤降低了我们从数据中获得有意义见解的速度。此外，他们依赖于许多关于将数据转换为假定的正确格式以供以后使用的假设——而不太了解这些数据未来会出现的业务问题。*

#### 从数据仓库到云

传统上，企业应用程序依赖于执行 ETL 操作来将数据移动到企业数据仓库(EDW)中。

<figcaption>![Typical ETL Architecture](img/318c65bf9e1e80e19a5b45821d52d600.png)</figcaption>

创建一个成功的数据仓库可能是一个漫长、复杂和昂贵的过程。有助于这一过程的技术之一是 Apache Hadoop。Hadoop 允许使用开源技术在商用硬件上处理大量数据。然而，ETL 和数据仓库环境非但没有简化，反而变得更加复杂和繁琐，工具的激增以及成熟度和采用问题只会增加成本。此外，根据 [Gartner 分析师 Nick Heudecker](https://twitter.com/nheudecker/status/928720268662530048) 的说法，85%的大数据项目会失败。主要是由于过程本身的复杂性。

随着许多组织向云的过渡，从有意义和及时的数据分析的角度来看，ETL 变得更加复杂。将数据从一个源移动到另一个源需要时间。现在有隐藏的数据传输、计算成本和延迟需要应对。虽然可以对陈旧数据执行一些有意义的分析，但大多数现代分析需要尽可能接近实时。

#### ETL 的问题

我们在设置 ETL 过程时面临的一些问题是:

1.  **延迟&停机**–将数据从 A 点移动到 b 点存在固有成本。四十年前，当 ETL 开始时，我们处理的是数兆字节的数据，不需要“即时”访问。如今，我们正在处理大量数据，需要从这些数据中获得实时洞察力。通过网络传输数据不是免费的。在 100 BaseT 网络上，传输 1gb 的数据需要 100 秒。一兆字节需要 10，000 秒或超过两个半小时。所有这些都假设它位于一个不被其他应用程序使用的专用网络上。随着 ETL 需求的增长，数据很容易过时几个小时。我们过去可以在午夜的“停机时间”安排这些传输。然而，在当今的全球化世界中，用户总是在需要即时访问和洞察的地方在线。停机时间不再是可接受的，延迟已经成为新的停机时间。难道世界一端的供应商就应该因为表现不佳而遭受损失，而世界另一端的高管早上就能看到最新的仪表盘吗？
2.  **存储便宜，人工昂贵**—数据仓库开始于存储昂贵的时期。1981 年，1gb 的数据存储成本约为 29 万美元。如今，这一成本不到 0.10 美元。因此，在存储时尽可能多地转换和压缩数据以节省成本非常重要。随着存储成本的降低，劳动力成本却在向相反的方向发展。让一个好的数据库管理员来设计、管理和维护您的数据仓库和 ETL 路径是昂贵的。存储原始数据通常被视为更经济可行的选择。
3.  **ETL 很难**–ETL 需要计划。很多。不仅仅是针对您当前的数据负载，还针对未来负载可能发生的情况。此外，ETL 脚本可能会变得冗长而复杂。从各种来源引入数据，对它们进行循环，添加日志记录、错误处理和配置只是开始。确定数据需要如何转换可能是复杂而脆弱的。如果现在以字符串形式存储的数据在将来被更改了，会发生什么？流程中断，需要进行调整。您是否曾经想过为什么 DBA 口中的第一个回答是一个强调的“不！”当被问及某些事情是否可以改变时？一个“简单”的更改可能意味着更改几十行或几百行代码。出于这些原因以及更多原因，ETL 需要对当前和未来的数据需求、负载和形状进行规划。
4.  开发人员是构建 ETL 管道的合适人选吗？——开发人员擅长很多事情，然而，了解数据存储和 ETL 管道通常不是其中之一。ETL 的设计和实现通常最好由数据工程师来完成。虽然开发人员可能能够通过 ETL 管道将数据放入数据仓库，但一般来说，这通常不是以最有效的方式完成的。专业的数据工程师应该负责这些任务。如果您的团队中没有他们，这是 ETL 的另一个成本。
5.  **维护难题**–随着数据、应用程序和分析需求的规模和复杂性的增长，ETL 维护也在增长。保持数据速度、格式、连接和功能的变化需要时间。许多这些挑战可能在项目开始时没有想到，但会导致长期的维护需求。

#### 使用 MongoDB 图表来避免 ETL 的头痛

如今，公司仍然在各种系统中保存数据。在某些情况下，ETL 是能够对数据进行可视化和分析的唯一选择。或者，您可能已经探索了 ETL，但是还没有采取必要的步骤来准备好数据以供分析，因为它太难了。

如果你已经利用 MongoDB 作为你的数据库，随着 [MongoDB 图表](https://www.mongodb.com/products/charts)的引入，对 ETL 过程的需求已经显著减少，现在处于测试阶段。MongoDB 图表本身理解 MongoDB 文档模型，允许在您的数据上快速创建数据可视化。

使用 MongoDB 图表，您可以连接到您的 MongoDB 服务器，为您的报告分配用户授权策略，并轻松地生成可视化仪表板。有十多种不同的图表可供选择，只需点击几下鼠标，即可获得令人惊叹的可视化效果。

MongoDB 图表允许在不执行 ETL 操作的情况下可视化数据，节省了宝贵的时间和资源。不需要写任何代码，也不需要依赖第三方工具。此外，您仍然可以利用文档模型的丰富性。

#### 结论

对于那些想要快速访问 MongoDB 数据的情况， [MongoDB 图表](https://www.kenwalger.com/blog/nosql/visualizing-data-mongodb-charts/)是一个很好的选择。如果您处于需要分析多个数据源的情况下，我们为商业智能提供了 MongoDB [连接器](https://www.mongodb.com/download-center/bi-connector)。如果你正在使用 [Apache Spark](https://spark.apache.org/) 进行高级分析，我们有一个选项，也可以使用用于 Apache Spark 的 MongoDB [连接器。](https://www.mongodb.com/products/spark-connector)

对于组织中的许多角色来说，MongoDB 图表是分析数据的绝佳工具。没有必要经历 ETL 过程的痛苦。无论 MongoDB 数据存储在哪里，这都是对其进行可视化的最快方式。在本地或由 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 托管的云中。今天就试试吧！

* * *

*这篇文章最初发表在 [MongoDB 博客](https://dev.to/mongodb/reducing-the-need-for-etl-with-mongodb-charts-1f86-temp-slug-2439141)上。*

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F&t=Reducing%20the%20Need%20for%20ETL%20with%20MongoDB%20Charts&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2018%2F11%2FETL_Visual-300x231.png&p%5Btitle%5D=Reducing%20the%20Need%20for%20ETL%20with%20MongoDB%20Charts)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F&title=Reducing%20the%20Need%20for%20ETL%20with%20MongoDB%20Charts)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F&title=Reducing%20the%20Need%20for%20ETL%20with%20MongoDB%20Charts)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Reducing%20the%20Need%20for%20ETL%20with%20MongoDB%20Charts&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Freducing-need-etl-mongodb-charts%2F)

用 MongoDB 图表减少 ETL 需求的帖子最先出现在肯·w·阿尔杰的博客上。