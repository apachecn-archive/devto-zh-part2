# 用系统强化服务

> 原文：<https://dev.to/djmoch/hardening-services-with-systemd-2md7>

Systemd 受到很多人的憎恨。在我看来，在这些讨论中有很多热量，但很少有光，我不期望这篇文章会改变任何已经决定讨厌 Systemd 的人的想法。我在这里的目标要小得多。我想分享新的 init 系统的一个特性，我发现这个特性非常吸引人，而且我还没有在任何地方看到讨论:Systemd 利用 Linux 内核的命名空间特性在沙箱环境中运行服务的能力。

[Linux 名称空间](https://en.wikipedia.org/wiki/Linux_namespaces)是一个内核特性，它支持从进程、文件系统到网络堆栈的一切分区。在一个命名空间中运行的进程与在另一个命名空间中运行的进程对系统资源的看法不同。Linux 名称空间最著名的应用可能是容器平台，如 [Docker](https://www.docker.com/) 。虽然 Docker 表面上是一个支持快速部署应用程序的 devops 平台，但底层的内核命名空间功能也可以应用于安全性，允许进程有效地相互隔离，并在不同程度上与底层系统隔离。这就是人们谈论在沙盒环境中运行流程或服务时的意思。

那么如果我是一个想在沙盒中运行服务的 sysadmin 呢？这通常通过设置一个 [chroot](https://wiki.archlinux.org/index.php/Change_root) 环境来完成。但是另一个选项提供了更多的灵活性，那就是在 mount 名称空间中运行服务，然后重新配置名称空间中的现有文件系统，对服务需要的数据应用最小特权，并隐藏服务不需要访问的数据。

使用 Systemd，只需在服务文件中添加几行代码，就可以根据上述场景配置服务。假设我希望我的服务在位于`/srv/http`的 chroot 中运行。假设 chroot 设置正确，服务可以访问其文件夹层次结构中需要的所有数据，那么我需要做的就是将行`RootDirectory=/srv/http`添加到 Systemd 服务文件的`[Service]`部分。

第二个场景更有趣一些。假设我正在为我的 Git 服务运行一个 web 前端，我的服务需要对`/dev/urandom` `/tmp`的访问和对`/home/git`的只读访问。Systemd 提供了几个选项，允许您以一种几乎不暴露服务的方式来完成这项工作。取下面的服务文件:

```
...
[Service]
PrivateDevices=yes
PrivateTmp=yes
ProtectHome=tmpfs
BindReadOnlyPaths=/home/git
... 
```

这些选项隐式地将服务放在一个 mount 名称空间中，这意味着我们可以按照自己喜欢的方式设置文件层次结构。在上面的例子中，`PrivateDevices`创建了一个私有的`/dev`结构，它只提供对类似`random` `null`等伪设备的访问。关键的是，当使用`PrivateDevices`选项时，磁盘设备对服务是不可见的。同样，`PrivateTmp`创建一个`/tmp`文件夹，除了服务本身写的内容之外，这个文件夹是空的。`ProtectHome`有几个选项，但是根据文档,`tmpfs`选项是为与`BindPaths/BindReadOnlyPaths`选项配对而设计的，以便有选择地提供对`/home`内文件夹的访问。因为我们所需要的只是对`git`用户的只读访问，这正是我们所提供的，不多也不少。

这一切都很棒，但无可否认，它只为服务提供了挂载命名空间。在大多数情况下，这可能已经足够了，但是 Systemd 确实提供了网络和用户名间距的选项。希望利用这些信息或寻找挂载命名空间选项的全面描述的读者，建议阅读 [systemd.exec 手册页](http://jlk.fjfi.cvut.cz/arch/manpages/man/core/systemd/systemd.exec.5.en)。