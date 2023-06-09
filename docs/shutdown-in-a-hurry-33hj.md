# 匆忙关机

> 原文：<https://dev.to/ferricoxide/shutdown-in-a-hurry-33hj>

去年，我为一个客户完成了一个 CI 工具的自动化部署。客户使用它为公司内不同的开发团队提供 CI 服务。每个开发人员组就像我的客户的 CI 服务的一个独立租户。

最近，随着越来越多的开发团队开始使用这项服务，空间消耗出现了爆炸式增长。最初，他们尝试在其 CI 系统中设置作业，以自动清理一些租户的架构较差的 CI 作业，这些作业在完成后无法进行适当的清理。不幸的是，一旦 CI 系统的文件系统填满，作业就会停止工作...包括清理工作。

当我编写初始自动化时，我编写了自动化来为他们的 CI 系统创建两种不同的最终状态:一种基本上只是 CI 服务的标准独立部署的云托管版本；另一个是支持云的版本，旨在定期或在服务失败时自动重建自身。他们选择部署前者，因为这是他们最熟悉的部署类型。

由于他们的租户经常导致服务组件离线，他们的清洁工作不可靠，他们要求我调查一些事情，并提出一个解决方案。我向他们介绍了我提供给他们的原始自动重建工具集，并指出，只要稍加修改，这些工具就可以检测到文件系统已满状态并启动紧急操作。在这种情况下，建议的紧急措施是系统自杀，导致自动化将服务重建回健康状态。

最初，我打算修补自动化，以便在检测到磁盘已满状态时，触发正常关闭。然后我突然想到，“我不关心这些系统的完整性，我关心的是它们尽快离开*，*”因为它们离开得越快，自动化就会越快注意到并开始采取措施重建服务。我想要的不是一种`init 6`式的关机，而是一种“把电源线从墙上拔下来”式的关机。

在 Linux 之前的日子里，我处理过的商业 UNIX 系统都有强制系统立即停止运行的方法。不要错过。不要收取 200 美元。所以，开始四处寻找类似物。

在许多 Linux 发行版——包括部署 CI 服务的发行版——迁移到 systemd 之前，您可以通过执行以下操作来暂停系统:

> ```
> # kill -SEGV 1 
> ```

然而，在 systemd 下，上述操作将导致 systemd 转储一个核心文件，但不会暂停系统。相反，systemd 会立即恢复:

> ```
> Broadcast message from systemd-journald@test02.lab (Tue 2018-11-06 18:35:52 UTC):
> 
> systemd[1]: Caught , dumped core as pid 23769.
> 
> Broadcast message from systemd-journald@test02.lab (Tue 2018-11-06 18:35:52 UTC):
> 
> systemd[1]: Freezing execution.
> 
> Message from syslogd@test02 at Nov  6 18:35:52 ...
>  systemd:Caught , dumped core as pid 23769.
> 
> Message from syslogd@test02 at Nov  6 18:35:52 ...
>  systemd:Freezing execution.
> 
> [root@test02 ~]# who -r
>          run-level 5  2018-11-06 00:16 
> ```

所以，我做了更多的调查。在这样做的过程中，我发现 Linux 有一个功能类似于在 80 或 90 年代的系统上[按下`SysRq`键](https://www.kernel.org/doc/html/v4.11/admin-guide/sysrq.html)。这是 Enterprise Linux 中启用的一项可选功能。对于过去用`SysRq`键可以做的事情的列表，我会向您推荐这篇“ [*神奇的 SysRq 键*](https://en.wikipedia.org/wiki/Magic_SysRq_key) ”文章。

所以，我做了以下测试:

> ```
> # echo o >/proc/sysrq-trigger 
> ```

这几乎会立即导致到远程系统的 SSH 连接“挂起”。与其说连接已经挂起，不如说执行上述操作会导致远程系统*立即*停止。这可能需要几秒钟的时间，但是最终 SSH 客户端会判定端点已经断开，导致“挂起”的 SSH 会话退出，类似于:

> ```
> # Connection reset by 10.6.142.20 
> ```

这一点知识验证，我有我的自动自杀方法。基本上，我设置了一个每五分钟运行一次的`cron`任务来测试`${APPLICATION_HOME}:`的“满度”

> ```
> #!/bin/bash
> #
> ##################################################
> PROGNAME="$(basename ${0})"
> SHELL=/bin/bash
> PATH=/sbin:/bin:/usr/sbin:/usr/bin
> APPDIR=$(awk -F= '/APP_WORKDIR/{print $2}' /etc/cfn/Deployment.envs)
> RAWBLOCKS="$(( $( stat -f --format='%b*%S' ${APPDIR} ) ))"
> USEBLOCKS="$(( $( stat -f --format='%a*%S' ${APPDIR} ) ))"
> PRCNTFREE=$( printf '%.0f' $(awk "BEGIN { print ( ${USEBLOCKS} / ${RAWBLOCKS} ) * 100 }") )
> 
> if [[ ${PRCNTFREE} -gt 5 ]]
> then echo "Found enough free space" > /dev/null
> else
>    # Put a bullet in systemd's brain
>    echo o > /proc/sysrq-trigger
> fi 
> ```

基本上，上面的代码找到了目标文件系统中的可用块数，除以原始块数，并将其转换为百分比(具体来说，它检查非特权应用程序而不是根用户可用的块数)。如果百分比下降到或低于“5”，它通过`SysRq`接口发送一个“硬停止”信号。

外部(重新)构建自动化从那里开始。从超过磁盘满阈值到在替换节点上恢复业务的初步基准恢复时间大约为 15 分钟(尽管通过更好地优化构建时强化例程，在未来的迭代中将会更快)。