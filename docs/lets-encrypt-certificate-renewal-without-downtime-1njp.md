# 让我们在不停机的情况下加密证书续订

> 原文：<https://dev.to/booyaa/lets-encrypt-certificate-renewal-without-downtime-1njp>

*[照片](https://unsplash.com/photos/eCNjfCrZeYY)埃里卡·尼尔森拍摄*

警告:这篇博文假设了以下情况:

*   您正在一个 [Docker](https://www.docker.com/) 容器中运行 nginx。
*   让我们在 nginx 容器中正确配置加密。
*   你读这个博客真是太棒了。

[certbot](https://certbot.eff.org/) (让我们加密的命令行工具)的默认行为是重启 web 服务器。这在真实环境中是不可取的，理想情况下，您希望您的 web 服务器重新加载它的配置。对于 [nginx](https://www.nginx.com/) ，这涉及到向进程发送一个[信号](https://en.wikipedia.org/wiki/Signal_(IPC))，在本例中是`HUP`(挂断)。

但是你怎么知道你的证书已经被更新了呢？

nginx(组织而不是 web 服务器)推荐的方法是在触发 NGINX 重新加载配置之前检查 PID(进程 id)。

```
docker top <NGINX_CONTAINER_ID> axw -o pid,ppid,command | egrep '(nginx|PID)'
PID                 PPID                COMMAND
2089                31208               tail -f /var/log/nginx/access.log
3509                31222               nginx: worker process
31222               31208               nginx: master process nginx -g daemon off; 
```

Enter fullscreen mode Exit fullscreen mode

您要观察的 PID 是 **nginx 工作进程**(命令)，在本例中是 **3509** 。

亲提示:可以传递`docker top`子命令`ps`标志？整洁吧？

现在让我们向容器发送一个`HUP`信号，强制 nginx 重新加载配置:`docker kill —signal HUP <NGINX_CONTAINER_ID>`

然后重新检查 PID

```
docker top <NGINX_CONTAINER_ID> axw -o pid,ppid,command | egrep '(nginx|PID)'
PID                 PPID                COMMAND
2089                31208               tail -f /var/log/nginx/access.log
3643                31222               nginx: worker process
31222               31208               nginx: master process nginx -g daemon off; 
```

Enter fullscreen mode Exit fullscreen mode

**nginx 工作进程**的 PID 现在已经变成了 **3643** ！

延伸阅读:

*   [控制 nginx](http://nginx.org/en/docs/control.html)