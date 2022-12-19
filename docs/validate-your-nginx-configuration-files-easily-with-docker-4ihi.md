# 用 Docker 轻松验证你的 Nginx 配置文件

> 原文：<https://dev.to/simdrouin/validate-your-nginx-configuration-files-easily-with-docker-4ihi>

这是我的第一篇帖子，请多多包涵，如果有什么可以改进的地方，请告诉我。

# 情况

让我们假设您的 web 服务器运行任何版本的 Nginx，并且您被设置为从 Git 存储库或任何其他来源自动部署 Nginx 配置文件(通过 Jenkins、StriderCD、TeamCity、Travis CI、Strider CD 或任何其他您喜欢的 CICD 工具)。

有了这样的设置，一切都运行得很顺利，直到有人修改了有错别字或错误的配置文件，在这种情况下，您不希望这些文件到达 Nginx 服务器。

为了避免这种情况，您希望在更新过程中尽快进行验证。

这种情况在我们身上发生过几次，所以我决定写一个小脚本，在允许部署之前验证`nginx.conf`文件。

# 第一步

我们想做的第一件事是能够从一台装有 Nginx 的服务器上验证 Nginx 配置文件。使用这个命令很容易做到:
`nginx -c /etc/nginx/nginx.conf -t`

对于有效的配置文件，它返回这个:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful 
```

Enter fullscreen mode Exit fullscreen mode

对于无效的配置文件，它会返回如下内容:

```
2018/04/13 21:26:15 [emerg] 1#1: unexpected ";" in /etc/nginx/nginx.conf:155
nginx: [emerg] unexpected ";" in /etc/nginx/nginx.conf:155
nginx: configuration file /etc/nginx/nginx.conf test failed 
```

Enter fullscreen mode Exit fullscreen mode

# 记录本次验证

然而，这需要编辑配置文件的人在机器上安装了 Nginx。情况并非总是如此，我们也不想在构建服务器上安装 Nginx(不同的服务器上也可能有多个版本...)，所以我们转向 Docker，因为我们已经用了很多了。

几分钟后，我们能够想出一个类似的命令，可以在每个安装了 Docker 的系统上运行:

```
docker run --rm -t -a stdout --name my-nginx -v $PWD/config/:/etc/nginx/:ro nginx:latest nginx -c /etc/nginx/nginx.conf -t 
```

Enter fullscreen mode Exit fullscreen mode

Docker 命令简要解释道:

*   `--rm`:执行完成后移除容器
*   `-t -a stdout`:在标准输出上输出容器日志
*   `--name my-nginx`:码头集装箱的名称
*   `-v $PWD/config/:/etc/nginx/:ro`:将`config`子文件夹映射到容器内的 Nginx config 文件夹
*   hub.docker.com 的 Nginx 镜像版本
*   `nginx -c /etc/nginx/nginx.conf -t`:验证 nginx.conf 文件的 Nginx 命令

为了在 Docker for Windows 上运行，可能需要做一些调整

这假定您具有以下文件夹结构:

*   。/
*   。/config
*   。/config/nginx.conf

这样，每个人都可以在 config 目录的父文件夹中运行这个命令，看看他们的`nginx.conf`文件是否有效。

# 使用 bash 脚本使之更简单

尽管这已经是一个好的开始，但我们希望这样做，以便我们得到一个成功或失败的消息，不管配置文件是否有效。我们还希望能够为 Nginx docker 映像指定一个更动态的版本，而且最重要的是，我们不想记住所有这些命令。

这是我们在一个名为`checkNginx.sh` :
的文件中得到的结果

```
#!/bin/bash
rootPath=$1
nginxVersion=1.13  # Could also be passed as an argument using $2

result=$(docker run --rm -t -a stdout --name my-nginx -v ${rootPath}/config/:/etc/nginx/:ro nginx:$nginxVersion nginx -c /etc/nginx/nginx.conf -t)

# Look for the word successful and count the lines that have it
# This validation could be improved if needed
successful=$(echo $result | grep successful | wc -l)

if [ $successful = 0 ]; then
    echo FAILED
    echo "$result"
    exit 1
else
    echo SUCCESS
fi 
```

Enter fullscreen mode Exit fullscreen mode

可以使用以下命令调用该脚本:
`bash checkNginx.sh $PWD`

如果一切正常，则输出`SUCCESS`，如果有错误，则输出以下内容:

```
FAILED
2018/04/13 21:26:15 [emerg] 1#1: unexpected ";" in /etc/nginx/nginx.conf:155
nginx: [emerg] unexpected ";" in /etc/nginx/nginx.conf:155
nginx: configuration file /etc/nginx/nginx.conf test failed 
```

Enter fullscreen mode Exit fullscreen mode

然后，在部署过程的测试阶段，我们能够从构建服务器运行相同的命令，并在 Nginx 配置文件中出现打字错误或错误时完全中止配置的部署。

这也可以被添加到一个*预提交*或*预推送* Git 挂钩中，以便存储库跟踪这些配置，但是到目前为止，我们觉得没有必要这样做。

感谢阅读，希望你欣赏:)