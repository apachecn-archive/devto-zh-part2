# Docker 最佳实践:图像

> 原文：<https://dev.to/grigorkh/docker-best-practices-images-558>

# 了解构建上下文

当您发出 docker 构建命令时，当前工作目录称为构建上下文。要排除与构建无关的文件(不重新构建您的源存储库),请使用. dockerignore 文件。该文件支持类似的排除模式。gitignore 文件。有关创建的信息，请参见[。dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file) 文件。

# 非 root 用户

默认情况下，Docker 以 root 用户身份运行容器，容器内部可能存在安全问题。您可能希望尽可能以无特权用户的身份运行容器。

# 最小化层数

尽量减少将在 docker 文件中创建的层数。指令运行，复制，添加创建层。其他指令创建临时中间映像，并不直接增加构建的大小。

### 举例:

假设您需要获取一个 zip 文件，并提取和删除该 zip 文件。有两种方法可以做到这一点。

```
COPY <filename>.zip <copy_directory>
RUN unzip <filename>.zip
RUN rm <filename>.zip 
```

Enter fullscreen mode Exit fullscreen mode

或者在一个运行块中:

```
RUN curl <file_download_url> -O <copy_directory> \
&& unzip <copy_directory>/<filename>.zip -d <copy_directory> \
&& rm <copy_directory>/<filename>.zip 
```

Enter fullscreen mode Exit fullscreen mode

第一种方法将创建三层，也将包含不需要的。压缩图像，这也将增加图像的大小。然而，第二种方法仅产生单一层，因此是优选的最佳方法，只要最小化层数是最高优先级。然而，它有一个缺点，对任何一条指令的更改都会导致所有指令再次执行——`docker build`缓存机制会避免这种情况。选择最适合你情况的策略。

有关更多详细信息，请参见[编写 Dockerfiles 的最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)。

然而，有时在一个`RUN`块中执行所有命令会使脚本更加不透明，尤其是当试图混合和匹配`&&`和`||`语句时。另一种语法是像平常一样使用行继续符，但是明确地打开 shell 的“出错时退出”模式。

```
RUN set -e ;\
    echo 'successful!' ;\
    echo 'but the next line will exit: ' ;\
    false ;\
    causing this line not to run

# now you can use traditional shell flow of control without worry:
RUN set -e ;\
    echo 'next line will take evasive action' ;\
    if false; then \
      echo 'it seems that was false' >&2 ;\
    fi ;\
    echo 'and the script continues' 
```

Enter fullscreen mode Exit fullscreen mode

# 使用多阶段构建

对于多阶段构建，您可以在 docker 文件中使用多个`FROM`语句。每条`FROM`指令可以使用不同的基础，并且它们中的每一条都开始了构建的新阶段。您可以有选择地将工件从一个阶段复制到另一个阶段，在最终图像中留下所有不想要的东西。为了展示这是如何工作的，让我们来看看 Dockerfile:

```
FROM golang:1.7.3
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=0 /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"] 
```

Enter fullscreen mode Exit fullscreen mode

# 最小化图层尺寸

有些安装会创建不需要的数据。尝试删除图层中不必要的数据:

```
RUN yum install -y epel-release && \
    rpmkeys --import file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 && \
    yum install -y --setopt=tsflags=nodocs bind-utils gettext iproute\
    v8314 mongodb24-mongodb mongodb24 && \
    yum -y clean all 
```

Enter fullscreen mode Exit fullscreen mode

# 避免 CMD 中的 npm 和 pm2

创建映像时，可以绕过 package.json 的 start 命令，直接将它烘焙到映像本身中。首先，这减少了容器内部运行的进程数量。其次，它会导致退出信号(如 SIGTERM 和 SIGINT)被 Node.js 进程接收，而不是被 npm 接收。

```
CMD ["node","index.js"] 
```

Enter fullscreen mode Exit fullscreen mode

仅运行环境变量
如果需要在`RUN`块中设置一个环境变量，但这要么是不必要的，要么可能会破坏下游映像，那么可以在`RUN`块中设置该变量，而不是使用`ENV`在映像中全局声明它:

```
RUN export DEBIAN_FRONTEND=noninteractive ;\
    apt-get update ;\
    echo and so forth 
```

Enter fullscreen mode Exit fullscreen mode

# 标注

使用标签来引用图像的特定版本。

标签可以用来表示特定的 Docker 容器图像。因此，标记策略必须包括来自 CI 服务器(例如 Jenkins)的唯一计数器`build id`，以帮助识别正确的图像。

更多详细信息见[标签命令](https://docs.docker.com/engine/reference/commandline/tag/)。

# 原木旋转

使用`--log-opt`允许日志旋转。如果您正在创建的容器过于冗长，并且由于持续的部署过程而创建得过于频繁，那么这将很有帮助。更多详细信息，参见[日志驱动选项](https://docs.docker.com/engine/admin/logging/overview/#/json-file-options)。

[![How to host a coder dinner-party](img/d882fc2f242ea184bf29482c6e2003da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NQhs2tiX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.commitstrip.com/wp-content/uploads/2016/06/Strip-Discussion-Docker-english650final-1.jpg)

*原文发布于此:[https://medium . com/dev gorilla/docker-best-practices-images-98e 9464 cc 173](https://medium.com/devgorilla/docker-best-practices-images-98e9464cc173)T3】*