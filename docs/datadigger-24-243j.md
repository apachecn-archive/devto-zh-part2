# 数据挖掘者 24

> 原文：<https://dev.to/patricktingen/datadigger-24-243j>

DataDigger 是一款面向 OpenEdge 开发人员的开源数据库浏览器。它使他们能够查看、更新、删除、即时通讯和导出数据库中的数据。《数据挖掘者》完全是用 OpenEdge 4GL 版编写的，并且像免费啤酒一样免费。

在 DataDigger 24 中，除了大量的小修正和改进，还引入了一些新特性。我要特别感谢 DataDigger 测试团队，他们由 50 多人组成，测试 DataDigger 的 beta 版本。对测试版感兴趣？在设置屏幕中设置更新检查来检查 beta 版本，DataDigger 会警告你。想加入团队并收到通知邮件吗？给我发一条[短信](https://datadigger.wordpress.com/contact/)，我会把你加到名单里。

### 启动更快

大型数据库会话的第一次启动可能需要相当长的时间。这是因为 DataDigger 必须检查数据库的元模式。如果很大，就需要一些时间。在这个版本中，这大约是 12 倍的速度，使启动时间从 2 分钟多一点回到 10 秒多一点。

### 新建工具栏

如果你启动 DataDigger，你会注意到左边的工具栏。以前，这是隐藏在汉堡菜单中的，但现在你可以选择让它们永久显示。用汉堡按钮 [![](img/18d16e8564666276af993df3e3e21c45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MmRavc5W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/sliNTEn.png) 

隐藏或查看工具栏用底部的按钮 [![](img/4a435e5f537a0e177dbd36bad86b4b7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iLSo_dNS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/UFInsVx.png) 展开/折叠

### 生成代码

如果您在表格中单击鼠标右键，浏览并选择“生成代码”。您将看到一个基于表定义的可能生成的代码片段列表:

[![](img/e300434487580e71b77416cf7bf11f7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a7gVyJXk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vwR3UoB.png)

[![](img/b97402171fc901301e61dff8dd20bf4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f3Ii9aC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ms7eAzC.png)

在这里，您可以根据自己的喜好调整如何生成代码的设置。

### **成群的收藏夹**

在 DD24 中，您可以拥有多组收藏夹。图像处理与发票相关的东西。然后，您只想查看与开票相关的表。下一张票是关于您将要实现的新特性，它涉及一组完全不同的表。我打赌你明白了。

[![yraowc9](img/050134646be1127ed3aac05541a087ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uX3d5R5u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/08/yraowc9.png%3Fw%3D840)

按下新的“+”按钮创建一个新组。

要维护一个组，按下编辑按钮，您将进入一个屏幕，在该屏幕中您可以添加/删除组中的表，或者重命名或删除组

[![yj0vp8n](img/9ca4943ea3606e220393e9dd4d64cd0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h3mhPZ_r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/08/yj0vp8n.png%3Fw%3D840)

### **憋黄提示**

一个可能会受到你们中一些人欢迎的功能是取消黄色提示的选项。只需进入设置，取消“显示新功能提示”

[![](img/854160366d9cdfc28fd3aa207b64a57d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yKQ-NtQl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/lFwJZVc.png)

您现在不会再看到黄色框架。

### **设置工作文件夹**

您现在可以为 DataDigger 设置工作文件夹。默认情况下，工作文件夹是您安装它的文件夹，但是如果您像这样设置 DataDigger.ini 中的值:

```
[DataDigger]WorkFolder=c:\DataDigger\%username%\ 
```

Enter fullscreen mode Exit fullscreen mode

然后，DataDigger 将使用您在“工作文件夹”中指定的文件夹作为缓存文件夹、用户 ini 文件和备份文件夹。不再对 DataDigger 的程序文件夹进行任何写操作。

请注意，如果您没有程序文件夹的写权限，您可能还想在同一部分设置以下内容:

```
[DataDigger]AutoCompile=no 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您这样做，DataDigger 将不会编译自己。所以如果你想在编译过的源代码上运行，你应该自己提供或者不编译就运行。

您可能已经从上面的例子中猜到了，在文件夹的名称中使用 OS-vars 是可能的。如果不存在，将会创建它。如果无法创建，DataDigger 将退回到 DataDigger 程序文件夹。

### 工厂重置

通常，DataDigger 会问一个问题。很多这样的问题可以通过勾选“不要再问”来解决。如果你想重置你的答案，你可以重置这些。进入设置屏幕，选择“行为”选项卡，并选择以下选项之一:

[![](img/9520a9ba818eb9a54cd0a9043f5d10d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--99-l_ao7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6Y2lW7i.png)

这两个操作都会要求您确认您的选择:

[![](img/6c15cafaedfc78c8529246ec302c8b31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rxOeqqqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6hlO4ho.png)

[![](img/453a61d238640598650e3e86e867568a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jKiC46lR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/0tErgrV.png)

*这篇文章之前发表在 [DataDigger 博客](https://datadigger.wordpress.com/2018/10/03/20181003/)T3 上*