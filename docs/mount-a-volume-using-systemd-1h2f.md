# 使用系统装入卷

> 原文：<https://dev.to/adarshkkumar/mount-a-volume-using-systemd-1h2f>

## 什么是 systemd？

`systemd`是一套软件，为 Linux 操作系统提供了基本的构建模块。这是一个初始化系统，因此是第一个启动的过程(pid 1)。它将启动和管理其他服务，并管理它们之间的依赖关系。

它取代了 sysvinit，后者是许多 Linux 发行版中的新贵。到目前为止，几乎所有流行的 Linux 发行版都使用 systemd 作为默认的 init 系统(包括我最喜欢的 Debian)。

一直以来，为了在自动启动后挂载一个卷，我们常常用`fstab`来捣乱。最近，我知道我们可以使用 systemd 来安装卷，下面是如何做。

## 单元配置文件

由 systemd 管理的任何实体(服务、套接字、设备、挂载点等)的配置都在单元配置文件中维护。它们通常位于`/etc/systemd/system`。

您可以使用`systemctl`命令启动/停止/重启检查机器中的服务状态。

```
systemctl status postgresql.service
systemctl start postgresql.service
systemctl stop postgresql.service
systemctl restart postgresql.service 
```

Enter fullscreen mode Exit fullscreen mode

这个命令提供了更多的功能，更多的细节请参考联机帮助页。

```
# systemctl status postgresql.service 

● postgresql.service - PostgreSQL RDBMS
   Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
   Active: active (exited) since Sun 2018-05-20 17:37:20 IST; 1 day 20h ago
  Process: 976 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
 Main PID: 976 (code=exited, status=0/SUCCESS)
    Tasks: 0 (limit: 4915)
   Memory: 0B
      CPU: 0
   CGroup: /system.slice/postgresql.service 
```

Enter fullscreen mode Exit fullscreen mode

## 挂载单元配置

为了使用 systemd 挂载文件系统，我们创建了特殊类型的单元文件，其扩展名为`.mount`和`.automount`

> 挂载单元必须以它们控制的挂载点目录命名。示例:挂载点/home/lennart 必须在单元文件 home-lennart.mount.
> 中配置

```
[Unit]
Description=Additional drive

[Mount]
What=/dev/disk/by-uuid/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
Where=/mnt/driveone
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用
启用该单元

```
systemctl enable mnt-driveone.mount 
```

Enter fullscreen mode Exit fullscreen mode

## 自动挂载

这些单元文件配置以`.automount`结尾。这可用于按需自动挂载文件系统。

```
Description=Automount Additional Drive

[Automount]
Where=/mnt/driveone

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

> 自动装载单元必须以它们控制的自动装载目录命名。

每个`automount`单元文件必须有一个匹配的`mount`单元配置，当访问`automount`配置中的路径`Where`时激活。

所以挂载`/mnt/mydata`需要的单元配置是:

```
/etc/systemd/system/mnt-mydata.automount
/etc/systemd/system/mnt-mydata.mount 
```

Enter fullscreen mode Exit fullscreen mode

> 最初发布于 [JigsawCode](https://www.jigsawcode.com/)

## 参考文献:

[Systemd 挂载参考](https://www.freedesktop.org/software/systemd/man/systemd.mount.html)
[Systemd 自动挂载参考](https://www.freedesktop.org/software/systemd/man/systemd.automount.html)