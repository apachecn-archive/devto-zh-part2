# OpenBSD ftpd 6.4: FTP 服务器

> 原文：<https://dev.to/nabbisen/ftp-server-on-openbsd-8cg>

## 总结

OpenBSD 有自己的 FTP 服务器，名为 [ftpd](https://man.openbsd.org/ftpd) 。
以简单安全的方式在主机间传输文件就足够了。

## 如何使用

安装操作系统时，`ftpd`作为守护进程安装在 OpenBSD 中。
为了使用它，运行`rcctl enable`和`rcctl start`，就差不多了。

### 第 0 步:配置前

默认情况下守护程序被禁用:

```
$  doas rcctl check ftpd
ftpd(failed) 
```

Enter fullscreen mode Exit fullscreen mode

### 第一步:启用守护进程

```
$  doas rcctl enable ftpd 
```

Enter fullscreen mode Exit fullscreen mode

**注:此处`/etc/rc.conf.local`更新为:*

```
$  cat /etc/rc.conf.local
ftpd_flags= 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:启动守护进程

```
$  doas rcctl start ftpd
ftpd(ok) 
```

Enter fullscreen mode Exit fullscreen mode

FTP 服务器现在正在运行。
只要没有网络问题，就可以建立到服务器的 ftp 连接😉
**注意:禁止以`root`身份访问。*

## 参考:附加配置

OpenBSD 为我们提供了[几个文件](https://man.openbsd.org/ftpd#FILES)，也就是几个方式，来添加更多的配置，比如`chroot`和认证。