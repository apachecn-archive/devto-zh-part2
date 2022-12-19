# 2018 年通过 DNS 隧道传输 TCP

> 原文：<https://dev.to/booyaa/tunnelling-tcp-over-dns-in-2018-jkp>

我写这篇文章是因为看到自 2016 年以来没有人写过任何关于通过 dns 隧道传输 tcp 流量的文章。

这种类型的隧道的一个常见用途是通过 WiFi 强制门户隧道获得免费互联网接入。

我还想说，在你的 ISP 或政府实施类似的网络限制之前，进行一些实践可能是个好主意。

就像有状态的数据包检测会发现通过 https 的 ssh 隧道一样，这绝不是一种隐藏的方法，所以“买者自负”!

我选择的掘进工具是**碘酒**。代码可以在 [GitHub](https://github.com/yarrick/iodine) 上找到，网站托管在 [code.kryo.se](http://code.kryo.se/iodine/) 上。

当前版本(0.7.0)的设置没有改变。这里需要注意的重要一点是，您应该始终与碘的客户端和服务器版本保持一致。

## 设置碘:

### 配置服务器(通过 DigitalOcean 等)。

*   安装碘(ubuntu/debian) `apt-get install iodine`
*   作为服务运行`iodined -f -c -P f00b44 10.0.0.1 t1.yourdomain.tld`

注 1: f00b44 是您在客户机和服务器之间共享的秘密

注 2:就像使用 vpn 一样，您为自己的隧道创建一个专用网络，在我们的例子中，我们使用

注 3:如果你觉得特别慷慨，这是我的数字海洋推荐链接。

### 创建指向你的服务器的 DNS 记录

```
t1ns A your_vps_ip
t1 NS t1ns.yourdomain.tld 
```

提示:我使用 cloudflare 来管理我的 dns，这意味着我的 DNS 记录在互联网上传播得相当快。

### 安装客户端(mac OS)

```
git clone https://github.com/yarrick/iodine.git
ls
cd iodine/
ls make
make
PREFIX=/usr/local make install 
```

### 连接客户端到服务器

`sudo /usr/local/sbin/iodine -d utunX -f -P f00b44 t1.yourdomain.tld`

注意:你需要 sudo 来使用隧道网络适配器。

### 设置验证

为了验证设置，我们将设置一个 ssh 作为 SOCKS 代理`ssh -D 9999 10.0.0.1`。

注意:我们正在通过服务器的 ip 地址设置 SOCKS 代理。

然后用 httpbin 验证我们的 ip 地址`curl --socks5-hostname 127.0.0.1:9999 -L http://httpbin.org/ip`

当隧道传输预计会有相当大的带宽损失时，我只推荐在这种类型的隧道传输上使用 ssh，它太慢了，不能做任何其他有用的事情。

随机提示:`<enter> ~.`逃避挂起的 ssh 会话