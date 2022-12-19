# 网络基础:DNS

> 原文：<https://dev.to/swyx/networking-essentials-dns-1dl7>

*这是我参加[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的一系列课堂笔记中的第五篇。*

## 域名系统

DNS 的目的是将 IP 地址映射到人类可读的名称:

*   客户想要查找一个域名
*   客户端的存根解析器获取名称并发出查询。解析程序可能缓存了该名称。
*   如果不在缓存中，查询将被发送到本地 DNS 解析器(通常在您的机器被分配 IP 地址时配置，使用[域主机控制协议](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)
*   如果第一个本地 DNS 解析器在预设的超时时间内没有响应，它将尝试第二个。
*   查询是**递归的**，因此重定向和引用会产生进一步的查询，而客户端只需要最终结果。
*   但是本地解析器迭代地工作**，只回答它们前面的特定查询。**

 **因此，查询/解析序列可能是:

*   请求:`A "www.gatech.edu"`(到根服务器)
*   回应:`NS "k.edu.servers.net" (` NS `记录为转介)
*   请求:`A "www.gatech.edu"`(到`.edu`服务器)
*   响应:` NS "dns1.gatech.edu "
*   请求:`A "www.gatech.edu"`(到`gatech.edu`服务器)
*   回应:`A 130.207.160.173`(成功！)

由于所有的往返，这个过程相当慢，所以本地解析器为特定的 TTL(生存时间)保存所有的`A`和`NS`映射的缓存。您可能还想存储更频繁访问的域名，如根域名或`google.com`数天或数周，但是像`www.gatech.edu`这样的本地域名可能会更频繁地更改，因此应该有更短的 TTL。

## 记录类型

上面那些`A`和`NS`符号是什么？它们是指示回应中权限级别的记录类型:

*   `A`记录将名称映射到 IP 地址
*   `NS`(又名推荐)记录将域名映射到权威域名服务器

说白了，你问根服务器具体名字，它很可能不具体知道 IP，但是它会知道谁知道，告诉你去问那个“权威名称服务器”。依此类推，直到找到知道你要找的域名的确切 IP 地址的最后一个域名服务器。这使得域名系统可以作为一个层次来实现。

其他记录类型:

*   `MX`将名称映射到邮件服务器
*   `CNAME`为需要查找的另一个域名设置规范名称或别名
*   `PT R`将 IP 地址映射到域名(反向查找)
*   `AAAA`将名称映射到 IPv6 地址

## 自己试试吧！

您可以在您的终端中运行您自己的跟踪！试试`dig www.gatech.edu` :

```
; <<>> DiG 9.8.3-P1 <<>> www.gatech.edu
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 40374
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.gatech.edu.            IN  A

;; ANSWER SECTION:
www.gatech.edu.     59  IN  CNAME   tlweb.gtm.gatech.edu.
tlweb.gtm.gatech.edu.   29  IN  A   130.207.160.173

;; Query time: 267 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed Sep 26 00:01:01 2018
;; MSG SIZE  rcvd: 72 
```

Enter fullscreen mode Exit fullscreen mode

问题部分显示了我们对`www.gatech.edu`的 A 记录查询。

答案部分显示了用 59 秒 TTL 将`www.gatech.edu`替换为`tlweb.gtm.gatech.edu`的 CNAME 的答案。

因此，我们发出另一个对`tlweb.gtm.gatech.edu`的请求，这一次得到的是`130.207.160.173`。

## 负载均衡示例

试试`dig nytimes.com` :

```
; <<>> DiG 9.8.3-P1 <<>> nytimes.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 23334
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;nytimes.com.           IN  A

;; ANSWER SECTION:
nytimes.com.        319 IN  A   151.101.193.164
nytimes.com.        319 IN  A   151.101.1.164
nytimes.com.        319 IN  A   151.101.129.164
nytimes.com.        319 IN  A   151.101.65.164

;; Query time: 128 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed Sep 26 00:03:49 2018
;; MSG SIZE  rcvd: 93 
```

Enter fullscreen mode Exit fullscreen mode

答案部分中的 4 个并行地址都是相同的，但是如果`151.101.193.164`过载，下一个响应将把它换成它的一个兄弟。

## 反向查找示例

如果你试图查找一个 IP 地址呢？

`dig -x 130.207.7.36` :

```
; <<>> DiG 9.8.3-P1 <<>> -x 130.207.7.36
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 3657
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;36.7.207.130.in-addr.arpa. IN  PTR

;; ANSWER SECTION:
36.7.207.130.in-addr.arpa. 299  IN  PTR granite.cc.gatech.edu.

;; Query time: 449 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed Sep 26 00:09:13 2018
;; MSG SIZE  rcvd: 78 
```

Enter fullscreen mode Exit fullscreen mode

您得到的`PTR`记录类型将您指向人类可读的领域。请注意，当 IP 地址在域名层次结构中从较高位置移动到较低位置时，会出现颠倒的 IP 八位位组。

## 我们系列的下一个

希望这是一个很好的域名系统的高层次概述，你可以 ping 你自己的域名，看看记录保存在哪里。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

*   [架构和原理](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)
*   [切换](https://dev.to/swyx/networking-essentials-switching-3eba)
*   [路由](https://dev.to/swyx/networking-essentials-routing-5gb7/)
*   [命名/寻址/转发](https://dev.to/swyx/networking-essentials-naming-addressing-and-forwarding-13kk)
*   [DNS](https://dev.to/swyx/networking-essentials-dns-1dl7)
*   [拥塞控制和流式传输](https://dev.to/swyx/networking-essentials-congestion-control-26n2)
*   [速率限制和流量整形](https://dev.to/swyx/networking-essentials-rate-limiting-and-traffic-shaping-43ii)
*   [内容分发](https://dev.to/swyx/networking-essentials-content-distribution-jag)
*   [软件定义网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)
*   [交通工程](https://dev.to/swyx/networking-essentials-traffic-engineering-13c4)
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)**