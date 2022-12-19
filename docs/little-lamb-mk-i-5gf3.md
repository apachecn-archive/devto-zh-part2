# 小羊羔 Mk I

> 原文：<https://dev.to/commonshost/little-lamb-mk-i-5gf3>

这个小小的 CDN 边缘服务器是开放硬件，运行完全的 FOSS 栈，价格非常实惠。这些特性使其成为许多部署 CDN 接入点(pop)的关键推动者。尤其是在没有传统数据中心的地区。目标是在延迟方面比任何其他 CDN 更接近全球人口。

[![Little Lamb Mk I](../Images/0d878a487185d2b28c3fd2b5a77715f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8vs7tU5s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpeqgctpec1ply0arsc9.jpg)

Mk I 硬件由一个带有 250 GB 三星 850 EVO SSD 的 [HardKernel Odroid HC1](http://www.hardkernel.com/main/products/prdt_info.php?g_code=G150229074080) 组成。其高能效的 8 核 ARM CPU 在基准测试中为[提供超过 800 Mbps 的服务](https://twitter.com/sebdeckers/status/987151171251945473)。由于 6 Gbps SATA 驱动器受到 1 Gbps 网络接口的瓶颈限制，我正在考虑通过使用更便宜的 SSD 来保持类似的 IOPS，从而优化成本。

Mk I 车型[现已发售](https://carousell.com/p/commons-host-cdn-pop-edge-server-166149743/)。自动化部署和仪表板集成即将推出。

定价目前计算如下:

1.  材料清单
2.  +10%来源运输和处理(S&H)
3.  +7%的商品及服务税
4.  +33%的安装费用

因此，通过构建自己的服务器，您可以节省 25%的成本。从这个角度来看，硬件初创公司的经验法则是收取 200%的加价以保持盈利。因此，很明显，在通常利润的一小部分，我没有开始硬件业务。共享主机是一种网络业务。通过保持较低的硬件利润和免费的软件，将会部署更多的服务器。[梅特卡夫定律](https://en.wikipedia.org/wiki/Metcalfe's_law)预测网络的价值应该呈指数级增长。