# 用 JMeter 对 API 进行负载测试

> 原文：<https://dev.to/0xshetty/load-testing-an-api-with-jmeter-47d8>

负载测试是一个独立的流程，我所做的一切都是在它上面平稳的航行。由于手头时间不多，唯一选择的武器是 Jmeter，这是一个非常好的工具！！

[![Load with Max request](../Images/33c4351a3ad0e43022b32ed5bbc80600.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vKbbEWOz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0i6k7vlbd6v5u70atn9y.jpg)

我们不得不测试用 nodejs 构建的 facebook 聊天机器人。这个应用程序是 FB 的一个 webhook，用于将聊天消息推送到我们的应用程序。

这就是我们如何实现的

在 Windows 上:

从[这里](https://jmeter.apache.org/download_jmeter.cgi)下载 JMeter。

现在运行 jmeter.bat，这将打开一个 java GUI，您可以在其中轻松配置想要测试的端点。

*   添加一个线程组，并设置想要生成的用户/线程数量，以测试您的 API
*   添加 Http 请求采样器
*   填写直观的字段，如 URL、请求正文、方法等。,
*   要设置 HTTP 头，如内容类型或授权字段，请右键单击 HTTP 请求并添加配置元素 HTTP 头管理器，然后添加键值
*   要查看将产生的所有请求的结果，请创建添加侦听器视图结果树。
*   您可以单击播放按钮，查看查看结果树以验证状态。

一台机器上可以产生的线程数量是有限制的，所以我建议做两件事

使用命令提示符运行这个测试，而不是 GUI，因为如果内存耗尽，它可能会放弃你。另外，如果你的机器没有足够的内存，试着在多台机器上运行同样的测试。

所以一旦你创建了*。GUI 中的 jmx 文件。

在使用多线程运行 JMeter 之前，请将 JVM 内存设置为系统可以占用的最大内存。否则，您可能会在每次越过限制时都以“java.lang.OutOfMemoryError”结束。

在 fav 编辑器中打开 JMeter.bat 文件，搜索“set HEAP ”,然后替换为

> set HEAP="-Xms512m -Xmx10480m "

*   这个数字用在我的 16gb ram 笔记本电脑上，我能够运行 2k 到 4k 的线程。

使用 JMeter 的 bin 文件夹中的以下命令

> start/real time Java-jar Apache jmeter . jar-n-t filename . JMX-l results 1 . jtl-j log 1 . log

现在有很多方法可以获得测试数据，我们选择的方法是 CSV 数据集。我们必须在 http 请求上添加配置元素 CSV 数据集配置。并对从 CSV 文件中选取数据的请求进行一些小的修改。使用 csv 头作为 json 中的变量，如下所示

> {
> "发件人":{"id": "${userid}"}
> }

所以 userid 是必须在 CSV 数据集配置中指定的列名或标题，而不是 CSV 文件本身。CSV 文件中的每一行都将被视为一个用例，因此标题不能出现在该文件中。

这是项目结构的快照

[![project structure](../Images/297dacb57a7b4bb99db511ca6f9ee3e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MpOAC4C2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ul2l0sgttux8o56l6yti.PNG)

这对于基本的负载测试来说应该足够了。

愉快的负载测试。