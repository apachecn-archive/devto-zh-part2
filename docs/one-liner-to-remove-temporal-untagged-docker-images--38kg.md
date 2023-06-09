# 如何移除临时未标记的 Docker 图像

> 原文：<https://dev.to/ech0server/one-liner-to-remove-temporal-untagged-docker-images--38kg>

*更新，感谢 [champkeh](https://dev.to/champkeh)

移除未使用的 docker 图像的正确方法是使用:

`docker image prune [OPTIONS]`

欲了解更多信息，请访问:

[https://docs . docker . com/engine/reference/command line/image _ prune/](https://docs.docker.com/engine/reference/commandline/image_prune/)

* * *

如果你有一些使用 Docker 的经验，你会知道在建立你自己的图像时会犯一些错误。起初，我并不介意使用`docker rmi ######`命令不时地删除一个未标记的图像，然而，当我不得不在一个项目中将一个遗留应用程序迁移到 Docker 时，我意识到有许多图像只是悬在空中，就像“我们在这里只是放松一下”。

[![chillin](img/5f5912b8a364444ab9bfce3ee95ccc0d.png "Chillin")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NJJ3Peka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/in5wq65fd5bvl6bibkzg.png)

所以，我开始一个接一个地做一些清理工作，直到，你知道，谁想粘贴图像 id 或名称来删除它？没错。然后我想到了下面的一句俏皮话:

`docker images | awk '{if(match($1,"<none>")){print "docker rmi " $3 ";" | "/bin/sh"}}'`

然后，您可以将该行添加到 shell 脚本或其他脚本中。好的一面是，如果由于某种原因，一个图像有一个依赖的子对象(关联的容器)，它不会删除它，除非你强制它(-f)。