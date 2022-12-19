# apt 的 Windows 10 WSL Ubuntu 18.04 代理配置。

> 原文：<https://dev.to/david_j_eddy/windows-10-wsl-ubuntu-1804-proxy-configuration-for-apt-fhi>

对于开发人员、操作人员和管理员来说，使用 Windows Subsystem for Linux (WSL)是一个很好的工具。这些角色的缺点通常是在大型组织中提供防火墙和严密的代理安全性。

我发现自己就在这种情况下。公司代理使用*。打包文件脚本。Debian 的世界里没有这样的东西。Ugg。下面是我如何让 Debian 倾向于在代理后面工作。

来自 [Linux 问题论坛](https://www.linuxquestions.org/questions/debian-26/using-apt-behing-a-pac-http-proxy-592148/)的原始解决方案。

```
wget http://some.domain.tld/some-proxy.pac
```

...或者你可以用浏览器打开它。

寻找所有其他选项都失败时使用的代理地址。通常在底部/在它自己的函数调用中。

```
    function FindProxyForURL(url, host) { return "PROXY proxy.foo.com:8080; DIRECT"; }
```

记下 URL:端口地址。

最后，将它放在/etc/apt/apt.conf 文件中:

```
    Acquire::http::Proxy "http://proxy.foo.com:8080";
```

注销并重新登录。此时`apt-get update -y`和相关的 apt 命令应该按预期工作。

[![](../Images/7ef97066de8d32d4889f11c0e38de157.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mb4IHICx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/apt-get-update.png)