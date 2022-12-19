# 减少 Perl6 Cro webapp 的 Docker 图像大小

> 原文：<https://dev.to/lancew/reducing-docker-image-size-for-a-perl6-cro-webapp-4kd3>

我的[https://myjudo.net？UTM _ source = RSS&UTM _ medium = RSS](https://myjudo.net?utm_source=rss&utm_medium=rss)perl 6 应用程序已经上线一段时间了；而且最近我已经把它从 Bailador 框架移植到 Cro 上了。我的$dayjob 同事(也是[背后的男人)https://code-golf.io？UTM _ source = RSS&UTM _ medium = RSS](https://code-golf.io?utm_source=rss&utm_medium=rss))一直在提供帮助，最近还帮助调优了应用程序，特别是 docker 容器。

[Cro](https://cro.services/?utm_source=rss&utm_medium=rss) ，当你 stub 一个应用提供了一个 Docker 文件，但它并没有特别优化…所以新的 Docker 文件看起来像这样:

> 来自阿尔卑斯山:3.7
> 
> 运行 apk add–无缓存 curl gcc git libres sl-dev Linux-headers make musl-dev perl SQLite-libs
> 
> # 安装 Perl 6
> 
> run curl-l[https://github . com/rakudo/rakudo/archive/2018.05 . tar . gz？UTM \ _ source = RSS&UTM \ _ medium = RSS](https://github.com/rakudo/rakudo/archive/2018.05.tar.gz?utm%5C_source=rss&utm%5C_medium=rss)\
> | tar xzf–\
> &&【rakudo CD-2018.05 \
> &&
> 
> # 安装 zef
> 
> 运行 git 克隆[https://github.com/ugexe/zef.git?utm\_source=rss&UTM \ _ medium = RSS](https://github.com/ugexe/zef.git?utm%5C_source=rss&utm%5C_medium=rss)\
> 
> &&CD zef \
> 
> &&perl 6-Ilib bin/zef install-/test。
> 
> WORKDIR /app
> 
> COPY META6.json .
> 
> 运行/usr/share/perl 6/site/bin/zef install–deps-only –/ test。
> 
> # 避免在最终图像中出现“二进制”
> 
> 运行 rm -r /usr/share/perl6/site/bin
> 
> 从头做起
> 
> COPY–from = 0/bin/sh/bin/
> 
> COPY–from = 0/lib/LD-musl-x86 _ 64 . so . 1 \
> 
> /lib/libz。*/lib/
> T5】COPY–from = 0/usr/bin/moar \
> 
> /usr/bin/perl 6/usr/bin/
> 
> COPY–from = 0/usr/lib/libmoar . so \
> 
> /usr/lib/lib crypto。* \
> /T13】/usr/lib/libsqlite3。* \
> /T15】/usr/lib/libssl。*/usr/lib/
> 
> COPY–from = 0/usr/share/nqp/usr/share/nqp
> T19】COPY–from = 0/usr/share/perl 6/usr/share/perl 6
> 
> WORKDIR /app
> 
> 收到。/应用程序
> 
> CMD [“perl6”, “-Ilib”, “service.p6”]

你可以看到它从 [Alpine Linux](https://www.alpinelinux.org/?utm_source=rss&utm_medium=rss) 开始，一个裸机 Linux。从那里我们添加了一些我们需要的 Linux 工具，特别是 gcc，这样我们可以在以后构建 [Crypt::Bcrypt](https://github.com/skinkade/p6-Crypt-Bcrypt?utm_source=rss&utm_medium=rss) 以及 SSL 和 SQLite 的库。

接下来… Perl 6 本身(实际上，首先是 Perl 5，然后是 Perl 6，因为您需要 Perl 5 来构建 Perl 6)。我们直接从 Rakudo github repo 获得 Perl6 并构建它。接下来我们构建 zef，这样我们就可以安装我们需要的 Perl 6 模块。

此时，我们跳转到/app 目录，复制 META6.json 文件。META6.json 类似于节点 javascript 世界中的 package.json。它列出了需要的模块，所以我们可以调用 zef 并安装这些模块(我关闭了测试)。

在这之后，我们删除一些 perl 6 二进制文件以保持总的容器大小更小。

接下来…

我们从最初的构建中复制我们需要的东西到一个新的构建中，以使大小最小化，这包括复制我们需要的 Linux 系统模块，如 ssl 和 sqlite 库。最后，我们复制应用程序代码。

我们可以构建和共享这个 docker 文件，它总共有 30MB，比由 cro stub 命令创建的 docker 文件生成的原始容器要小得多。

在这一阶段，我将它推送到 docker hub，并将其拉到生产服务器上运行，这是我如何调用它的一个示例:

> ```
> #!/bin/sh -e docker pull lancew/myjudo exec docker run \ -e MYJUDO\_TLS\_CERT=/etc/letsencrypt/live/myjudo.net/fullchain.pem \ -e MYJUDO\_TLS\_KEY=/etc/letsencrypt/live/myjudo.net/privkey.pem \ -p 80:1080 \ -p 443:1443 \ -w /app \ -v /app/db:/app/db \ -v /etc/letsencrypt:/etc/letsencrypt \ lancew/myjudo \ perl6 -Ilib service.p6 
> ```

在这里，我首先提取最新的容器，然后运行它。

e 行设置 cro 使用的环境变量，特别是 SSL 证书。

p 行设置将外部可用端口链接到容器内部端口的端口，因此端口 80 指向容器内部的 1080。

-v 行集挂载到文件系统。在我们的例子中，我们将容器内的主机服务器/app/db 映射到/app/db，以便 cro 应用程序可以从 sqlite 数据库文件中读取/写入。我们还映射了 letsencrypt ssl 证书。

这使旧的部署思想现代化了，因此我们不需要将源代码复制到生产服务器，安装模块并调用系统 Perl 等，而是简单地复制(或者在我的例子中是拉取)包含代码、模块和 perl 6 的容器。那就通过 Docker 运行。

因为在 dev 中我也使用容器，所以我们失去了经典的“在我的机器上工作”的问题。这很好。

我不是码头专家，所以得到一些帮助非常有帮助，也很有教育意义。我希望我有一个易于维护和部署的应用程序。

正如我在其他地方说过的，使用 cro 和 perl 6 是令人愉快和富有成效的。将它与 docker 结合起来，看起来是一个真正现代的 web 应用程序平台，可以与更时髦的框架和语言抗衡。

如果你还没有尝试过 Perl 6 编程语言，那么我认为你应该试一试。它真的是一种很棒的语言，有很好的工具，感觉很现代，随着时间的推移和发展，我认为/希望它能与 Phoenix、Express 等语言抗衡

如果你确实想尝试，请让我知道！