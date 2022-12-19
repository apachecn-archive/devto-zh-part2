# 快速提示:如何在本地测试基于定时器的 Azure 函数

> 原文：<https://dev.to/seankilleen/quick-tip-how-to-test-timer-based-azure-functions-locally-55p6>

我最近一直在研究 Azure 函数，它非常棒。我碰到了一个小绊脚石，所以我想我应该在这里记录下来，以防其他人有同样的问题。

## 挑战

我有一个由定时器触发的 Azure 函数。

我想在本地测试这个功能，但是它似乎只在我设置的时间表上工作。在调试时弹出的控制台窗口中，我看到

> 该计划接下来的 5 次出现将是:2018 年 9 月 14 日 4:00:00 2018 年 9 月 21 日 4:00:00 2018 年 9 月 28 日 4:00:00 PM10/2018 年 5 月 4:00:00 PM10/2018 年 12 月 12 日 4:00:00 下午

我如何强制运行这个函数，以便在本地测试它，而不需要等待下一次计划运行？

## 解

然后，您可以对`/admin/functions/[functionName]`端点进行 POST 重新请求以执行该函数。

例如，如果 azure functions 在`localhost:7071`上运行，我有一个名为`MyFunction`的函数，我可以发送一个 http POST 请求到:

`http://localhost:7071/admin/functions/MyFunction`

然后该功能将被执行。

希望这能帮别人节省一些时间。编码快乐！

[快速提示:如何在本地测试基于定时器的 Azure 函数](https://SeanKilleen.com/2018/09/test-azure-timer-function-locally/)最初由 Sean Killeen 于 2018 年 9 月 7 日在 SeanKilleen.com[发表。](https://SeanKilleen.com)