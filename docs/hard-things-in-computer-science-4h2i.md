# 计算机科学中的难题

> 原文：<https://dev.to/patricktingen/hard-things-in-computer-science-4h2i>

DataDigger 是一款面向 OpenEdge 开发者的开源数据库浏览器。它使他们能够查看、更新、删除、即时通讯和导出数据库中的数据。DataDigger 完全是用 OpenEdge 4GL 编写的，有超过 40，000 行代码。里面是一些真正的宝藏，所以我将剖析数据挖掘者，揭示他们。今天:缓存。

几年前，在 Netscape，Phil Karlton 说:

> 计算机科学只有两个硬东西:
> 
> 命名东西，缓存失效和 off-by-1 错误

他是对的。

我在 2008 年用 DataDigger 的第一个版本解决了第一个问题。这是理查德·塔迪温的工具 DataHack 的分支。第二个问题直到 2013 年才显现出来，当时我在 DataDigger 18 中引入了缓存来加速一些事情。因为，你知道，缓存可以加快速度。大时代。

我记得在那个版本中，我对 DataDigger 改进的启动时间感到惊讶。此时我记不起确切的数字了，但它大约快了 10 倍。好家伙，我很高兴我介绍了它。但是，伙计，我后来后悔了吗，因为旧东西一次又一次地从储藏室里爬出来。我花了很长时间才把它弄好。现在，DataDigger 中基本上有两个缓存系统:一个用于表定义，一个用于设置。我将带你浏览这些，并解释我如何设法填充它们，并在适当的时候使它们无效。

**文件定义**

收集文件定义需要相当长的时间。对于一个小型数据库来说，这一点没有被注意到，但是如果您有——就像我的一些用户一样——一组 8 个数据库，有几千个(是的，几千个)表，那么分析它们需要相当多的时间。启动时，DataDigger 首先检查数据库最后一次修改的日期和时间。该文件(如果存在)应该位于缓存文件夹中。让我们看看我的缓存文件夹的内容:

[![leyndii](../Images/2c042440c7c79f1e49f55dd9db52afa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pId-JRDe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/05/leyndii.png%3Fw%3D840)

前两个文件是数据库缓存文件。我们可以通过以“db”开头的名称来识别它们，后面是名称以及最后修改的日期和时间。最后修改的日期可以这样找到:

```
FIND FIRST \_DbStatus NO-LOCK. 
MESSAGE \_DbStatus.\_dbstatus-cachestamp VIEW-AS ALERT-BOX. 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我的运动数据库的两个文件。这是因为我通过添加一个字段更改了模式。将最后修改日期改为今天。当我再次启动 DataDigger 时，它查看 _DbStatus 中的日期，发现值为 2018 年 5 月 10 日，19:45:42。然后，它查找名为“db . sports . thu _ May _ 10 _ 194542 _ 2018 . XML”的文件，但由于该文件不存在，它重新创建了该文件，并分析了所有表的 CRC。这种机制确保数据库定义的缓存在适当的时候失效。

数据库缓存文件包含数据库中所有表及其 crc 编号的列表。当我们想要读取单个表的缓存时，这个值很重要。

[![dv2xpzo](../Images/feda5b9e5dc4479fe3815bd69fad1223.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X8Go9Zry--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/05/dv2xpzo.png%3Fw%3D840)

如您所见，我的客户表的当前 crc 是 6269。如果你在上面的文件列表中查找，你会发现一个名为“运动”的文件。' Customer.6269.xml '。这是包含此版本客户表设置的文件。还有另一个编号为 48132 的客户文件。显然，这是我改变之前的 CRC。

因为 CRC 已更改，并且 CRC 是文件名的一部分，所以这也确保了缓存是无效的。如果 DataDigger 找不到一个缓存文件，它将从 _file 表中读取定义并创建一个缓存文件，这样在下一次启动时就可以读取它。

由于缓存为 DataDigger 的启动提供了如此大的推动力，所以拥有它们是很方便的。但是如果 DataDigger 应该等到您选择了表，您仍然需要等待。为了避免这种情况，DataDigger 在启动时会在后台进行一些预缓存。它使用最近使用的表的列表，并检查每个表的缓存文件是否存在。在设置中，您可以在“行为”选项卡中找到它:

[![cakvqic](../Images/542061dc4bcff987e6cd549f2d70f136.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bomJtlON--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/05/cakvqic.png%3Fw%3D840)

您可以取消选中缓存设置，但通常不需要这样做。构建缓存是在后台完成的，通过使用调度机制，正如我在上一篇文章[中所描述的那样，将计时器变成调度器](https://wordpress.com/post/datadigger.wordpress.com/1000)。为了确保数据挖掘器保持响应，它每 2 秒钟只检查一个表。

就是这样。在下一篇文章中，我将详细阐述设置的缓存，因为这完全是另一回事。如果您在自己的项目中使用了缓存，或者我的技术可以改进，请告诉我。现在:玩得开心！