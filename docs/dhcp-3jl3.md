# 动态主机配置协议

> 原文：<https://dev.to/gabeguz/dhcp-3jl3>

曾几何时，如果你想连接到互联网(甚至你的局域网)，你需要编辑一堆文件，向你的系统或网络管理员要一个 IP 地址，并希望你没有把你的电脑搞得太糟，因为你不知道你在做什么。今天，你只需插上以太网线，或登录到你的 wifi 网络，就可以开始工作了。这是怎么回事？

# 假设

*   网线是好的
*   端口处于活动状态(插入另一端的交换机或集线器)
*   网络上的某个地方有一个 DHCP 服务器

 *# 预赛

好了，你已经插上了电缆，你看到一些闪烁的灯，这是一个好迹象。这意味着物理接口(网卡)通过网络接收信息，也可能通过网络发送信息。此时你将无法访问互联网上的主机，因为你的电脑没有 IP 地址。您的 TCP/IP 网络信息目前如下所示:

```
|---------------|
|  My Computer  |
|---------------|
|IP: 0.0.0.0    |
|Router: 0.0.0.0|
|DNS: 0.0.0.0   |
|---------------| 
```

Enter fullscreen mode Exit fullscreen mode

没有 IP(互联网协议)地址-IP 地址是你的电脑在互联网上的地址。这就是互联网上的其他主机如何知道在哪里找到你。当你浏览到[https://duckduckgo.com](https://duckduckgo.com)时，你发送你的 IP 地址作为“返回地址”,以便 duckduckgo 服务器知道向哪里发送回复。

没有路由器地址——路由器只是一台被配置为将数据包从一个网络传输到另一个网络的计算机。你本地网络上的所有机器都可以不通过路由器互相对话，只要你知道它们的 IP 地址，你就可以直接给它们发送数据包。如果你想到达另一个网络，你需要通过一个知道这两个网络的路由器发送你的包。通常，路由器通过两块以太网卡连接到至少两个网络中，并且可以将数据包从一块网卡传递到另一块网卡。

没有 DNS 服务器地址——我们在之前的帖子中已经讨论过 [DNS](https://dev.to/gabeguz/whats-dns-13c3) ,但是基本上 DNS 就是你的地址簿。你请求 https://duckduckgo.com/的，DNS 服务器负责在它的地址簿中查找这个名字(duckduckgo.com)，并告诉你这个名字的 IP 地址是:107.20.240.232(答应我，查一下)。

# 没有 IP，现在怎么办？

没有 IP 地址你能做什么？我们还不能到达互联网上的其他主机，但是我们可以和我们自己网络上的其他主机对话。没有 IP 地址意味着我们不能告诉别人在哪里能找到我们，但我们可以对着黑暗大喊，希望有人在听。这基本上就是您的电脑首次接入网络时所做的事情(假设您有一个正在运行的 DHCP 客户端)。

```
you> HELLO?
you> HELLOOOOOOOOOOOOOOO!
dhcpserver> HEY!
dhcpserver> STOP YELLING.  Use the phone, call me here: 192.168.1.2
dhcpserver> And, here's a number you can use: 192.168.1.115
dhcpserver> Also, this is the number for the router, in case you need to get on another network: 192.168.1.1
dhcpserver> make sure you let me know you're going to use this.
you> oh, hey, thanks!  I'll remember that my number is 192.168.1.115 and yours is 192.168.1.2\.  
dhcpserver> Great, oops, almost forgot, here is the DNS server you can use to lookup other computers: 192.168.1.3\.  See you around! 
```

Enter fullscreen mode Exit fullscreen mode

您的计算机(通过其 dhclient 程序)向网络广播地址(网络上任何人都可以接收消息的特殊地址)发送一条名为`DHCPDISCOVER`的消息。您的计算机提前知道这个地址，在 TCP/IP 网络上是 255.255.255.255。向此地址发送数据包会将它们发送到本地网络中的所有主机。路由器不会将广播消息转发到其他网络。如果有一个 DHCP 服务器在监听，它将回复一个`DHCPOFFER`消息，其中包含您将自己配置为本地 TCP/IP 网络的一部分所需的所有信息。由于您还没有配置 IP 地址，DHCP 服务器也将使用广播消息向您发送`DHCPOFFER`。然后你的电脑会发回一条`DHCPREQUEST`消息，要求 DHCP 服务器为你保留它刚刚告诉你的那个号码。如果一切顺利，DHCP 服务器将用一个`DHCPACK`来响应它所拥有的任何附加信息(比如 DNS 服务器地址)。此消息让您知道可以开始在网络上正式使用该 IP 地址。

现在，假设一切顺利，您的网络配置应该如下所示:

```
|--------------------|
|     My Computer    |
|--------------------|
|IP: 192.168.1.115   |
|Router: 192.168.1.1 |
|DNS: 192.168.1.3    |
|--------------------| 
```

Enter fullscreen mode Exit fullscreen mode

您可以在大多数类似 unix 的系统上使用`ifconfig`命令来确认您当前的 ip 地址:

```
$ ifconfig iwm0
iwm0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
        lladdr e8:2a:ea:ab:49:24
        index 1 priority 4 llprio 3
        groups: wlan egress
        media: IEEE802.11 autoselect (HT-MCS14 mode 11n)
        status: active
        ieee80211: nwid Odessa chan 11 bssid ac:f1:df:62:d6:9a 70% wpakey wpaprotos wpa2 wpaakms psk wpaciphers ccmp wpagroupcipher ccmp
        inet 192.168.0.115 netmask 0xffffff00 broadcast 192.168.1.255 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`route`命令:
来确认您的路由器地址

```
$ route show
Routing tables

Internet:
Destination        Gateway            Flags   Refs      Use   Mtu  Prio Iface
default            192.168.1.1        UGS        9      860     -    12 iwm0 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过查看/etc/resolv.conf
的内容来确认您的 DNS 服务器

```
$ cat /etc/resolv.conf
nameserver 192.168.1.3
lookup file bind 
```

Enter fullscreen mode Exit fullscreen mode

# 提问

恭喜你，你上互联网了！需要思考的几个问题:

1.  如果您决定在笔记本电脑上运行 DHCP 服务器，并开始在您的网络上传递 IP 地址，会发生什么情况？

2.  在你有一个 IP 地址之前你还能做什么？

3.  在配置 TCP/IP 网络之前，什么允许您连接到网络上的其他计算机？*