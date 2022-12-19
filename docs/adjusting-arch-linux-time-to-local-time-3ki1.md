# 将 Arch Linux 时间调整为本地时间

> 原文：<https://dev.to/musale/adjusting-arch-linux-time-to-local-time-3ki1>

在更新我的系统后，我发现显示的系统时间提前了 1 小时。事实上，我以为我吃午饭要迟到了，却发现时钟快了一个小时。

在网上翻来翻去，碰到很多解决不了的办法。然而，这被证明是有效的。检查`timedatectl`状态后:

```
$ timedatectl status
$ Local time: Tue 2018-04-03 15:02:23
  Universal time: Tue 2018-04-03 12:02:23 UTC
  RTC time: Tue 2018-04-03 12:02:23
  Time zone: Africa/Nairobi (EAT, +0300)
  System clock synchronized: no
  systemd-timesyncd.service active: no
  RTC in local TZ: no 
```

环顾四周，简单的解决方法是运行:

```
$ timedatectl set-ntp true 
```

嘣！在下一个时钟滴答声，时间调整正确。

### 动机

如果你把你的 Arch Linux 升级到最新版本，你可能会面临这个问题，所以如果你这样做了，不要担心😉