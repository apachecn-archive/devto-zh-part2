# 使用 Docker 中的“socat”来支持一次性下载服务

> 原文：<https://dev.to/jmarhee/using-socat-in-docker-to-power-a-single-use-download-service-3c98>

<figure>[![](img/5710a4cb388dd10a77654bbdb8060a80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8uLHvwmg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao8EmyG8hOa0C7gi4zRifKQ.png) 

<figcaption>[来源](https://cntnr.io/running-guis-with-docker-on-mac-os-x-a14df6a76efc)</figcaption>

</figure>

2016 年，我搭建了一个依靠 Docker 提供 VPN 部署服务的 app。自动化的一部分允许我自动生成，并为将 VPN 容器部署到其服务器的用户提供通过单一用户界面下载其配置的能力，以确保只能下载一份副本。

我不会深入应用程序本身的细节，只关注这一个功能(它将通知容器的生命周期，从而通知服务)，但是，出于好奇，您可以在这里查看应用程序组件:

[https://github . com/jmarhee/dock VPN](https://github.com/jmarhee/dockvpn)

顾名思义，配置将主要依赖于一个 shell 脚本来打开和终止与配置提供者服务的连接，它使用 socat [Linux 实用程序](https://linux.die.net/man/1/socat) y 来完成这一任务。这是一个非常可配置的实用程序，通过 Socket 接口提供了许多选项:

[http://man7.org/linux/man-pages/man7/socket.7.html](http://man7.org/linux/man-pages/man7/socket.7.html)

在这个例子中，与我们相关的是 SO_REUSEADDR 和 Linux fork()系统调用来创建一个新的子进程(我们不会使用它，但是我会解释为什么在其他情况下会使用它——如果您还不熟悉类似 Unix 的系统中的进程管理的话)。

通常，要使用 socat 中继数据，您可以使用如下命令:

```
socat -d -d TCP-LISTEN:8080,reuseaddr,fork EXEC:"cat config" 
```

Enter fullscreen mode Exit fullscreen mode

其中 config 是我的示例中要从磁盘读入响应的文件。

在您的 TCP-LISTEN 块中(您可以使用许多其他绑定选项，但因为这将主要使用覆盖网络为 Docker 容器之间的流量提供服务，并且对单机版文件的入口本身将使用前端应用程序上的 TLS，我使用了 TCP 端口)，您指定了端口(8080)，然后重用 addr(这对我们来说是可选的，因为我们只计划使用接口一次，而不重试——这主要确保我们可以为该服务绑定本地地址)，然后 fork，在请求完成时创建一个新的进程。

[![](img/7ecc7aca2b4a4fa4cd88218c5f9e1bb7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6_6QXifL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AfvlorB-Ihh_U0Nwp.png)

然后我们可以发出请求，并看到 socat 进程仍然绑定到端口 8080:

[![](img/a0f89e50c8244be26027f90d0bf30926.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QdnwpEjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AfruqrDJrYcjZvFot.png)

[![](img/94a7a3a2f5082af711ec1abdb47a770b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2iM1cIK2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AExq0awD5mONqiVQM.png)

好吧，很好，对吧？因此，在我们的例子中，我们*不想*能够第二次请求这个数据，正如你从我们的输出中看到的，我们的进程退出 0，一个子进程被创建，并再次绑定到端口 8080 用于一个新的连接:

```
...
2018/07/07 04:35:22 socat[464] N exiting with status 0
2018/07/07 04:35:22 socat[461] N childdied(): handling signal 17
2018/07/07 04:35:23 socat[461] N accepting connection from AF=2 100.115.92.193:60238 on AF=2 100.115.92.197:8080
2018/07/07 04:35:23 socat[461] N forked off child process 466
2018/07/07 04:35:23 socat[461] N listening on AF=2 0.0.0.0:8080
... 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们在服务器端修改我们的命令，看起来像:

```
TCP-LISTEN:8080,reuseaddr 
```

Enter fullscreen mode Exit fullscreen mode

如果不使用 fork，要防止替换已完成请求的新流程运行:

[![](img/dad91378b63280edcd85a53a90ba2a8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jnWOtYYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Apk4Eq_nnF-G8JEOK.png)

您将看到，在该进程退出后，每个后续请求都会失败:

[![](img/64b7e1842def64bc9a4ac8cb48eb8df4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2M1QZtoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A5ieMc8-F9UZF99wK.png)

如我所说，这个 TCP 端口将只在 Docker 容器网络覆盖上公开，其中一个前端服务将发出这样的请求:

```
get "/config" do
 response = HTTParty.get("http://dockvpn_serveconfig_1:8080")
 tempfile = Tempfile.new('client.ovpn')
 File.open(tempfile.path,'w') do |f|
 f.write response.body
 end
 send_file(tempfile)
end 
```

Enter fullscreen mode Exit fullscreen mode

创建一个可下载的配置副本并将其提供给 UI，并使用我们的 socat 命令作为后端 in _servconfig，只有第一个请求会成功。

因此，回到我们的 serveconfig 服务，我们将创建一个脚本 entrypoint.sh，如下所示:

```
#!/bin/bash

socat -d -d \
 TCP-LISTEN:8080,reuseaddr \
 EXEC:'cat client.http' \
 2\>\> http8080.log 
```

Enter fullscreen mode Exit fullscreen mode

和一个 docker 文件，如:

```
FROM ubuntu:trusty

RUN apt-get update ; \
 apt-get install -y socat

COPY entrypoint.sh serveconfig

ENTRYPOINT serveconfig 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里的入口点实际上就是我们的 socat 命令，所以一旦进程退出，容器就会停止，在 Composefile 的上下文中，比如:

[https://raw . githubusercontent . com/jmar hee/dock VPN/master/docker-compose . yml](https://raw.githubusercontent.com/jmarhee/dockvpn/master/docker-compose.yml)的缩写

serveconfig 服务将不再可用(所有容器都已完成)，就像我们的手动 CLI 示例一样，后续请求将无法连接！这意味着，如果服务开始运行，而您发现服务不可用，这是一个可靠的指标，表明服务已经失败，或者资产已经被请求，您应该重新提供该资源，Docker 使用类似上面的 composefile 这样的声明，使复制和再现变得非常容易。

[Socat 联机帮助页](https://linux.die.net/man/1/socat)

[Socket — Linux 编程参考](http://man7.org/linux/man-pages/man7/socket.7.html)

[Bind — Linux 编程参考](http://man7.org/linux/man-pages/man2/bind.2.html)