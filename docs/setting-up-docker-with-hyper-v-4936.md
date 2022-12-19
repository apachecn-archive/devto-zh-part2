# 使用 Hyper-V 设置 docker

> 原文：<https://dev.to/bitsmonkey/setting-up-docker-with-hyper-v-4936>

Docker 非常灵活，我们不用担心为我们的应用程序设置环境。

[![Image](../Images/934b21593ad059425647f25163d6fcb7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zFzy4rI---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s4v2omchiwudi5gmganu.jpg)

-照片由弗兰克·麦肯纳在 Unsplash 上拍摄

下载 docker for windows。

所以有了 Windows，我们可以使用 hyper-V 并在几分钟内生成容器。

我们是这样做的。

确保在 windows 上启用了 Hyper-V，如果没有，请转到打开 windows 功能并打开此功能。一旦重启完成。

使用 Hyper-V Manager for Windows 创建虚拟交换机

[![Image](../Images/687f1fae31d8739dbcaddad7e7679720.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OuNwJQgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lkzs8kihi3jg38yydjnb.PNG)

现在使用以下命令创建 docker 容器

> docker-machine create-d hyper-hyper-Virtual-Switch“主虚拟交换机”默认

这将创建一个虚拟机，您可以通过 Hyper-V 管理器或

> 坞站-ls 机器

一旦创建了机器，您需要在环境变量中设置一些机器细节

要列出变量，请使用

> docker-机器环境默认值

现在使用 bash 如下设置 env 变量

> eval $(" C:\ Users<username>\ bin \ docker-machine . exe " env default-shell bash)</username>

不，继续使用现有的 docker 图像进行测试

> docker run -it 你好世界
> 
> docker run -it supertest2014/nyan

** CTRL + C 正从中走出来