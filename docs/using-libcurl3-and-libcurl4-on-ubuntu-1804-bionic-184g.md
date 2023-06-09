# 在 Ubuntu 18.04 上使用 libcurl3 和 libcurl4(仿生)

> 原文：<https://dev.to/jake/using-libcurl3-and-libcurl4-on-ubuntu-1804-bionic-184g>

[![image](img/77ea8ba61416705699ec2ddfe6703346.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t8859Dre--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wa3vunhh44isjs2hgtkb.png)

我相信我们中的许多人在任何时候都因为某种原因需要在 Ubuntu 18 上运行 libcurl3 和 libcurl4。

在 Ubuntu 18.04 (Bionic)上，libcurl3 和 libcurl4 不能共存。许多第三方包、库等需要 libcurl3，而 curl 本身需要 libcurl4。

请注意，这个解决方案只针对预编译的命令(和潜在的二进制文件，未经测试)。这是在一个 [C#二进制代码](https://github.com/tihmstar/tsschecker)上测试的。

首先，您需要获得 libcurl3 的副本，并将其保存到您的`/usr/lib`目录中。您可以通过运行:
来实现

```
$ sudo apt-get install libcurl3 -y
$ cp /usr/lib/x86_64-linux-gnu/libcurl.so.3 /usr/lib/ 
```

Enter fullscreen mode Exit fullscreen mode

然后删除 libcurl3 并重新安装 libcurl4:

```
$ sudo apt-get install libcurl4 libcurl4-openssl-dev -y 
```

Enter fullscreen mode Exit fullscreen mode

最后，在任何需要 libcurl3 的命令前面加上`env LD_PRELOAD=/usr/lib/libcurl.so.3`。