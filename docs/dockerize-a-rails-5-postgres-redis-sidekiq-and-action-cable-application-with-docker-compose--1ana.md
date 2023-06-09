# Dockerize 一个 Rails 5，Postgres，Redis，Sidekiq 和 Action Cable 应用程序

> 原文：<https://dev.to/nickjj/dockerize-a-rails-5-postgres-redis-sidekiq-and-action-cable-application-with-docker-compose--1ana>

**本文最初发布于 2016 年 7 月 8 日:[https://nickjanetakis . com/blog/dockerize-a-rails-5-postgres-redis-sidekiq-action-cable-app-with-docker-compose](https://nickjanetakis.com/blog/dockerize-a-rails-5-postgres-redis-sidekiq-action-cable-app-with-docker-compose)**

* * *

完成本教程后，您将了解使用 Docker 的好处，并能够:

*   在 5 分钟或更短时间内在所有主要平台上安装 Docker
*   运行一个使用大量最佳实践的示例 Rails 5+应用程序
*   知道如何写文档吗
*   使用 Docker 编写运行多个 Docker 容器

## Docker 是什么，为什么有用？

Docker 允许你将一个应用或服务及其所有的依赖项打包成一个标准化的单元。该单元通常被标记为 Docker 图像。

应用程序运行所需的一切都包括在内。Docker 镜像包含代码、运行时、系统库和其他任何东西，如果你没有使用 Docker，你可以安装在服务器上使它运行。

为了更好地了解 Docker 将如何影响你作为一名软件开发人员的日常工作，我强烈推荐你阅读我以前的一篇博文，这篇博文将通过使用 Docker 把你从多年的混乱中拯救出来。

## 安装对接器

我们将使用的代码库与 Docker 17.06 和 Docker Compose 1.14 兼容，我相信它将继续与 Docker 的未来版本兼容，所以请放心安装这两个版本的最新版本。

**更新:**Docker v 18.04 和 Docker Compose 1.21 一切正常！

本指南希望您已经安装了 Docker。如果你是零基础，那么你可能想注册这篇文章底部的免费 Docker 电子邮件课程，因为它涵盖了在 Mac、Windows 和 Linux 上安装 Docker 的无数方法。

否则，如果你想现在就开始安装 Docker，请随意查看 Docker 的文档。为了帮助你，我还写了一个关于 Mac / Windows 的 [Docker 和 Docker Toolbox](https://nickjanetakis.com/blog/should-you-use-the-docker-toolbox-or-docker-for-mac-windows) 的比较指南。

#### 确保 Docker 和 Docker Compose 正在工作

在继续之前，你应该看到我所看到的，或者非常类似的东西:

```
docker --version
> Docker version 17.06.0-ce, build 02c1d87

docker-compose --version
> docker-compose version 1.14.0, build c7bdf9e 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 Rails 应用

这篇博客文章的重点是 Dockerizing 一个 Rails 应用程序，它有
个移动部件。我们可以使用`rails new`来创建一个新项目并构建它，但是使用一个预先制作的应用程序会更快。

我们将使用 orats 提供的一个基础应用程序，它是一个用于生成固执己见的 Rails 项目的
[开源工具。](https://github.com/nickjj/orats)

如果你想了解更多关于这个基础应用所提供的东西，那么我推荐你阅读一下 [orats 的自述文件](https://github.com/nickjj/orats)。这篇文章将只涉及 Docker 的特定方面。

#### 现有的 Rails 应用呢？

本文非常笼统，您可以将将要阅读的内容应用到自己的应用程序中，而不是 orats 示例应用程序。

你要读的东西根本不是针对 orats 的。你只需要
创建几个文件，因为你事先不会有它们。

#### 安装 orats

因为你很可能是一个 Ruby 开发者，你已经在你的
工作站上安装了 Ruby，所以安装就像`gem install orats`一样简单。

##### 生成新项目

```
# Feel free to generate the project anywhere you want.
orats new /tmp/my_dockerized_app 
```

Enter fullscreen mode Exit fullscreen mode

#### 在你喜欢的代码编辑器中打开项目

```
# Move into the project's directory
cd /tmp/my_dockerized_app

# Open the project with your favorite editor (mine is Sublime)
subl . 
```

Enter fullscreen mode Exit fullscreen mode

你可以随意使用任何你想要的编辑器，但是如果你喜欢 Sublime Text 3 并且
你想为 Rails、Docker 等等配置它，那么看看我在[25 Sublime Text 3 Packages for Polyglot Programmers](https://nickjanetakis.com/blog/25-sublime-text-3-packages-for-polyglot-programmers)上的帖子。

## 将 Rails 应用程序文档化

我们需要做一些事情来整理申请。

#### 测井

为了让日志正常工作，Docker 希望您的应用程序或
进程记录到 STDOUT。这是一个非常好的想法，因为管理日志文件的概念现在将从 Rails 应用程序中分离出来。

您可以选择让 Docker 将这些日志条目写入 syslog 或运行在您的服务器上的另一个本地服务，或者您可以将日志输出传送给第三方服务，如 Loggly。

**无论哪种情况，你都需要对你的 Rails 应用做一个小小的调整:**

```
# config/application.rb : Lines 21-24

logger           = ActiveSupport::Logger.new(STDOUT)
logger.formatter = config.log_formatter
config.log_tags  = [:subdomain, :uuid]
config.logger    = ActiveSupport::TaggedLogging.new(logger) 
```

Enter fullscreen mode Exit fullscreen mode

在 orats 基础项目中，我决定在`application.rb`文件中设置日志记录，因为它对所有环境都是通用的。

我们只是将 Rails 设置为记录到 STDOUT，然后我们还设置了一个自定义格式化程序，在每个日志条目中包含子域和 uuid。

我喜欢这样做，因为它使过滤日志变得非常简单——尤其是当你的应用程序变得越来越大并且有很多移动部分的时候。

#### Docker 特定文件

项目的根有几个与 Docker 相关的文件:

```
nick@oriath:/tmp/my_dockerized_app ⚡ ls -la
-rw-rw-r--  1 nick nick    3507 Jul  7 10:50 .env
-rw-rw-r--  1 nick nick    1032 Jul  7 10:50 docker-compose.yml
-rw-rw-r--  1 nick nick    4353 Jul  7 10:50 Dockerfile
-rw-rw-r--  1 nick nick     49  Jul  7 10:50 .dockerignore 
```

Enter fullscreen mode Exit fullscreen mode

唯一需要添加的文件是`Dockerfile`，但是你会发现
大多数支持 Docker 的 web 应用程序都有其他文件。

##### Dockerfile

让我们从`Dockerfile`开始，因为谈论其他文件
需要了解一点 Docker 映像是如何构建的。

你可以把这个文件想象成你的 Docker 图像蓝图或者菜谱。当你运行`docker build`命令时，它将从上到下执行每一行。

它将在 Docker 映像的上下文中运行所有这些命令。

为了更好地理解这份文件，请查看我闪亮的新
[投入 Docker 课程](https://diveintodocker.com/?utm_source=nj&utm_medium=devto&utm_campaign=dockerize-rails)。

```
FROM ruby:2.4-alpine

RUN apk update && apk add build-base nodejs postgresql-dev

RUN mkdir /app
WORKDIR /app

COPY Gemfile Gemfile.lock ./
RUN bundle install --binstubs

COPY . .

LABEL maintainer="Nick Janetakis <nick.janetakis@gmail.com>"

CMD puma -C config/puma.rb 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们可以构建映像，您将能够访问 Rails 应用程序，但我们现在避免这样做。

##### 。码头工人

让我们首先看看下一个文件，即`.dockerignore`文件。

```
.git
.dockerignore
.byebug_history
log/*
tmp/* 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用`COPY . .`命令将当前目录中的所有文件复制到 Docker
映像中时，它将复制几乎所有内容。

这不是世界上最好的主意，因为如果你的项目是 git repo，你将会有大量的额外数据。你应该努力在合理的范围内拥有最小的 Docker 图片。

`.dockerignore`文件与`.gitignore`文件非常相似。它让你
黑名单某些文件夹或文件被包括在内。

##### 码头工-化合物. yml

Docker Compose 是 Docker 提供的官方工具。在其核心，它是一个实用程序，让您“组成”Docker 命令，并以一种简单的方式管理多个容器。

让我们看一下`docker-compose.yml`文件:

```
version: '2'

services:
  postgres:
    image: 'postgres:10.3-alpine'
    volumes:
      - 'postgres:/var/lib/postgresql/data'
    env_file:
      - '.env'

  redis:
    image: 'redis:4.0-alpine'
    command: redis-server --requirepass yourpassword
    volumes:
      - 'redis:/data'

  website:
    depends_on:
      - 'postgres'
      - 'redis'
    build: .
    ports:
      - '3000:3000'
    volumes:
      - '.:/app'
    env_file:
      - '.env'

  sidekiq:
    depends_on:
      - 'postgres'
      - 'redis'
    build: .
    command: sidekiq -C config/sidekiq.yml.erb
    volumes:
      - '.:/app'
    env_file:
      - '.env'

  cable:
    depends_on:
      - 'redis'
    build: .
    command: puma -p 28080 cable/config.ru
    ports:
      - '28080:28080'
    volumes:
      - '.:/app'
    env_file:
      - '.env'

volumes:
  redis:
  postgres: 
```

Enter fullscreen mode Exit fullscreen mode

如果你想了解所有这些是如何联系在一起的，请深入了解 Docker 详细介绍了一切。很多时候，知道“为什么”比看到如何做更重要。这就是让你能够自己应用东西的原因。

##### [T1。包封/包围（动词 envelop 的简写）](#env)

从技术上讲，这个文件不是 Docker 的一部分，但它被 Docker
Compose 和 Rails 应用程序部分使用。

默认情况下，Docker Compose 会在与您的`docker-compose.yml`文件相同的目录
中查找一个`.env`文件。

我们可以在这里设置各种环境变量，如果您的应用程序使用 ENV 变量，您甚至可以在这里添加您的自定义环境变量。

```
COMPOSE_PROJECT_NAME=my_dockerized_app 
```

Enter fullscreen mode Exit fullscreen mode

通过将`COMPOSE_PROJECT_NAME`设置为`my_dockerized_app`，Docker Compose
将自动为我们的 Docker 映像、容器、卷和网络添加前缀`mydockerizedapp`。

碰巧 Docker Compose 会从名字中去掉下划线。

这个`.env`文件中还有很多其他值，但其中大部分是 Rails 应用程序定制的
。我们稍后将讨论一些与 Docker 相关的值。

## 运行 Ruby on Rails 应用

输入:`docker-compose up --build`就可以运行一切。Docker Compose 有许多不同的子命令和标志。你肯定想自己去看看。

在`up`命令完成后，打开一个新的终端选项卡，检查以您的名义创建的内容。

#### Docker 图片

运行`docker images` :

```
mydockerizedapp_cable     latest              ...         392 MB
mydockerizedapp_sidekiq   latest              ...         392 MB
mydockerizedapp_website   latest              ...         392 MB
postgres                  10.3-alpine         ...         39.5 MB
redis                     4.0-alpine          ...         27.5 MB
ruby                      2.5-alpine          ...         60.7 MB 
```

Enter fullscreen mode Exit fullscreen mode

Docker Compose 自动给你拉下 Redis 和 Ruby，然后给你建网站，sidekiq 和 cable images。

#### 码头工人集装箱

运行`docker-compose ps` :

```
Name                        State   Ports          
------------------------------------------------------------------------
mydockerizedapp_cable_1      ...   Up      0.0.0.0:28080->28080/tcp
mydockerizedapp_postgres_1   ...   Up      5432/tcp
mydockerizedapp_redis_1      ...   Up      6379/tcp
mydockerizedapp_sidekiq_1    ...   Up                              
mydockerizedapp_website_1    ...   Up      0.0.0.0:3000->3000/tcp 
```

Enter fullscreen mode Exit fullscreen mode

Docker Compose 自动为您命名了容器，并附加了一个`_1`,因为它运行 Docker 映像的一个实例。Docker Compose 支持缩放，但这超出了本教程的范围。

我们还可以看到服务正在使用和公开哪些端口。

还有很多要讨论的，但上面的内容已经足够了。

#### 查看站点

如果你通过 Docker 工具箱安装了 Docker，那么你需要对`.env`文件做 3 个快速修改。如果您正在本地运行 Docker，那么您现在就可以访问`http://localhost:3000`。

**Docker 工具箱用户需要对`.env`文件进行 3 处修改:**

```
# Open the .env file and find these values:
ACTION_MAILER_HOST=localhost:3000
ACTION_CABLE_FRONTEND_URL=ws://localhost:28080
ACTION_CABLE_ALLOWED_REQUEST_ORIGINS=http:\/\/localhost*

# Replace `localhost` with your Docker Machine IP address:
ACTION_MAILER_HOST=192.168.99.100:3000
ACTION_CABLE_FRONTEND_URL=ws://192.168.99.100:28080
ACTION_CABLE_ALLOWED_REQUEST_ORIGINS=http:\/\/192.168.99.100* 
```

Enter fullscreen mode Exit fullscreen mode

工具箱用户需要将几个实例的`localhost`改为`192.168.99.100`或者你的 Docker 机器的 IP 地址。你可以从一个支持 Docker 的终端运行`docker-machine ip`来确定你的 IP 地址。

这是因为您的系统上没有运行 Docker。这也意味着你需要在浏览器中访问`http://192.168.99.100:3000`。

此时，您会注意到它抛出一个错误，说数据库
不存在。别担心，这是意料之中的，对吗？我们还没有重置数据库。

#### 与 Rails 应用程序交互

博客文章的这一部分有两个目的。它将向您展示如何通过 Docker 运行 Rails 命令，并向您展示如何初始化 Rails 数据库。

*在一个新的支持 Docker 的终端标签中运行以下两个命令*

##### 重置数据库

`docker-compose exec --user "$(id -u):$(id -g)" website rails db:reset`

*   如果你在 OSX 或 Windows 上，不要包括`--user flag`

##### 迁移数据库

`docker-compose exec --user "$(id -u):$(id -g)" website rails db:migrate`

*   如果你在 OSX 或 Windows 上，不要包括`--user flag`

##### 上面的命令是怎么回事？

Docker Compose 有一个`exec`命令，可以让你在已经运行的容器上执行命令。我们在`website`容器上运行命令。

`--user "$(id -u):$(id -g)"`标志确保由`exec`命令生成的任何文件最终归用户所有，而不是归 root 所有。

#### 一个活的开发环境

由于我们在`docker-compose.yml`中设置了卷，您将能够
主动开发您的应用程序，就像它在您的操作系统上本地运行一样。

尝试转到`app/views/pages/home.html.erb`，然后对文件进行更改。
您所要做的就是保存文件并重新加载浏览器以查看更改。

#### 关机

您将需要转到 Docker 编写终端选项卡并按下`CTRL+C`。然后为了更好的测量，输入`docker-compose stop`。有时编写 bug 并不会自动停止所有的容器。

#### 结论

Docker 很牛逼。现在，您可以在其他平台上运行您的项目，而不必担心依赖性和特定于平台的问题。

您甚至可以毫不费力地将项目部署到生产环境中。

如果你已经准备好掌握 Docker，那就去参加 Docker 课程吧。