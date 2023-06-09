# 从本地主机在远程主机上运行 Systemd 命令

> 原文：<https://dev.to/mayeu/running-systemd-commands-on-remote-host-from-your-local-host-2jea>

当我在阅读[非常棒的 Archlinux wiki](https://wiki.archlinux.org/index.php/systemd) 寻找完全不同的东西时，我发现人们可以通过`--host`(或`-H`)标志向远程主机发出任何`systemctl`命令。

假设您想在一个名为
的服务器上检查`cron`进程

```
$ systemctl -H root@kitten status cron
● cron.service - Regular background program processing daemon
   Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2018-07-28 11:36:37 CST; 2 months 25 days ago
     Docs: man:cron(8)
 Main PID: 1045
    Tasks: 1 (limit: 4643)
   CGroup: /system.slice/cron.service
           └─1045 /usr/sbin/cron -f 
```

不错吧？在幕后，使用 SSH，因此您必须能够访问主机。当然，您可以使用任何`systemctl`命令，而不仅仅是`status`；)

玩得开心👋

*本文最初发表于 [mayeu.me](https://mayeu.me/blog/running-systemd-command-on-remote-host-from-local-host/) 。*