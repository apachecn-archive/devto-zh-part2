# 使用自定义维度和指标的谷歌分析

> 原文：<https://dev.to/_robynedgar/using-google-analytics-with-custom-dimensions-and-metrics--4dij>

*供稿[坦尼娅·达科斯塔](https://www.linkedin.com/in/tanya-da-costa-0616a390/)T3】*

当我们建立网站时，我们希望能够弄清楚人们是否真的在浏览我们的努力工作。表现好的网站是在做好自己的工作，表现不好的可以调整得更好。

实现这一目标的最佳工具是谷歌分析。Google Analytics 是一个收集和显示与消费者如何使用和浏览您的网站相关的数据的伟大工具，只需一点编码经验，分析就可以快速扩展并为您的网站定制。本教程将介绍如何使用自定义维度和指标来设置自定义事件，这些维度和指标专门针对用户如何与您的网站进行交互。

首先，我们将概述一个使用该工具集的场景。假设我们有一个视频流媒体网站，用户必须注册才能观看内容。我们的主页显示不同的视频缩略图，当点击时，在用户的浏览器中播放该视频的简短预告片。

让我们从确定用户最感兴趣的视频开始。我们可以通过跟踪每个视频预告的播放次数来做到这一点。

### 第一步:为你的网站建立一个分析账户

[![alt text](img/0e7e492ac273801aca524bba82fd4002.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ej94bEow--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AQBhKdgIKMXgi-ApHkl1ueA.png) 
[在此注册账户](https://analytics.google.com/analytics/web/?authuser=0#provision/SignUp/)

一旦你注册了一个帐户，进入管理面板。在管理面板中，从帐户下拉列表中为网站创建一个帐户。完成后，Google 将创建一个与帐户同名的属性，并为您提供跟踪代码。

### 步骤 2:加载“ga”命令所需的库

首先，让我们找到我们网站的唯一跟踪代码。导航到管理设置(齿轮图标)，进入“跟踪信息”并在“跟踪代码”下找到跟踪 ID。

[![alt text](img/71649dbe991de5d03eeab3162e845225.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XU-_y11l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2Al7ZGVSVBOzAU13NE.)

接下来，让我们将信息添加到我们的网站，这样我们就可以开始跟踪。在我们网站的索引头，添加以下代码片段。请确保替换为唯一的跟踪 ID。

[![alt text](img/59cbae64db8e67e3e5bb3d8b42f1b42a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6MEplHQQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/krfiv418i1l7dpuo4nht.png)

### 第三步:发送您的第一个事件，并查看它的运行情况

回想一下我们的场景，一个有视频预告片的流媒体网站。由于我们有兴趣了解用户在我们的主页上播放了哪些视频预告片，并且预告片仅在用户点击该视频的缩略图后才会播放，因此让我们放置一个跟踪器，每当一个视频缩略图被点击时，它都会告诉 Google Analytics。

我们所要做的就是发送一个带有我们想要跟踪的数据的自定义 Google 事件。使用参数 eventCategory、eventAction、eventLabel 和 eventValue 发送自定义事件。如果您有兴趣了解更多，这些字段在[分析文档中进行了细分。](https://developers.google.com/analytics/devguides/collection/analyticsjs/events)

让我们关注必需的字段 eventCategory 和 eventAction，并使用 eventLabel 和 eventId 在事件中传递特定于事件的数据(在本例中，是所单击视频的名称和 Id)。我们将事件类别命名为“主页”来描述在主页上进行的操作，并将事件操作称为“预告片 _ 播放”。

[![alt text](img/44ed586e69e13185eeab5c7e24f18cb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GQOBiuyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpxhjlwqu8z94wv3jlva.png)

让我们通过触发我们的事件来尝试一下。在这种情况下，就是点击一个视频。

为了确保事件正常运行，让我们转到 Google Analytics 门户，并选择实时事件。

[![alt text](img/6e0493fa5657a5fed4ff9e330457785d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7uS7Is1w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AAvu-gniH2KbK6YCBX2c8YA.png)

我们的视图应该反映我们刚刚在网页上触发的动作。

### 步骤 4:创建一个仪表板和一些小部件

对于这一步，我们将导航到左侧菜单中的定制面板。点击仪表板，并选择创建空白画布。

浏览 Google 维度和度量标准，尝试小部件允许的不同组合。度量是定量值，而维度是定性值。

让我们从第一个小部件开始。用 dimension 事件标签和 metric Total Events 制作一个 table 小部件，为了只显示“trailer_play”事件，添加一个过滤器，只显示事件动作“trailer_play”的数据。

[![alt text](img/9a0aed4334b34b8015c9f371b5646a4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kMcb1znr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AMv6FLhphVUH0vBHy.)

确保右上角的日期范围设置为包括今天的日期。如果幸运的话，我们之前发送的事件将已经填充到您的图表中(但是请耐心等待，数据可能需要几个小时才能显示出来)。

谷歌有一些很好的自定义维度供我们使用，如他们从全球 cookies 中收集的国家、年龄和其他人口统计信息(确保为帐户启用人口统计报告)，但如果我们想要我们自己的与我们的事件相关的定性数据，而不必依赖于通用的“事件标签”、“事件值”和“总事件”，该怎么办？这样，我们的图表将更具体地针对事件，更具可读性，我们将不必担心通过我们的特定事件操作来过滤我们的数据。

让我们把我们的事件动作“预告片播放”做成自己的量化指标，为视频名称做一个维度。同时，让我们创建一个自定义维度来更好地描述我们的用户。

### 步骤 5:创建定制维度和度量

假设现在，我们不再仅仅知道哪些视频预告片获得了最多的动作，我们也有兴趣知道未注册用户正在观看的视频类型。除了创建一个描述动作类型(预告片播放)的自定义指标和一个反映播放视频名称的维度，我们还将创建一个允许我们传递用户数据的维度，即用户是否注册。

在 Google Analytics 门户中，单击设置(齿轮图标)、自定义定义(在属性列下)、自定义指标，然后单击新建自定义指标。

[![alt text](img/e93536656bccaef991f9bc3981545a87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sDyKSwZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2ApHGEEF1j3pQ8gE3F.)
[![alt text](img/406e52985d068f2ab2bbcad76f295335.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s---MwDQNtQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AS2XXL4DsyLtJwOym.)

让我们将这个度量命名为“预告片播放”。现在让我们将范围设置为“Hit ”,格式类型设置为“Integer”。最后，确保选中活动，点击创建，然后完成。

[![alt text](img/1dc4feb2bcf878a793e2b90f0700b6fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LVAGjCHy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AXs4wtaMYtLF4Q24S.)

现在我们将对自定义维度做同样的事情。首先导航到设置(齿轮图标)、自定义定义(在属性列下)、自定义尺寸，然后选择新建自定义尺寸。

我们将新维度命名为“视频名”。再次将范围设置为“命中”。单击创建。创建另一个维度来描述我们的用户。让我们将第二个维度命名为“用户状态”，这次我们将范围设置为“用户”。单击创建。

每个自定义定义都与一个索引号相关联，我们将在发送事件时使用该索引号。通过导航至设置(齿轮图标)、自定义定义、自定义，查看并记录每个自定义指标和维度的这些指数。

[![alt text](img/cc0f22f64b96fc0f3ff7b96bbadcb246.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5aqlN1TA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AhnKcIfA2PZcy_KNR.)

### 步骤 6:发送包含您的自定义维度和指标的事件

现在让我们回到我们的代码，重新安排如何发送我们的事件。我们可以保留所有的事件属性，但是也可以添加自定义的度量和维度。

[![alt text](img/83ec1500d2a96284cd2aba63eeafca90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pO_tUSgs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/auykhjhh05c2895vef94.png)

### 步骤 7:创建一个小部件

现在我们对事件的跟踪更加有力了，让我们创建一个图表来直观地显示我们的数据。

在返回控制面板时，请注意，这一次，当我们创建一个小部件时，我们的自定义维度和指标将显示在维度和指标下拉列表中，这使我们可以创建一个图表来准确显示我们想要的内容。

[![alt text](img/01b522b7350c2a312f365fa2927d3b15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iaCvIQkz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Ay8B54Oh5vVBKEi-4.)

就是这样！我们在谷歌分析中创建了定制的事件跟踪。现在，我们正在跟踪网站上发生的实际事件，我们将能够更好地管理我们的内容，并为我们的用户群策划我们的产品。

发现这些提示有用吗？一定要感谢我们的开发者，并关注媒体上的 [Hifyre，以获得两周一次的](https://medium.com/hifyre)[开发技巧](https://medium.com/hifyre/dev-tips/home)。你也可以在[推特](https://twitter.com/hifyre)、[脸书](https://www.facebook.com/Hifyre/)、 [Instagram](https://www.instagram.com/hifyre/) 和 [LinkedIn](https://www.linkedin.com/company/mdh/) 上找到我们。

哦，我们有没有提到，我们在招人？我们希望收到您的来信。