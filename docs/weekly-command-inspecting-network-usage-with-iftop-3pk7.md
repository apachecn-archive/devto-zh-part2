# 每周命令:使用 iftop 检查网络使用情况

> 原文：<https://dev.to/roperzh/weekly-command-inspecting-network-usage-with-iftop-3pk7>

[![iftop-overview](../Images/59f3b3e4705904a1603286a292ecc14a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fx2g-Ycf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/40357344-412b927c-5d92-11e8-9ca6-8588bcdf009b.jpg)

如果您需要一个工具来检查您的网络使用情况，而不必连接代理或安装更复杂的工具，如 Wireshark，`iftop`可能是您正在寻找的。

## 基础知识

```
iftop | [-nNpblBP] [-i interface] [-f filter] [-F net/mask] [-G net6/mask6] 
```

运行时，`iftop`使用整个屏幕显示网络使用情况。在顶部有一个对数标度，它给出了流量的直观指示，在底部有一个包含所有捕获的包的信息的摘要。

默认情况下，该程序显示每对主机在前 2、10 和 40 秒间隔内发送和接收数据的速率。箭头(`<=`和`=>`)指示数据流的方向。例如:

```
foo.example.com => bar.example.com 1Kb 500b 100b
                 <= 2Mb 2Mb 2Mb 
```

如果在没有参数的情况下调用，`iftop`会显示它所能找到的第一个接口的信息，这个接口看起来像一个外部接口，大多数情况下，您可能希望提供一个带有`-i`标志的特定接口。

*注意:要查找接口，可以在*nix 系统中使用`ifconfig`，或者在 macOS 中使用`networksetup -listallhardwareports`。*

系统需要特殊权限来执行`iftop`，这在大多数情况下意味着以根用户身份运行它。

这个程序非常通用，你可以过滤，排序，甚至在文本模式下使用，如果你不需要交互界面的话。

## 例子

### 筛选过滤器

`l`允许您输入 POSIX 扩展正则表达式，该表达式将用于过滤显示器中显示的主机名。

值得注意的是，这发生在比过滤器代码晚得多的阶段，并且不影响实际捕获的内容，因此，显示过滤器不影响屏幕底部的总计。

### 过滤代码

屏幕过滤器有利于进行快速、直观的过滤，但如果您只需要跟踪在特定条件下传输的数据包，您可以使用过滤器代码来选择要计数的数据包。

`iftop`使用 [pcap 格式的过滤器](http://www.manpagez.com/man/7/pcap-filter/)作为输入，你可以给它们提供`-f`标志，或者在 app 中通过按`f`来提供。作为一般规则，我总是建议使用标志来防止意外行为。

*注意:因为只计算 IP 数据包，所以指定的代码被评估为“(过滤代码)**和 ip** ”。*

### 排序

您还可以使用一组预定义的排序方法，通过按以下键之一来对视图中显示的数据进行排序:

*   `1/2/3` -按第一/第二/第三列排序(2、10 和 40 秒间隔)
*   `<` -按信号源名称排序
*   `>` -按目的地名称排序
*   `o` -冻结当前订单

### 文本模式

您可以使用`-t`标志启用文本模式，在文本模式下，输出会间隔打印到标准输出。