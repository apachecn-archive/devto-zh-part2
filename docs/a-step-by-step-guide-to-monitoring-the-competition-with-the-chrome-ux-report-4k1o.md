# 用 Chrome UX 报告监测竞争的逐步指南

> 原文：<https://dev.to/chromiumdev/a-step-by-step-guide-to-monitoring-the-competition-with-the-chrome-ux-report-4k1o>

# 症结是什么

Chrome UX 报告(又名“CrUX”)是 Chrome 衡量的真实用户体验的数据集。Google 每月在 BigQuery 上发布超过 400 万个源的快照，并统计 web 性能指标，如第一次内容丰富的绘制(FCP)、DOM 内容加载(DCL)和第一次输入延迟(FID)。

PageSpeed Insights 是另一个与 CrUX 集成的工具，除了关于如何提高页面性能的说明性信息之外，它还提供对原始性能和特定于页面的性能数据的轻松访问。

自 2017 年 11 月以来，CrUX 数据集一直存在并不断增长，因此我们甚至可以看到历史性能数据。

在这篇文章中，我将带你了解如何使用它来洞察你的网站的性能，以及它如何与竞争对手相抗衡。

# 如何使用

在 [BigQuery](https://console.cloud.google.com/bigquery?p=chrome-ux-report) 上写几行 SQL，我们就可以开始在网上获取关于 UX 的见解了。

```
SELECT
  SUM(fcp.density) AS fast_fcp
FROM
  `chrome-ux-report.all.201808`,
  UNNEST(first_contentful_paint.histogram.bin) AS fcp
WHERE
  fcp.start < 1000 AND
  origin = 'https://dev.to' 
```

原始数据像直方图一样进行组织，每个条块都有开始时间、结束时间和密度值。例如，我们可以查询“快速”FCP 体验的百分比，其中“快速”被定义为在一秒内发生。结果告诉我们，在 2018 年 8 月期间，dev.to 上的用户在大约 59%的时间里体验到了快速 FCP。

比方说，我们想将其与一个假设的竞争对手 example.com 进行比较。下面是该查询的样子:

```
SELECT
  origin,
  SUM(fcp.density) AS fast_fcp
FROM
  `chrome-ux-report.all.201808`,
  UNNEST(first_contentful_paint.histogram.bin) AS fcp
WHERE
  fcp.start < 1000 AND
  origin IN ('https://dev.to', 'https://example.com')
GROUP BY
  origin 
```

[![competitive analysis results](../Images/a18b13fbe064553dbe245e70dd3d34a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1WKJEXqG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9xjiq9du9mmoy3bnpp5i.png)

没有太大的不同，我们只是添加了另一个原点，并将其分组，这样我们最终得到了每个原点的快速 FCP 密度。事实证明，dev.to 的快速体验密度高于 example.com，后者的密度约为 43%。

现在，假设我们想要测量这种随时间的变化。因为这些表的日期都是 YYYYMM，所以我们可以使用通配符来捕获所有这些表并对它们进行分组:

```
SELECT
  _TABLE_SUFFIX AS month,
  origin,
  SUM(fcp.density) AS fast_fcp
FROM
  `chrome-ux-report.all.*`,
  UNNEST(first_contentful_paint.histogram.bin) AS fcp
WHERE
  fcp.start < 1000 AND
  origin IN ('https://dev.to', 'https://example.com')
GROUP BY
  month,
  origin
ORDER BY
  month,
  origin 
```

通过将结果绘制成图表(BigQuery 可以导出到 Google Sheet 进行快速分析)，我们可以看到 dev.to 的性能一直很好，但是 example.com 最近出现了大幅波动，导致其低于通常的约 80%的密度。

[![Chart of the fast FCP of the two origins](../Images/0c99e592ed9a08a961594053c3347f2e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QvKp3kEE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7wbpxk2hhxvywz7sg756.png)

# 好吧，但是没有 SQL 的解决方案怎么样？

我听到了。BigQuery 非常强大，可以编写定制查询来根据需要对数据进行切片，但是第一次使用它可能需要一些配置，如果一个月内查询超过 1 TB，您可能需要为超出部分付费。它还需要一些 SQL 经验，如果没有基本查询作为起点，很容易迷失方向。

有几个工具可以帮助你。首先是 CrUX 仪表盘。你可以通过访问[g.co/chromeuxdash](https://g.co/chromeuxdash)建立自己的仪表盘。

[![chrome ux dashboard for dev.to](../Images/7a29e4f1d1a194c463258743def0c627.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fiu9CBHR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zicn2kd1qvi6enykc0h.png)

这将为您生成一个仪表板，其中包括一段时间内的 FCP 分布。不需要 SQL！

仪表盘还有一个隐藏的超能力。因为 CrUX 中的数据是按照用户的设备和连接速度等维度划分的，所以我们甚至可以获得关于用户本身的聚合数据:

[![form factor distribution](../Images/c81940838a1ae79e1e398e4b15690c37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vX52_q6M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0sc9k9ut4wqyb438x9lm.png)

该图表显示，dev.to 上的用户大多使用手机，几乎从不使用平板电脑。

[![connection distribution](../Images/a4b3d99d57fa8ebf9246a1acbe23a5d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yH-QA1kP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x643izr9doux3hspw7od.png)

连接图显示 4G 和 3G 连接速度相差 90%。这些分类是*有效的*连接类型。这意味着在 4G 网络上体验接近 2G 速度的用户将被分类为 2G。使用快速 WiFi 的桌面用户将被归类为 4G 用户。

这个仪表板的另一个很酷的地方是它是专门为你定制的。你可以随意修改它，例如，你可以为 example.com 添加相应的图表来并排比较统计数据。

[![dashboard comparison of two origins](../Images/7cadf712f8c7a21be54d258335306b56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B49G9CbH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aft51iay5usxhw6naphz.png)

仪表板是用 Data Studio 构建的，它还具有方便的共享功能，如访问控制和可嵌入性。

# 获取症结数据的绝对最简单方法是什么？

使用 PageSpeed Insights web UI，您只需输入一个 URL 或原点，就可以立即获得图表。例如，使用`origin:`前缀，我们可以获得 dev.to 的原点范围的 FCP 和 DCL 统计信息:

[![dev.to on PageSpeed Insights](../Images/c27623a328c0e7fa5bda9970b75c1ff3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sDEuDJKk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8bsioqcradfbnw5c8yd.png)

[https://developers.google.com/speed/pagespeed/insights/?URL = origin % 3A https % 3A % 2F % 2f dev . to](https://developers.google.com/speed/pagespeed/insights/?url=origin%3Ahttps%3A%2F%2Fdev.to)

PSI 数据的一个重要区别是，它使用滚动的 30 天聚合窗口每天更新，而不是 BigQuery 上的日历月发布。这很好，因为它使您能够获得最新的数据。另一个重要的区别是特定于页面的性能数据的可用性:

[![dev.to open source perf](../Images/7b3a96fca501027e6768d22ee4e01c1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y4cmbE1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o72ish0emajd3cxl09tu.png)

[https://developers.google.com/speed/pagespeed/insights/?URL = https % 3A % 2F % 2f dev . to % 2f Ben % 2f devto-is-now-open-source-5 n1](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fdev.to%2Fben%2Fdevto-is-now-open-source-5n1)

这个链接为 [@ben](https://dev.to/ben) 的热门开源公告提供了症结数据。请记住，并不是所有的页面(甚至是原始页面)都包含在 CrUX 数据集中。如果你看到“不可用”的响应，那么这个页面可能没有足够的数据，就像在这种情况下的[AMA 主题页面](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fdev.to%2Ft%2Fama):

[![speed not available](../Images/bf9002eb3c1910acd151736f6b3bdd34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xa1BGf_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eu6qigr995vz9nymwwhs.png)

还要记住，包含当前 30 天窗口数据的页面在未来的窗口中可能没有足够的数据。因此， [@ben](https://dev.to/ben) 帖子的 PSI 数据链接可能在几个月内没有可用的速度数据，这取决于页面的受欢迎程度。确切的阈值是保密的，以避免对 CrUX 报告之外的不受欢迎的页面说太多。

# 我如何获得随时间变化的页面级数据？

这很棘手，因为虽然 PSI 提供了页面级数据，但它只提供了最近 30 天的快照，而不是像 BigQuery 那样的历史视图。但是我们可以做到！

PSI 有一个 [API](https://developers.google.com/speed/docs/insights/v4/getting-started) 。我们可以编写一个巧妙的小脚本，每天 pings 那个 API 来提取性能数据。

1.  首先，创建一个新的[谷歌表单](https://sheets.google.com)
2.  进入“工具”菜单，选择“脚本编辑器”
3.  在应用程序脚本编辑器中，给项目命名为“PSI Monitoring”，进入“Resources”菜单，选择“Advanced Google Services”，然后点击“Google Cloud Platform API Dashboard”中的链接以启用服务![apps script services](../Images/7ad04af0c4b3131067e58782a5c88e8e.png)
4.  从 Google 云平台控制台中，搜索 PageSpeed Insights API，启用它，然后单击“创建凭据”以生成 API 密钥
5.  在应用程序脚本编辑器中，转到“文件”、“项目属性”，创建一个名为“PSI_API_KEY”的新“脚本属性”，并粘贴新的 API 密钥

现在你已经准备好剧本了。在`Code.gs`中，粘贴以下脚本:

```
// Created by Rick Viscomi (@rick_viscomi)
// Adapted from https://ithoughthecamewithyou.com/post/automate-google-pagespeed-insights-with-apps-script by Robert Ellison

var scriptProperties = PropertiesService.getScriptProperties();
var pageSpeedApiKey = scriptProperties.getProperty('PSI_API_KEY');
var pageSpeedMonitorUrls = [
  'origin:https://developers.google.com',
  'origin:https://developer.mozilla.org'
];

function monitor() {
  for (var i = 0; i < pageSpeedMonitorUrls.length; i++) {
    var url = pageSpeedMonitorUrls[i];
    var desktop = callPageSpeed(url, 'desktop');
    var mobile = callPageSpeed(url, 'mobile');
    addRow(url, desktop, mobile);
  }
}

function callPageSpeed(url, strategy) {
  var pageSpeedUrl = 'https://www.googleapis.com/pagespeedonline/v4/runPagespeed?url=' + url + '&fields=loadingExperience&key=' + pageSpeedApiKey + '&strategy=' + strategy;
  var response = UrlFetchApp.fetch(pageSpeedUrl);
  var json = response.getContentText();
  return JSON.parse(json);
}

function addRow(url, desktop, mobile) {
  var spreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = spreadsheet.getSheetByName('Sheet1');
  sheet.appendRow([
    Utilities.formatDate(new Date(), 'GMT', 'yyyy-MM-dd'),
    url,
    getFastFCP(desktop),
    getFastFCP(mobile)
  ]);
}

function getFastFCP(data) {
  return data.loadingExperience.metrics.FIRST_CONTENTFUL_PAINT_MS.distributions[0].proportion;
} 
```

该脚本读取您分配给脚本属性的 API 键，默认情况下有两个来源。如果您想将其更改为 URL 端点，只需省略前缀`origin:`即可获得特定页面。

该脚本将通过 PSI API 运行桌面和移动的每个 URL，并将快速 FCP 的比例提取到表中(名为“Sheet1”，因此保持任何重命名同步)。

您可以通过打开“选择功能”菜单，选择“监视器”，然后单击三角形的运行按钮来测试它。第一次运行时，您需要授权脚本运行 API。如果一切顺利，您可以打开工作表查看结果。

[![PSI API results](../Images/8b62a607ccd932455db4c8682e7e5a79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ezORuDsT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7z99duf7x4k0jufzshyn.png)

我添加了一个标题行并格式化了列(A 是日期类型，C 和 D 是百分比),以便于阅读。从那里你可以做所有工作表能做的强大的事情，比如建立一个数据透视表或者可视化结果。

# 但是监控呢？

幸运的是，有了 Apps 脚本，你不需要每天手动运行它。您可以设置“触发器”来每天运行`monitor`函数，这将每天为每个 URL 添加一个新行。要进行设置，请转到“编辑”菜单，选择“当前项目的触发器”并添加一个新的触发器，配置如下:

*   运行`monitor`功能
*   “时间驱动”
*   "日期计时器"
*   选择脚本运行的任意时间，或者保留默认值“午夜到凌晨 1 点”

保存触发器后，您应该能够每天返回到这个表，查看所有被监控的 URL 或源的最新性能统计。

# 给我一些我可以克隆的东西！

这是我做的床单。转到“文件>制作副本……”去克隆它。现在，除了 API key 属性和每日触发器之外，一切都为您设置好了。按照上面的步骤进行设置。您还可以清除旧数据并覆盖示例 URL 来定制分析。瞧啊。

# 包装完毕

这篇文章探讨了从 Chrome UX 报告中获取真实用户信息的四种不同方式:

1.  BigQuery
2.  CrUX 仪表板
3.  PageSpeed 洞察
4.  页面速度洞察 API

这些工具使所有专业水平的开发人员都能够利用数据集。在未来，我希望看到这些数据得到更广泛的应用。例如，如果你用来监控网站真实用户表现的工具也能向你展示它与你的竞争对手相比如何呢？像这样的第三方集成可以打开一些非常强大的用例，以更好地了解用户体验。

我还写了我们如何将它与其他数据集结合起来，以更好地理解整个网络的状态。比如我用 [HTTP Archive](https://httparchive.org) 数据加入 CrUX，分析顶级 CMSs 的[真实用户表现。](https://discuss.httparchive.org/t/cms-performance/1468)

[![CMS performance](../Images/08e4f9a9cc6a003d38799ba95c180c79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--me5xUZ8c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://discourse-cdn-sjc2.com/standard17/uploads/httparchive/original/2X/7/79f6dc42c93f657e9112174daa6c19459b3725bf.png)

该数据集甚至还不到一年，它有很大的潜力成为更好的用户体验的真正驱动力！

如果你想了解更多关于 CrUX 的信息，你应该阅读官方开发者文档或者观看我制作的关于它如何工作的更多信息的网络视频的 T2 状态。还有一个 [@ChromeUXReport](https://twitter.com/ChromeUXReport) 账户，如果你有任何问题，我可以用它。