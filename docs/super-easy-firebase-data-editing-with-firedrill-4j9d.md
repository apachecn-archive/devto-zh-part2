# 使用 FireDrill 超级轻松地编辑 Firebase 数据

> 原文：<https://dev.to/scottlepp/super-easy-firebase-data-editing-with-firedrill-4j9d>

在我的上一篇[帖子](https://dev.to/scottlepp/introducing-firedrill---power-tools-for-firebase-g9f)中，我向你展示了如何使用 [FireDrill](https://firedrill.co) 在你的 Firebase 数据库中轻松找到数据。现在，我将向您展示如何轻松管理您的数据。

有了 FireDrill，我们可以快速找到我们的 Firebase 数据，一旦我们找到它，我们可以很容易地编辑它。如果您想使用电子表格来进一步可视化或操作数据，也可以很容易地将数据导出为 CSV(电子表格，Excel)。最后，您可以从 CSV 格式导入批量数据。

让我们来看看。

我将再次使用我的“鞋子”系列(与我妻子的鞋子系列规模相差甚远)。在这里，我钻了“鞋”。

[![shoes](img/2349a7e3a7439e245435fd0d31c431dc.png "Shoes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6OJwx5TV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9gmjciniaogokxfgtsbl.png)

太好了，我找到我的鞋子了。我有一个销售，所以我想改变匡威“查克·泰勒”鞋的价格。因此，我将在搜索结果上单击“编辑”。该卡将进入编辑模式。

[![edit](img/8b515e621b65fc2d944b39e8a75f06b6.png "Edit")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3yqcSoYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fbitmwbjfv00s4bx5a5u.png)

现在，我可以在这里更新任何字段，包括带有日期选择器的日期。我将更新我的价格，然后单击“保存”。就是这样！轻松点。我的价格现已更新。

[![edit](img/e140c3df2516ec6c45bb92f2f394fb2d.png "Edit")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5wHDVErk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfv1kjec58ljj03wfww7.png)

现在我想添加一只新鞋。如果我使用 Firebase 控制台，我将向我的集合中添加一个新项目，然后再次输入每个字段的名称和值，这有点麻烦。在 Firedrill 中，我可以克隆一个现有的鞋子并更新值。

在任何搜索结果项目上，单击“克隆”，一个新项目将在编辑模式下添加到结果中:

[![edit](img/8607522d644131fc041c1cc831628536.png "Edit")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2IFbw-z2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pdbxqpjtd736ygvhvlo5.png)

现在更新这些值，然后单击“保存”。嘣！新鞋。简单多了。

太棒了，我可以轻松地添加和编辑内容。现在我想删除一些项目。没问题！只需点击卡片来选择它们，一旦选中，你会看到一个删除链接。

[![delete](img/70dbb75660353f9e65237c90b3058445.png "Delete")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tOcBumCR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1c5lv4fp5gd34bhu0zy7.png)

现在点击上面看到的“删除”链接。噗！不见了！(是啊，要小心)。

所以现在我想将我的鞋子导出为 CSV(电子表格)格式，这样我就可以运行一些电子表格功能，或者只是以其他工具通用的格式，如 Tableau 或其他可视化数据的格式。或者，我可以只导出数据，为导入批量数据提供一个简单的起点。轻松点。只需点击“导出”链接。

[![export](img/8f482a108762251e0467b5364d7879e3.png "Export")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FILH1szs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6zqywft9u59p7xcxt569.png)

嘣！已导出。现在我想从其他数据源导入大量的鞋子。我可以将其他数据源批量复制或导出到 CSV 中。一旦我有了要导入的 CSV，只需点击“导入”

这是我的电子表格数据。让我们导入这个。

[![export](img/8c9ad79f706deaf807c20b166d85bcd8.png "Export")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cyjeiSei--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3tu0abe1gitudub7kia9.png)

单击“导入”链接，浏览到您的 csv 文件并选择它。嘣！进口的！

我已经向您展示了使用 FireDrill 管理 Firebase 数据是多么容易。试试看。如果您遇到任何问题，请在此记录它们[消防演习问题](https://github.com/scottlepp/fire-drill/issues)。

如果你有更多的想法，或者你想成为一名贡献者，请告诉我！