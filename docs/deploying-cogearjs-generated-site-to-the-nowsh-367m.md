# 部署 Cogear。JS 生成的站点添加到 Now.sh

> 原文：<https://dev.to/cogear/deploying-cogearjs-generated-site-to-the-nowsh-367m>

[齿轮。JS](https://cogearjs.org) 是一个用[节点构建的开源静态网站生成器。JS](https://nodejs.org) 并基于最新的 [Webpack](https://webpack.js.org) 。

**dev to**会员之前可能已经阅读过[的介绍文章](https://dev.to/codemotion/cogearjs--modern-static-websites-generator-450a)和关于[如何用 **Cogear 在几分钟内创建一个博客**](https://dev.to/cogear/creating-a-blog-with-cogearjs-21af)的教程。JS 。

现在是时候讨论部署了。

当一个站点被设计成`development`模式和内置的`production`或`build`模式时，是时候将它部署到远程服务器上了。

基本上 **Cogear。JS** 可以通过 **FTP** 或 **SFTP** 协议上传，甚至使用 [rsync](https://rsync.samba.org) (如果安装的话)。

部署方法速度比较:

| 方法 | 速度 |
| --- | --- |
| 文件传送协议 | 慢的 |
| science for the people 为人类服务的科学 | 快的 |
| rsync | 最快的 |

您可以在文档中了解更多关于标准部署运营的信息:
[https://cogearjs.org/docs/deploy](https://cogearjs.org/docs/deploy)

但是现在都是关于 [Now.sh](https://now.sh) 为静态网站提供免费托管。

# 入门

你需要[下载](https://zeit.co/download)并安装桌面 **Now.sh app** 。它将提供名为`now`的命令行实用程序。

安装后，部署过程变得异常简单。

只需编译您的站点(构建它):

```
$ cd ~/Sites/site

$ cogear build 
```

它将被编译到默认为`./public`的`output`文件夹中。

[![](img/03b97d86309b493387aa0a33ea6c0642.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0iI-NLB8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jktv0tu9imsd48rllb5r.png)

现在，您可以开始部署了。

```
$ now --public public 
```

你现在向**发送命令**，在`--public`模式下上传文件夹`./public`(非付费账户需要)。

几秒钟之内，你的网站就上线了！

[![](img/c383305776b5ca5802ecdc4ecef86c46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8V8DvUMg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1b7kou3t2xp7f9o6u6lx.png)

网站地址已被复制到剪贴板。

您可以使用`now alias`命令选择一个合适的域。

```
$ now alias https://public-psuaoefsus.now.sh cogear.now.sh 
```

搞定了。

您甚至可以通过 DNS 管理使用自定义域。

像我一样:

```
$ now alias https://public-psuaoefsus.now.sh cogearjs.org 
```

[阅读更多](https://zeit.co/dns#get-started)

[齿轮。JS 官方网站](https://cogearjs.org)托管在 [Now.sh](https://now.sh) 绝对免费。