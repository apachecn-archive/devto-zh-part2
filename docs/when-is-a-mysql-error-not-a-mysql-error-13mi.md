# 什么时候 MySQL 错误不是 MySQL 错误

> 原文：<https://dev.to/booyaa/when-is-a-mysql-error-not-a-mysql-error-13mi>

*照片由[卡西迪·米尔斯](https://unsplash.com/photos/QJBsOY6e7QI)在 Unsplash* 上拍摄

最近碰到这个错误:`Mysql2::Error: Can't connect to MySQL server on 'some-db-server.example.com' (113)`

在互联网上快速搜索一下,会发现各种各样的问答网站暗示 MySQL 服务器存在连接/路由问题。

虽然这些信息对我来说可能已经足够修复了，但是如果问题存在于第三方的基础设施上，您需要提供更多的信息。

第一个调用端口是查看错误代码`113`是否出现在 [MySQL 引用](https://dev.mysql.com/doc/refman/8.0/en/error-handling.html)中。当我在这一章的任何地方都找不到`113`时，你可以想象我有多惊讶。

幸运的是，MySQL 有一个名为 [`perror`](https://dev.mysql.com/doc/refman/8.0/en/perror.html) 的实用程序，可以帮助你查找 MySQL 的错误代码。

通过键入`perror`和错误代码，您将得到以下内容:

```
$ perror 113
OS error code 113:  No route to host 
```

所以我们在 MySQL 参考手册的客户端或服务器部分都找不到这个错误的原因是它是一个操作系统错误。

有问题的操作系统是 Linux，所以我们知道我们在寻找 C 错误代码( [errno.h](https://en.wikipedia.org/wiki/Errno.h) )。如果你可以访问内核源代码，你可以在`/usr/src/linux-source-<VERSION>/include/uapi/asm-generic/errno.h`中找到它，如果你没有安装源代码，你可以看到它(参见`113` [GitHub](https://github.com/torvalds/linux/blob/master/include/uapi/asm-generic/errno.h#L96) :
的定义)

```
#define    EHOSTUNREACH    113 /* No route to host */ 
```

有了这些信息，我可以联系第三方，让他们检查我们和数据库服务器之间的路由和防火墙规则。