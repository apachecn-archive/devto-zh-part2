# 如何计算 VoIP 中的数据包大小

> 原文：<https://dev.to/onmyway133/how-to-calculate-packet-size-in-voip--54ac>

正如您在研究中可能观察到的，有一种确定的方法可以计算 VoIP 数据包的大小。数据包的大小取决于许多不同的变量，因此对于“平均”数据包大小没有很好的答案——平均大小取决于环境。举个例子，如果您目前在局域网内运行 VoIP，并希望提供一个新的广域网，这样您就可以在另一个站点使用 VoIP，了解局域网上的 VoIP 数据包有多大是没有帮助的。下面是一个典型局域网的 VoIP 数据包大小的计算，这将帮助您入门。

### 数据包大小

VoIP 数据包大小的一般公式是这样的

```
Frame overhead + Encapsulation overhead + IP overhead + Voice payload. 
```

Enter fullscreen mode Exit fullscreen mode

假设数据包通过我们的局域网，那么现在以太网 II 的帧开销是 18 字节。(如果数据包通过带有 802.1Q 标记或 ISL 封装的中继，或者目的地是 WAN(可能会使用不同的链路层帧),则该大小稍后会发生变化。)

### 封装

封装开销包括诸如 IPSec 隧道之类的安全措施。假设我们没有封装这个语音包，所以这里没有开销。

“IP 开销”的开销发生在第 3 层及以上，因此对于 SIP 电话，这意味着 IP (20 字节)、UDP (8 字节)和 RTP (12 字节)。这总共是 40 字节的 IP 开销。

最后，您必须计算实际语音负载的大小。假设我们使用 G.711 编解码器，它为我们提供了 64kbps 的编解码器带宽。还假设我们的电话具有 20 毫秒的分组化周期(意味着每个分组中包含 20 毫秒的语音)。有了这两个数字，我们就可以算出语音有效载荷的大小。因为一秒钟的声音包含 64 千比特的数据(“64 kbps”)，所以很容易计算出有多少比特

找出每个有效负载的字节数:

```
64000 bits * .02 seconds = 1280 bits of voice per payload  
1280 bits / 8 bits per byte = 160 Bytes of voice per payload 
```

Enter fullscreen mode Exit fullscreen mode

总开销为 58 字节(18 + 40)

总 VoIP 数据包大小为 218 字节(160 + 58)

为了完全公开起见，从这里很容易得到每秒的比特率；只需将 218 个字节转换为比特，然后乘以分组速率(这是分组周期的倒数，在本例中为每秒 50 个分组)。该语音的一个流的比特率是 87.2kbps...我们希望用户不只是在自言自语，所以在真正的电话交谈中要加倍努力。

还有许多其他的小事情，比如 VAD 和各种报头压缩，您可能也需要考虑到这些计算中。正如你所看到的，这些事情中的任何一个出了问题都会给你一个不同的答案，所以知道如何进行整个过程是很重要的。

## 引用

1.  [www.techexams.net](//www.techexams.net)

* * *

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://www.producthunt.com/posts/push-hero-2)
*   [快速访问-在 Mac 菜单栏中组织文件](https://www.producthunt.com/posts/quick-access)
*   [帧记录器-带帧记录器 gif 和视频](https://www.producthunt.com/posts/frame-recorder)
*   [其他应用](https://onmyway133.github.io/projects/)

❤️❤️😇😍🤘❤️❤️