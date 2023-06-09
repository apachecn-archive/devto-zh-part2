# 如何修复 Manjaro Linux i3 中的红移

> 原文：<https://dev.to/lobo_tuerto/how-to-fix-redshift-in-manjaro-linux-i3-3i6k>

红移是一个非常酷的程序，可以帮助你保持视力健康——当夜晚来临的时候，它会把你的屏幕染成红色。

这会减轻你眼睛的压力。

# 问题

一切都运行良好，我安装了`redshift`包，并设置它在我的`~/.i3/config`上自动启动。

就在一次更新之后，当我试着运行它的时候，我开始看到这个:

```
Trying location provider `geoclue2'...
Using provider `geoclue2'.
Using method `randr'.
Waiting for initial location to become available...
Unable to start GeoClue client:
 GDBus.Error:org.freedesktop.DBus.Error.NoReply:
 Message recipient disconnected from message bus without replying.
Unable to get location from provider. 
```

Enter fullscreen mode Exit fullscreen mode

并且在尝试启动`redshift-gtk`时看到这个对话框:

[![redshift-gtk error](img/76219fe0c0484e3a2e6957df9247adfc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tFboC-Ha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lobotuerto.com/blog/how-to-fix-redshift-in-manjaro-linux-i3/redshift-gtk-error.png)

从那以后，它再也没有工作过，即使是最新版本的`geoclue2`和`redshift` :

```
pacman -Q geoclue2 redshift
geoclue2 2.4.12-1
redshift 1.12-2 
```

Enter fullscreen mode Exit fullscreen mode

# 解

在做了一些调查之后，我找到了一个解决办法。

编辑您的`/etc/geoclue/geoclue.conf`文件，并将其添加到末尾:

```
# ...
[redshift]
allowed=true
system=false
users= 
```

Enter fullscreen mode Exit fullscreen mode

要自动启动**红移**，将这些行添加到您的`~/.i3/config` :

```
exec --no-startup-id /usr/lib/geoclue-2.0/demos/agent
exec --no-startup-id redshift-gtk 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

# 链接

*   [红移](https://github.com/jonls/redshift) —红移根据周围环境调整你屏幕的色温。
*   [ArchWiki – Redshift](https://wiki.archlinux.org/index.php/Redshift)
*   [红移不允许使用地理蓝](https://github.com/jonls/redshift/issues/636)