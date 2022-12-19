# 使用 MongoDB 图表可视化数据

> 原文：<https://dev.to/kenwalger/visualizing-your-data-with-mongodb-charts-3eo2>

将数据存储在数据库中对于今天的企业来说几乎是理所当然的。客户信息、订单历史、产品定价、物联网传感器数据以及更多信息都被记录下来以备将来使用。然而，仅仅存储数据不足以形成有竞争力的市场优势。我们也必须能够分析数据。这样做有许多选择，方式也多种多样。如果您有需要在 MongoDB 中进行可视化分析的数据，MongoDB [Charts](https://www.mongodb.com/products/charts) 是一个很好的选择。

在 MongoDB 图表出现之前，实际上有三种方法来可视化 MongoDB 数据。

1.  将 [MongoDB 商业智能(BI)连接器](https://www.mongodb.com/download-center/bi-connector)与第三方 BI 工具结合使用，
2.  执行提取-转换-加载(ETL)操作并利用第三方工具，或者
3.  编写自定义代码，使用 D3.js 或 Bokeh 等图表库。

#### MongoDB 图表的好处

MongoDB 图表，目前处于测试阶段，提供了一种简单的方法来可视化您在 MongoDB 中的数据。您不需要将数据转移到不同的存储库，编写自己的代码，或者购买第三方工具。MongoDB Charts 知道并理解文档数据模型的丰富性，并允许轻松的数据可视化。

此外，MongoDB Charts 允许以安全的方式创建可视化仪表板并与每个人共享，或者只与目标团队成员共享。类似地，在后台使用的数据源也可以安全地共享。例如，除非需要，销售部门的数据不必提供给营销部门。非常强大，并且遵循 MongoDB 将安全性作为重中之重的设计。

在[下载 MongoDB 图表](https://www.mongodb.com/download-center/charts) Docker 图像并遵循[安装说明](https://docs.mongodb.com/charts/master/installation/)之后，我们能够连接到存储在 MongoDB Atlas 中的数据源并开始制作可视化仪表板。一旦连接到 MongoDB 图表服务器，我们需要采取三个步骤:

1.  添加数据源
2.  创建仪表板
3.  创建我们的图表

### 用 MongoDB 图表分析 Airbnb 数据

我用各个城市的 Airbnb 数据建立了一个数据库。我们将在这里探索来自华盛顿州西雅图的数据集，但也可以自己探索其他数据集。我们需要从包含我们数据的 Atlas 集群中获取连接字符串，并在图表中连接到它。

 [![Get URI From MongoDB Atlas for MongoDB Charts](../Images/76d9a40316978bbe90ba71e798f0254f.png)](https://webassets.mongodb.com/_com_assets/cms/atlas-URI-6k09mb3bg3.gif) 

<figcaption>从 MongoDB 图集中获取 URI</figcaption>

#### 添加一个数据源

随着我们的 MongoDB 图表服务器在`localhost:80`上运行，我们可以登录并前往*数据源*选项卡。我们使用 Atlas 中的 URI(`mongodb+srv://airbnbdemo:airbnb@airbnb-rgl39.mongodb.net/test?retryWrites=true`)并选择*连接*。接下来，我们会被问及希望使用该集群中的哪个数据源，对于本例，我将从`airbnb`数据库中选择 **seattleListingAndReviews** 。对于权限，我只想保持一切私有，所以我将接受默认值并选择*发布数据源*。发布后，我可以向数据源添加别名。我就叫它`Airbnb Seattle`。

> 注意:上面的 URI 包含一个样本 URI。您应该连接到自己的 Atlas 集群，并使用授权的用户名和密码。

#### 创建仪表板

下一步是创建一个实际的仪表板来容纳我们的可视化。在*仪表板*部分选择**新仪表板**并给它一个名称和描述，像**肯的 Airbnb 仪表板**。这将带我到我可以添加图表到我的仪表板。

#### 创建图表

点击*添加图表*按钮后，我们可以开始构建我们的可视化。我们想从下拉菜单中选择`Airbnb Seattle`数据源。MongoDB 图表自动确定哪些油田可供勘探。在本练习中，我想看看西雅图的哪些社区拥有最多的 Airbnb 房产，并按房产类型对它们进行分类。我们将使用该类型的堆积条形图。

 [![Assign X-Axis value in MongoDB Charts](../Images/264e61a7f9dc8b93bf62610f39eb9037.png)](https://webassets.mongodb.com/_com_assets/cms/x-axis-value-cz2tkvt97r.gif) 

<figcaption>将 X 轴值分配给 MongoDB 图表</figcaption>

 [![Assign Y-Axis value to a Stacked Bar in MongoDB Charts](../Images/dcd853d57b158316531978756b6d9339.png)](https://webassets.mongodb.com/_com_assets/cms/y-axis-value-h1llqzam8w.gif) 

<figcaption>将 Y 轴值赋给堆积条形图</figcaption>

 [![Assign a Series value to a Stacked Bar chart in MongoDB Charts](../Images/831eb40c9c4c4cc740726e09f3b351ab.png)](https://webassets.mongodb.com/_com_assets/cms/series-value-b1gprdumq6.gif) 

<figcaption>为堆积条形图分配系列值</figcaption>

现在，我们可以通过位置将图表命名为 *Properties，并保存它。然后，我们返回到我们的仪表板，在那里我们可以添加其他可视化效果，以便进一步探索。*

看看这个短片，看看从这个相同的数据源创建的其他一些可视化效果。

## 结论

MongoDB Charts 是一个出色的新工具，可以直观地探索您的数据。它具有一些针对特定使用情形的强大功能，例如:

*   对您的数据进行即席分析
*   天生理解文档数据模型的好处
*   基于用户的共享和权限使项目协作变得简单
*   它足够直观，可供非开发人员使用，允许进行自助式数据分析

MongoDB 图表是在 MongoDB 数据上构建可视化的最快方式。我鼓励你[下载它](https://www.mongodb.com/download-center/charts)并在今天试用它。让我知道你从 Airbnb 数据集中得到了什么可视化效果。我总是喜欢看人们如何探索他们的数据。

* * *

*这篇文章最初发表在 [MongoDB 博客](https://dev.to/mongodb/visualizing-your-data-with-mongodb-charts-2eh4-temp-slug-3842302)T3 上。*

[![Facebook](../Images/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F&t=Visualizing%20Your%20Data%20With%20MongoDB%20Charts&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fwebassets.mongodb.com%2F_com_assets%2Fcms%2Fatlas-URI-6k09mb3bg3.gif&p%5Btitle%5D=Visualizing%20Your%20Data%20With%20MongoDB%20Charts)[![twitter](../Images/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F&text=Hey%20check%20this%20out)[![google_plus](../Images/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F)[![reddit](../Images/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F&title=Visualizing%20Your%20Data%20With%20MongoDB%20Charts)[![linkedin](../Images/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F&title=Visualizing%20Your%20Data%20With%20MongoDB%20Charts)[![mail](../Images/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Visualizing%20Your%20Data%20With%20MongoDB%20Charts&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fvisualizing-data-mongodb-charts%2F)

用 MongoDB 图表可视化你的数据的帖子最早出现在肯·w·阿尔杰的博客上。