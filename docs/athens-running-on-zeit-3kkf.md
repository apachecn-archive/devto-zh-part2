# 时代的雅典

> 原文：<https://dev.to/arschles/athens-running-on-zeit-3kkf>

我是时代的崇拜者已经很久了。除了技术部分(这真的很好)，他们的平台给人们提供了非常简单、易懂和(最重要的)值得信赖的工具来将真正的应用程序投入生产。我希望 Kubernetes 有这样的东西已经有一段时间了(我甚至写过这样的文章，但我跑题了。)

有一段时间，该平台仅用于节点应用程序，但容器是你们所有人的东西，Zeit 现在允许你将它们部署到它的平台上(他们在 8 月中旬宣布了这一消息)。它的功能与 Node 完全相同，但 Docker:

1.  写一个文档
2.  运行`now`
3.  利润

第 2 步中有很多内容，但最基本的是:

*   将 Dockerfile 文件和上下文推向时代
*   Zeit 在他们的基础设施上做`docker build`
*   Zeit 将图像推送到他们的注册中心(我假设他们运行一个内部注册中心，但不知道是不是真的)
*   Zeit 将映像放入他们的服务基础设施中，进行配置，进行路由，~~检查您是否付费，如果您有，它会为您提供所有令人惊叹的硬件，或者在 RaspberryPi~~ 上运行您的映像，并在他们的服务器上运行您的映像

我的意思是，我以前写过关于 ACR 建造的博客，但这是下一个级别。一个命令给你一切。

[![yess](../Images/9dfb7f45230c1ac04823989e122b423c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HZbuzJI---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/72253ecacc740ac1b30979545713b146cca6b2ae/5b0af/images/athens-zeit/yessss.jpg)

# 雅典-ing it Up

我知道有一个 [rad 项目](https://docs.gomods.io/)。一些关于围棋和依赖 IDK 的东西，但是它有一个`Dockerfile`，你可以用它做一些很酷的事情。那么，为什么不看看 Zeit 是否能对付这种野兽呢？？

我不得不稍微调整一下 docker 文件，因为 CLI ( `now`)不允许您指定自定义的`Dockerfile`位置(我不认为？？？)但差不多就是这样了(查看[的公关](https://github.com/arschles/athens/pull/2)。)现在我可以这样做:

```
➜  athens git:(zeit-now) now --docker
> Deploying ~/github/athens under arschles
> Your deployment's code and logs will be publicly accessible because you are subscribed to the OSS plan.

> NOTE: You can use `now --public` or upgrade your plan (https://zeit.co/account/plan) to skip this prompt
> https://athens-ukiespgiyz.now.sh [in clipboard] (sfo1) [5s]
> Building…
> Sending build context to Docker daemon  37.83MB
> Step 1/16 : FROM golang:1.11-alpine AS builder
>  ---> 95ec94706ff6
> Step 2/16 : RUN mkdir /proj
>  ---> Using cache
>  ---> 7690d95ac3b4
> Step 3/16 : WORKDIR /proj
>  ---> Using cache
>  ---> 89a1e2d291ec
> Step 4/16 : COPY . .
>  ---> 6fdf77ac18ec
> Step 5/16 : ENV GO111MODULE=on
>  ---> Running in 61601842b526
> Removing intermediate container 61601842b526
>  ---> 75be7b20b225
> Step 6/16 : ENV GOPROXY=https://microsoftgoproxy.azurewebsites.net
>  ---> Running in 8e92f2e31291
> Removing intermediate container 8e92f2e31291
>  ---> 8f7e12c7fef4
> Step 7/16 : RUN GO111MODULE=on CGO_ENABLED=0 go build -o /bin/athens-proxy ./cmd/proxy
>  ---> Running in b25589823d39
> go: finding github.com/fatih/color v1.7.0
> go: finding github.com/golang/glog v0.0.0-20160126235308-23def4e6c14b
> go: finding github.com/minio/minio-go v6.0.5+incompatible
> go: finding github.com/gobuffalo/mw-csrf v0.0.0-20180802151833-446ff26e108b
> go: finding github.com/tinylib/msgp v1.0.2
[SNIP]
> go: downloading github.com/markbates/goth v1.46.0
> go: downloading github.com/rs/cors v1.5.0
> go: downloading github.com/gobuffalo/buffalo v0.13.1
> go: downloading github.com/unrolled/secure v0.0.0-20181005190816-ff9db2ff917f
> go: downloading github.com/mitchellh/go-homedir v1.0.0
[SNIP]
> Removing intermediate container b25589823d39
>  ---> d61630e38cd9
> Step 8/16 : FROM alpine
>  ---> 196d12cf6ab1
> Step 9/16 : ENV GO111MODULE=on
>  ---> Using cache
>  ---> cd147951c9a7
> Step 10/16 : COPY --from=builder /bin/athens-proxy /bin/athens-proxy
>  ---> Using cache
>  ---> a462fc7bd78b
> Step 11/16 : COPY --from=builder /proj/config.dev.toml /config/config.toml
>  ---> 3c99d68507fe
> Step 12/16 : COPY --from=builder /usr/local/go/bin/go /bin/go
>  ---> bcac2f09281a
> Step 13/16 : RUN apk update &&      apk add --no-cache bzr git mercurial openssh-client subversion procps fossil &&     mkdir -p /usr/local/go
>  ---> Running in 49fd4fa48969
> fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/main/x86_64/APKINDEX.tar.gz
> fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/community/x86_64/APKINDEX.tar.gz
> v3.8.1-38-g898a0bb28a [http://dl-cdn.alpinelinux.org/alpine/v3.8/main]
> v3.8.1-35-ga062ffc9e8 [http://dl-cdn.alpinelinux.org/alpine/v3.8/community]
> OK: 9539 distinct packages available
> fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/main/x86_64/APKINDEX.tar.gz
> fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/community/x86_64/APKINDEX.tar.gz
> (1/33) Installing libbz2 (1.0.6-r6)
> (2/33) Installing expat (2.2.5-r0)
> (3/33) Installing libffi (3.2.1-r4)
> (4/33) Installing gdbm (1.13-r1)
> (5/33) Installing ncurses-terminfo-base (6.1_p20180818-r1)
[SNIP]
> Executing subversion-1.10.0-r0.pre-install
> Executing busybox-1.28.4-r1.trigger
> Executing ca-certificates-20171114-r3.trigger
> OK: 123 MiB in 46 packages
> Removing intermediate container 49fd4fa48969
>  ---> e8a15877e355
> Step 14/16 : ENV GO_ENV=production
>  ---> Running in ac91ac08096a
> Removing intermediate container ac91ac08096a
>  ---> 096f14de684e
> Step 15/16 : EXPOSE 3000
>  ---> Running in eb60ce775db6
> Removing intermediate container eb60ce775db6
>  ---> 0075a1cdb3c5
> Step 16/16 : CMD ["athens-proxy", "-config_file=/config/config.toml"]
>  ---> Running in 33e543286967
> Removing intermediate container 33e543286967
>  ---> 211b19e9d4a4
> Successfully built 211b19e9d4a4
> Successfully tagged build:RWio8EYjdNbuK5HXd31rKHAp_1540411816
> ▲ Assembling image
> ▲ Storing image (64.0M)
> Build completed
> Verifying instantiation in sfo1
> [0] buffalo: Unless you set SESSION_SECRET env variable, your session storage is not protected!
> [0] time="2018-10-24T20:12:29Z" level=info msg="Exporter not specified. Traces won't be exported"
> [0] buffalo: Starting application at :3000
> ✔ Scaled 1 instance in sfo1 [14s]
> Success! Deployment ready 
```

这是用一个多阶段的建筑在一个 Dockerfile 文件中，在 Zeit 的基础设施上建造雅典。我创建它是因为我使用了一个[托管的雅典模块代理](https://github.com/gomods/athens/issues/772)(更新:现在在 https://athens.azurefd.net有一个更可靠的服务器)在 Zeit 的基础设施上构建雅典。

[![athens arch](../Images/2037357430d4e6358d3caa5396a363fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mzqbRPDm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/579517d79133bb68bc7cc870785c2c9e71a506e2/5bfad/images/athens-zeit/diagram.png)

下面是它部署后的样子:

```
➜  athens git:(zeit-now) now list
> 5 total deployments found under arschles [314ms]
> To list more deployments for an app run `now ls [app]`

  app       url                         inst #    type      state    age
  athens    athens-ukiespgiyz.now.sh         -    DOCKER    READY    4m 
```

在 zeit.co 网站上也有这一切的用户界面。他们甚至将构建日志流式传输到站点上#AMAZE

# 试一试

所以基本上我运行了几个命令，在 CLI 上得到了大量输出。机器在执行我的命令；成功！

现在它运行了，我想看看它表现如何。我从小处入手`bld` ( [博客文章](https://arschles.com/blog/az-acr-build-or-docker-build/)了解该工具的背景，如果你感兴趣的话):

```
➜  bld git:(master) export GOPROXY=https://athens-ukiespgiyz.now.sh
➜  bld git:(master) sudo rm -r $GOPATH/pkg/mod
➜  bld git:(master) time go build
go: finding github.com/magefile/mage v1.4.0
go: finding github.com/tdewolff/parse v2.3.3+incompatible
go: finding github.com/spf13/cobra v0.0.3
go: finding github.com/mitchellh/go-homedir v1.0.0
go: finding github.com/mattn/go-isatty v0.0.3
go: finding github.com/spf13/viper v1.2.1
go: finding github.com/dlclark/regexp2 v1.1.6
go: finding github.com/tdewolff/minify v2.3.5+incompatible
go: finding github.com/spf13/pflag v1.0.3
go: finding github.com/magiconair/properties v1.8.0
go: finding gopkg.in/yaml.v2 v2.2.1
go: finding github.com/fsnotify/fsnotify v1.4.7
go: finding golang.org/x/text v0.3.0
go: finding golang.org/x/sys v0.0.0-20180906133057-8cf3aee42992
go: finding github.com/pelletier/go-toml v1.2.0
go: finding github.com/spf13/jwalterweatherman v1.0.0
go: finding github.com/mitchellh/mapstructure v1.0.0
go: finding github.com/spf13/pflag v1.0.2
go: finding gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405
go: finding github.com/spf13/afero v1.1.2
go: finding github.com/spf13/cast v1.2.0
go: finding github.com/hashicorp/hcl v1.0.0
go: finding github.com/davecgh/go-spew v1.1.1
go: downloading github.com/spf13/viper v1.2.1
go: downloading github.com/spf13/cobra v0.0.3
go: downloading github.com/mitchellh/go-homedir v1.0.0
go: downloading github.com/magefile/mage v1.4.0
go: downloading github.com/mitchellh/mapstructure v1.0.0
go: downloading github.com/hashicorp/hcl v1.0.0
go: downloading github.com/pelletier/go-toml v1.2.0
go: downloading gopkg.in/yaml.v2 v2.2.1
go: downloading github.com/spf13/jwalterweatherman v1.0.0
go: downloading github.com/magiconair/properties v1.8.0
go: downloading github.com/fsnotify/fsnotify v1.4.7
go: downloading github.com/spf13/pflag v1.0.3
go: downloading github.com/spf13/cast v1.2.0
go: downloading github.com/spf13/afero v1.1.2
go: downloading golang.org/x/sys v0.0.0-20180906133057-8cf3aee42992
go: downloading golang.org/x/text v0.3.0
go build  4.97s user 2.06s system 7% cpu 1:32.16 total 
```

所以，花了点时间。建造雅典本身也花了很长时间(大约 6:30)，但有几个原因，以及从举办雅典的经验中获得大的加速的方法:

1.  使用多个实例运行此程序
2.  把这个接到外部存储器上
    *   无论如何，您都需要这样做才能让#1 工作
    *   这是使用本地磁盘，我不知道它在 Zeit 上有多快
3.  用东西在雅典的贮藏处预先播种
    *   我展示的所有东西都来自一个冷的本地(对那里的地鼠来说)和托管(在 Zeit Athens 服务器上)缓存

总而言之，这是一个相当不错的平台。我觉得很棒。不是超级 rad -那是为 [clippy](https://en.wikipedia.org/wiki/Office_Assistant) 级别的东西保留的，但是比半 rad 高很多。

[![clipyyyy](../Images/7faa7eff4dfef004717e90c26f004388.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AWEDxtI8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/0deafaa078d06808cf0c9b6abcb4b9e8272f6359/976c1/images/athens-zeit/clippy.jpg)

继续摇滚每个人！