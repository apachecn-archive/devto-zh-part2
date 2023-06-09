# 如何在 Vultr 上运行 OpenBSD 6.3 实例

> 原文：<https://dev.to/mnlwldr/how-to-run-openbsd-63-instance-on-vultr-2cpm>

创建一个新实例，选择 OpenBSD 6.3 作为操作系统。一分钟后，您可以通过 ssh 登录新实例。

首先我们设置 installurl，然后我们应该用`syspatch`给机器打补丁。

> `/etc/installurl`文件包含一行指定 OpenBSD 镜像服务器 URL 的代码。`syspatch`是一个获取、验证、安装和恢复 OpenBSD 二进制补丁的实用程序。

```
echo 'https://fastly.cdn.openbsd.org/pub/OpenBSD' > /etc/installurl && syspatch 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们用`adduser`为系统创建一个新用户，然后将他们添加到`/etc/doas.conf`。

> 使用`doas`，我们可以作为另一个用户执行命令，例如作为 root 用户。

```
echo "permit YOUR_NEW_USER" > /etc/doas.conf 
```

Enter fullscreen mode Exit fullscreen mode

为您的新用户添加一个 ssh 密钥。在本地机器上打开一个终端并执行

```
ssh-copy-id -i path/to/your/public.key YOUR_NEW_USER@server 
```

Enter fullscreen mode Exit fullscreen mode

切换回 OpenBSD 实例，在`/etc/ssh/sshd_config`中编辑 ssh 守护进程配置。

我们不允许以 root 用户身份通过 ssh 登录

`PermitRootLogin no`

我们禁止通过 ssh 使用密码登录

`PasswordAuthentication no`

现在用
检查配置

```
sshd -t 
```

Enter fullscreen mode Exit fullscreen mode

用
重启 ssh 守护进程

```
rcctl restart sshd 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够使用您的 _NEW_USER 通过 ssh 登录，而不需要密码。如果有效，关闭您的根连接并切换到您的 _NEW_USER。

我们需要设置一些网络信息。你可以在我的 vultur 上找到所有信息

用您的网关
填充`/etc/mygate`文件

```
echo YOUR_GATEWAY > /etc/mygate 
```

Enter fullscreen mode Exit fullscreen mode

用以下文本
填充`/etc/hostname.vio0`

```
inet YOUR_INSTANCE_IP 255.255.254.0 NONE
inet6 autoconf -autoconfprivacy -soii 
```

Enter fullscreen mode Exit fullscreen mode

用以下文本
填充`/etc/resolv.conf`

```
nameserver 108.61.10.10
lookup file bind 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。不，我们在 vultr 上有一个正在运行的 OpenBSD 6.3 实例。