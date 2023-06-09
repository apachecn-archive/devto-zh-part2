# 在 Ubuntu 中设置代理

> 原文：<https://dev.to/ech0server/setting-a-proxy-in-ubuntu-efe>

要在 Ubuntu 中配置代理，您可以在不同的地方设置它:

## 贴切

为了将 apt 与代理一起使用，您必须用以下代码编辑`/etc/apt/apt.conf`文件:

```
Acquire::http::proxy "http://username:password@host:port/";
Acquire::ftp::proxy "ftp://username:password@host:port/";
Acquire::https::proxy "https://username:password@host:port/"; 
```

Enter fullscreen mode Exit fullscreen mode

## 环境变量

要将代理作为环境变量添加，请使用以下代码编辑`/etc/environment`文件:

```
http_proxy=http://username:password@host:port/
ftp_proxy=ftp://username:password@host:port/
https_proxy=https://username:password@host:port/ 
```

Enter fullscreen mode Exit fullscreen mode

## gtk3 程序

对于 gtk3 程序，使用`network settings`中的代理设置。

我是从:[[https://www . quora . com/How-do-I-set-the-proxy-setting-using-Linux-command-line-on-Ubuntu-14-04](https://www.quora.com/How-do-I-set-the-proxy-setting-using-Linux-command-line-on-Ubuntu-14-04)得到的信息