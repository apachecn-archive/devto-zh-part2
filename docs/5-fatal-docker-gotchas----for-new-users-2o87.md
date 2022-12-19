# 5 个致命的码头工人陷阱😱-针对新用户

> 原文：<https://dev.to/uilicious/5-fatal-docker-gotchas----for-new-users-2o87>

用 [docker 容器](https://www.docker.com/what-container)开发很棒！我们在 [uilicious](https://uilicious.com/) 在 docker 上运行我们的整个后端。

> 如果没有 docker，我们不可能以目前的成本和规模推出 uilicious。

然而，与所有技术一样，有一些隐藏的、不太明显的问题，尤其是当跨多个主机大规模运行 Docker 时。尤其是对于那些从物理服务器或虚拟机工作负载迁移的客户。

需要明确的是，本文不是关于系统优化的。而是关于常见的错误配置设置，这些设置会给你和你的公司带来灭顶之灾(YMMV)；在这种情况下，您的生产系统可能会遭受长时间的停机，或者更糟，数据泄露给黑客。

本文还假设您对本地主机有一点基本的 docker 知识。

因此，这里有 5 个致命的陷阱，是我在过去 4 年中使用 Docker 时不幸经历并幸存下来的，以及在每一点上的快速总结。以及如何避免它们。

> 注意，当我提到涉及多个主机的情况时，这只适用于比单个主机更大的 docker 设置，如 [rancher 1.6](https://rancher.com/) (我们内部使用的)、 [Docker swarm](https://docs.docker.com/engine/swarm/) 或 [Kubernetes](https://kubernetes.io/) 等。

* * *

<figure>

[![A literal wall on fire](../Images/ba8d72f6d0f1353aaced696db9cdcd21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_2ZYYOsp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s7rdghmzbwl79zakdlvn.jpg)

<figcaption>Its probably too late if this happens</figcaption>

</figure>

## 1) Docker 与防火墙不兼容

**Docker 主机端口绑定，绕过典型的 iptables 输入规则**

遗憾的是，许多人会本能地开始使用 Docker，将容器端口绑定到主机上，或者镜像他们在传统 linux 中的常用设置(认为他们在主机上常用的 iptables 防火墙规则不需要验证就可以工作)。或者遵循许多在线初学者指南，这些指南倾向于将端口绑定到本地机器上的“主机”以便于开发(在这个问题上没有任何建议)。

但是， [Docker 端口绑定默认使用 iptables 预路由级别](https://github.com/moby/moby/issues/16329)。它优先于大多数 iptables 输入防火墙规则，许多人通常使用这些规则来保护他们的系统。

最终结果是:像 MySql 这样的特权端口被公开暴露在互联网上。更糟糕的是，NoSql 端口通常在没有身份验证的情况下部署，导致所有私人客户数据暴露给黑客。

**缓解指南**

> *   Mitigation by using container-to-container communication
> *   Mitigation through the use of network-based rules
>     *   AWS、Azure、DO 或谷歌云的 VPC/防火墙规则
>     *   Local router rules
>     *   Not applicable to Linode
> *   Alternative last resort
>     *   Review, manage and protect all host-bound authenticated ports.
>     *   Use custom docker iptables

幸运的是，通过在 Google 或 AWS 等提供商提供的虚拟私有云(VPC)内应用基于网络的防火墙规则，可以轻松缓解这些情况。或者，在本地路由器上应用类似的规则，将服务器安全地暴露给公众。

然而，对于一些云提供商来说，这种解决方案不是一个选项，最明显的是 Linode，其中专用网络简单地指的是让所有客户在其各自的本地数据中心内进行公共访问。

在这种情况下，替代地；您可以仔细检查公开绑定的端口，并确保它们使用复杂的随机生成的密码进行适当保护，并且所有非公开通信都发生在容器到容器的网络之间，甚至实现双向 SSL 身份验证。或者学习建立 docker iptables 规则的复杂过程。

**附加细节**

*   [码头官方文件](https://docs.docker.com/engine/userguide/networking/default_network/binding/)
*   [坞站 iptables guide](https://docs.docker.com/network/iptables/)
*   [数字海洋深入 iptables](https://www.digitalocean.com/community/tutorials/a-deep-dive-into-iptables-and-netfilter-architecture)
*   [Docker 和 iptables](https://fralef.me/docker-and-iptables.html)

* * *

<figure>

[![Smashed harddrives](../Images/a135eb8e450b028a5d9ae7175bc99a27.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_YkvOllo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u1zoaaqivp4iei6cnehg.jpg)

<figcaption>You do have backup right?</figcaption>

</figure>

## 2)不了解 docker 数据持久性

**Docker 默认不持久化任何容器数据！**

首先，这是一个极其复杂的话题，我只是想把它简单化。

这里有一个快速速成班，介绍如何在容器中持久地处理数据。尤其是当容器重新启动(停止-启动)或重新部署到另一个主机时。

> 1.  Container, no volume, local temporary storage changes. If the container is redeployed, the data will be destroyed.
> 2.  By default, the volume only persists on the host and is not transferred across hosts.
> 3.  When the container is closed or the health check fails, any of the following will happen.
>     *   Restart, container data and volume persistence
>     *   Redeploy on the same host, the volume is persisted, and the container data is destroyed.
>     *   **Redeployed on a different host, no data persistence! ! !**
>         *   This may happen in an unobvious way, such as a temporary failure of the host.
>         *   The infrastructure tool just decided to do this on Friday.
>         *   Orphaned volumes may be deleted by the cleanup function.
> 4.  However, the host storage can be directly mounted as a volume. ...

那么这些是什么意思呢？以下是一些真实发生的故障场景，并通过备份进行了补救。

**具有一个容量的单个容器，没有主机限制**

> 您已经部署了一个带有卷的 MySql 容器，如果失败，默认情况下它会重新启动。一天，运行 mysql 的主机与 docker 集群断开连接。它的集群管理器将容器重新部署到另一台主机上，连接到一个新卷。
> 
> 更糟糕的是，有一个清除脚本用于删除悬挂的卷和容器。使得数据在你醒来的时候不可恢复。

**主机受限容器，无卷装载**

> 您已经指定了一个 Jenkins 容器专门部署在一台主机上，并确保其失败条件是什么也不做。这将减少“在不同主机上重建”的情况，耶！
> 
> 然而，在一个阳光明媚的星期天，您注意到 Jenkins 需要一个安全更新。您决定单击升级按钮。噗，当你的容器被重新部署时，你所有的数据都不见了。

**分布式冗余系统，没有主机限制**

> 您已经在多个主机上部署了 ElasticSearch，并使用了它们各自的卷容器；相信即使它们单独失败，ElasticSearch 内部的魔力也会重新平衡并从其其他副本中恢复数据。有一天，您决定进行升级，但是您没有意识到您在集群中运行的主机比所需的容器多得多。
> 
> 在升级过程中，所有新版本都被部署到以前未使用的主机上，旧容器被终止。没有足够的副本，也没有时间传输数据，因为容器启动和停止动作是即时的。
> 
> 所有数据都丢失了。如果您正在使用自动缩放组，这可能会悄悄发生。

**缓解指南**

为了缓解上述情况，应采取以下措施。

> *   **have backups** and learn how to restore them.
> *   Ideally throw your persistent data out of your docker infrastructure.
>     *   Use cloud hosted SQL, or various available cloud hosted objects/non-SQL services.
>     *   Or use EBS equivalent to google Persistent Disk/aws directly in your docker container (if supported)
> *   As far as possible, containers should store zero data that needs persistence.
>     *   Examples (zero persistence): front-end server, application server
>     *   Counterexamples (persistence required): SQL server, file server
> *   For containers that hold persistent data
>     *   Pay attention to the health check and its fault behavior carefully.
>     *   Plan your upgrade carefully and upgrade one container at a time if necessary.
>     *   You can configure the health check to do nothing.

**附加细节**

*   Docker 官方文件[这里](https://docs.docker.com/storage/volumes/)，还有[这里](https://docs.docker.com/storage/#good-use-cases-for-bind-mounts)
*   [为 docker 管理持久性](https://thenewstack.io/methods-dealing-container-storage/)

* * *

<figure>

[![Fast moving vehicles](../Images/5b3b794055716b99e038140bd166a09c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xYF288Al--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/79xpynf094036u7uhwva.jpg)

<figcaption>Life on the npm/node.js fastlane is great... until the endless dependency wheels starts breaking apart...</figcaption>

</figure>

## 3)使用:容器的最新标签

**不受管理的自动升级会破坏您的系统。**

一种常见的模式是使用一个[基本容器映像](https://hub.docker.com/r/picoded/ubuntu-base/)，您可以将它合并为常用的容器模块，并从其扩展为更全面的容器映像。

或者，可以使用最新的官方容器映像进行部署，如 [Jenkins](https://hub.docker.com/r/jenkins/jenkins/) 等。

但是，在服务器出现故障及其后续自动伸缩替换的情况下，最新的升级依赖项可能会与集群的其余部分不同步。

> 此外，有时最新版本会对您的系统进行逆向更改，这会导致长时间停机。当某些系统只能单向升级，并且会在升级过程中修改共享文件或资源时，情况只会变得更糟(例如:Jenkins、GitLab)。

这种情况会使跟踪 bug 变得复杂。您可能只在一台机器上遇到这个问题，而在另一台机器上却不会。其中它不能被回收，直到做出改变，例如指示使用固定版本来代替所使用的容器模板中的最新标签。

在生产环境中，您肯定希望慢慢来，使用固定版本，或者至少使用主要版本/稳定分支。然而对于测试来说，如果你觉得需要速度的话，你可以在`:latest`上尽情发挥。

欲了解更多言论，请点击查看[。](https://derickbailey.com/2017/05/10/never-use-the-latest-image-from-docker-hub/)

**缓解指南**

> *   Just don't use `:latest` and adjust the rest according to your requirements.
> *   Or, just use your own container registry and manually select all the images deployed on it-this is a slow and painful way to do it in the bank.

* * *

<figure>

[![Door to unit number 404](../Images/7f6ee53ce4f58d6411c460dd6eae995c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OByXBDuw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w4cl81vjnodi8e9zeb8b.png)

<figcaption>Im not too sure if thats the right address</figcaption>

</figure>

## 4)容器没有永久 IP 地址

**假设容器的永久 IP 地址。**

假设 IP 地址的持久性会产生复杂的问题，通常在 nginx 负载均衡器上。更糟糕的是，在配置文件中硬编码 IP 地址。这可能会产生令人困惑的错误，即每个容器都“在线”并且看起来很好，但是它们不能相互通信。

> 例如，如果您使用 Nginx 作为多个容器的负载平衡器，默认情况下，域名的 IP 地址在启动时被解析。这意味着，如果 Nginx 负载均衡器连接的任何容器的 IP 地址被修改，负载均衡将不会更新和失败。(除非你用的是 Nginx Pro)

**缓解指南**

> kubernetes:学会使用集群 IP，它是针对这个问题的游戏规则改变者。*   Configure your stack to use the hostname on a fixed ip address.
>     *   This assumes that the application supports dynamic IP on the hostname.
>     *   Use alternative methods to Nginx, such as HAProxy or Traefik, for load balancing,
>     *   If this is not possible, use health check to automatically restart the container to repair the hostname route.

**附加细节**

*   [Docker 文档](https://docs.docker.com/engine/userguide/networking/)
*   [Nginx 文档-缺乏动态 ip 支持](https://tenzer.dk/nginx-with-dynamic-upstreams/)

* * *

<figure>

[![Learning the ABC's](../Images/6e85772ff994078c37cda957730366f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uVf_Qixn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rmc1pl20bwtzjjlyutzv.jpg)

<figcaption>Everything a new hypershiny tech comes along, everyone seems to need to relearn the ABC's</figcaption>

</figure>

## 5)缺乏对 CPU、RAM、磁盘的规划和监控

**Docker 只是一个抽象层，基本原理没有改变**

这尤其令人恼火，因为在大多数云提供商的默认情况下，随机存取内存(RAM)和磁盘存储的使用情况不会显示在实例仪表板上。

如果计划不周，单个主机故障会导致“资源不足”错误的连锁反应，迫使群集将受影响的容器重新定位到其他主机中。这又会触发更多的“资源不足”错误。

> 这种情况的一个常见例子是在部署多个容器(如 SQL、NoSql 或文件服务器)的过程中。默认情况下，随着使用量的增加，它会尝试消耗尽可能多的 ram 来提高性能。

另一件通常被遗忘的事情是:熵和对数旋转

如果不这样做，如果您使用的是认证系统或 SSL，熵将会在某个时候使事情变慢甚至停止。或者，日志轮换有利于确保您的服务器不会因为日志而耗尽文件空间。

最后，备份，备份，备份。

<figure>

[![Kibana dashboards](../Images/f8b99a0a9b2ec016b09f0a942d346705.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2O6wv6ut--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8l0gvhnseuuzchr5git0.jpg)

<figcaption>A good dashboard can go a long way in keeping your systems operational</figcaption>

</figure>

**缓解指南**

> 适当地计划你的 RAM 和磁盘使用需求，记住要有额外的缓冲空间以防可能发生的开销。*   Limit the RAM of a single container.
>     *   Use this to limit the damage caused by "out of control" errors, from one container to others on the same host.*   Monitor your usage requirements through the dashboard or manually from time to time.
>     *   Elastic searches for your metrics (free, but cumbersome to set up)
>     *   Datadog your indicators (not free, but many things are ready-made)*   Clean up the floating containers/images/volumes, which will free up unused space.*   [Essentials of deploying servers]
>     *   [Entropy Generation] (especially for cloud servers)- [container](https://hub.docker.com/r/picoded/haveged/)
>     *   Rotation- [container](https://hub.docker.com/r/picoded/docker-logrotate/)

**(以防万一):从永久故障状态恢复集群**

> 请注意，当这种级联的“资源不足”错误发生时，最安全的解决方案是
> 
> *   停止/关闭整个集群
> *   停止所有计划的容器
> *   一组一组的反应容器
> 
> 通过这种方式，集群不会在任何给定的时间点被淹没。

* * *

[![Website end-to-end test monitoring via uilicious.com](../Images/f8c8f7bba73d37db3e38df184b68626c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cQ_oVIpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncy2o4m20odrxjocx5un.gif)

## 还有一点:监控不仅仅是关于 http 代码 200

监控也适用于功能。毕竟，静态文件服务器有可能成功启动并运行。而一个损坏的 JS 文件，将导致所有浏览器功能的中断。

虽然您可以通过内置在 api 中的复杂运行状况检查命令或基于文本的静态网站运行状况检查来实现这一点。

您可以简单地使用 uilicious 来为您进行作业监控。在这里，您可以快速构建简单的测试用例，对网站进行完整的端到端测试，并在后台安排测试运行。当测试用例失败时，我们会自动通知你。

```
// Test scripts can be as simple as the following
I.goTo('https://google.com')
I.fill("Search", "uilicious")
I.pressEnter()
I.click("Smart User Journey Testing")
I.amAt("https://uilicious.com") 
```

Enter fullscreen mode Exit fullscreen mode

*   [您可以在我们的公共测试平台上尝试上述测试片段(无需登录)](https://snippet.uilicious.com/test/public/JBCw9VuztzCwowYwLHvFFz)
*   [或注册免费试用](https://user.uilicious.com/signup)。

* * *

# 结尾注释

希望以上能帮助你避免痛苦的经历。然而，与所有快速发展的新技术一样，对一切都要持保留态度。(PS:这是 2018 年 11 月更新的)

*   Docker 场景和工具瞬息万变，继续学习，四处阅读。
*   没有什么是魔法，都是工具，学着点。
*   不要盲目相信默认，和教程指南，学习他们做什么。
*   了解您的系统在各种可能的情况下会如何失败，并针对这些情况制定计划。

最重要的是——有一个测试环境，在那里进行实验，在这个过程中你可以安全地失败。

* * *

## 快乐航运🖖🏼🚀