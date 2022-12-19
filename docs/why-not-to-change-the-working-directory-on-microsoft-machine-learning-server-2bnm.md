# 为什么不改变微软机器学习服务器上的工作目录

> 原文：<https://dev.to/leolanchas/why-not-to-change-the-working-directory-on-microsoft-machine-learning-server-2bnm>

不久前，我开发了一个使用微软的 R 服务，现在叫做机器学习服务器，它没有像文档 [***示例***](https://docs.microsoft.com/en-us/machine-learning-server/operationalize/how-to-deploy-web-service-publish-manage-in-r#standard-workflow-examples) 中建议的那样创建一个模型，它只是运行 R 例程，这些例程操作一个 [***数据。因此，我需要在每次调用 API 服务时加载我的代码，因为您不能使用***](https://cran.r-project.org/web/packages/data.table/vignettes/datatable-intro.html) ***[***快照***](https://docs.microsoft.com/en-us/machine-learning-server/r/how-to-execute-code-remotely#r-session-snapshots) ，而只能在使用 R-Client 时使用(不能在任何 web 应用程序上使用)。***

当我试图改变工作目录时，问题出现了。因为我对不同的模块使用了相对路径，例如:

```
source('/home/administrator/src/MyMainFile.R') 
```

这又加载了一些其他模块，但正如我所说的，通过相对路径:

```
source('/utils.R') 
```

我需要设置我的基本目录。结果，在 3 次或者幸运的是 4 次非常慢的执行之后，web 节点崩溃了，我得到的唯一日志消息是:

```
{ "Link": null, "Message": "c66b3558-9d60-4620-adba-1429fc5355ab", "ExceptionType": null } { "Link": null, "Message": "b16bdf62-1396-4be9-ad54-bd1ed59f0d35", "ExceptionType": null } 
```

没有人在微软论坛上只给出一个答案。所以在几天的碰壁之后(因为我已经深入彻底地测试了我的代码)，我决定一次一行地重新发布这个服务，并运行 3 到 4 次。当只有第一行**setwd(' my/base/directory ')**对我的绝望负责时，我的惊讶是巨大的。当你看到微软文档里没有记载这一点的时候就更糟糕了。

所以解决方案很简单:

1.  移除 **setwd()**
2.  使用绝对路径

尽管如此，在那次经历之后，我决定不制作一个包就再也不用 MLS 了，所以从那时起我就用[***Rcpp***T5】和](https://cran.r-project.org/web/packages/Rcpp/index.html)[***R6 classes***](https://cran.r-project.org/web/packages/R6/vignettes/Introduction.html)***。*T15】**

*原载于 2018 年 4 月 14 日*[*leolanchas . ml*](http://leolanchas.ml/blog/2018/04/14/not-change-working-directory-machine-learning-server/)*。*