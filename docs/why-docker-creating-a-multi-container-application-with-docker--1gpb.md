# 为什么是 Docker？用 docker 创建多容器应用程序

> 原文：<https://dev.to/abiodunjames/why-docker-creating-a-multi-container-application-with-docker--1gpb>

[![Multi Container Application](img/018de0539740f213239c991119a4fa8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HnGD-tRp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/samueljames/image/upload/v1525300995/multi-container_application.jpg)

如果你刚刚开始学习 docker，脑海中出现的一个常见问题是 docker 是什么以及为什么需要它。在这篇文章中，我提供了这些问题的基本答案，并最终带你完成用 docker-compose 编写你的第一个多容器应用程序。

## 目录

1.  **Docker 和 why**
2.  **Docker 图像**
3.  **坞站组成**
4.  **使用 docker-compose 构建多容器应用**
5.  **延伸阅读**

## Docker 又为什么？

我个人喜欢的关于 docker 的定义是由 docker 团队提供的，他们将 docker 定义为一个开放平台，供开发人员和系统管理员构建、发布和运行分布式应用程序，无论是在笔记本电脑、数据中心虚拟机还是云中。

### 保持一致的环境很难

如果没有虚拟化技术，就不能保证应用程序的工作方式与它们在一个环境中的工作方式完全相同。这主要是由于不一致的环境和系统库中的差异。

您可以使用`python3`开发您的应用程序，却发现生产环境正在运行`python2`。当你不能升级到`python3`时，事情变得有点复杂，因为主机上的其他应用程序需要`python2`才能运行。

开发人员辛辛苦苦地工作了几个小时，寻找不一致环境的解决方案，然而在大多数情况下，他们出色的代码不会继续运行，因为一个库文件已经过时。

斗争是无止境的。我经历过这种痛苦，它伴随着对在一种环境下工作正常而在另一种环境下工作不正常的功能进行故障诊断。耶！，它在我们的机器上总是工作得很好，但在生产中却从来没有！:(

### 在本地机器上安装应用程序依赖项需要时间

如果没有虚拟化，设置开发环境可能会很困难，我们经常发现自己安装不同版本的软件来支持我们正在工作的各种服务。维护不同版本的 PHP 或 MySQL 数据库会占用你大量的时间，而这些时间本可以用来编写代码。

容器和虚拟机解决了这个问题，通过将应用程序及其依赖项隔离到一个可以在任何地方可靠运行的独立单元中，减轻了您的压力。

虽然虚拟机和容器有相同的目标，但容器比虚拟机更受欢迎，因为它们轻量级、速度快、资源利用率低。

在这种情况下，集装箱在功能上类似于你在公路上看到的集装箱:它们都是用于容纳产品的外壳。区别在于他们持有的内容。我们持有软件产品。

为数不多的受欢迎的容器提供者之一是 [Docker](https://docker.com) ，在接下来的几段中，我们将详细探讨如何将开发环境 Docker 化。

## Docker 图像

Docker 容器是从 docker 映像构建的。映像是惰性的、不可变的文件，本质上是容器的快照。我喜欢将 docker 容器视为 docker image 的运行实例。

图像是从`Dockerfile`开始构建的，它基本上是一个包含 docker 应该如何构建图像的说明的文本文档。

## 复合坞站

Docker-compose 是一款帮助你运行复杂应用的工具。
一个有效的软件从来都不是一个孤岛，它很可能依赖于一系列其他东西，比如数据库、缓存甚至其他服务。最好的做法是将它们分别放在各自的容器中。使用 docker-compose，您可以在一个文件中定义一个多容器应用程序，该应用程序可以在一个命令中启动。

## 使用 docker-compose 构建多容器应用程序

在这篇文章变得令人难以忍受的长之前，让我们把手弄脏吧。
如果你没有安装 docker 和 docker-compose，[点击](https://www.docker.com/community-edition)获取安装说明。
我们将为我们的项目组合多个服务。这是一个 laravel 应用程序，使用 MySQL 数据库和 Redis 进行缓存。

###开始新的 Laravel 项目

通过运行`composer create-project --prefer-dist laravel/laravel laravelApp`
启动一个 Laravel 项目。如果一切正常，您的目录应该如下所示。
T3![Project-directory](img/1708ca10391a0987784eab430b9a0790.png)T5】

###为 web 服务器创建 docker file(Nginx)
我们需要一个 web 服务器(Nginx)来提供静态内容。我们创建一个`Dockerfile`，它基本上是一个文本文件，包含 docker 应该如何构建我们的图像的指令。
在项目根目录下，我们创建一个包含以下内容的`web.docker`文件。

```
 # /project/root/web.docker

 # start building image from nginx 1.13
 FROM nginx:1.13

 # copy site.conf from local directory to /etc/nginx/conf.d/default.conf
 ADD ./site.conf /etc/nginx/conf.d/default.conf

 WORKDIR /var/www 
```

Enter fullscreen mode Exit fullscreen mode

`site.conf`包含项目根目录下 nginx
的虚拟主机配置，用这个内容
创建`site.conf`

```
# /project/root/site.conf

server {
    listen 80;
    index index.php index.html;

    access_log /var/www/storage/logs/access.log;
    error_log /var/www/storage/logs/error.log;
    root /var/www/public;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Headers' 'Authorization,DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,api_key' always;
        add_header 'Access-Control-Allow-Methods' 'POST, GET, PUT, DELETE' always;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为 PHP 7.1 创建 Dockerfile

Laravel 需要 PHP 以及一些特定的 PHP 扩展才能运行。我们创建了一个名为`app.docker`的文件，其中包含了我们想要的 PHP 版本和扩展。
同样在项目根目录下创建它。

```
FROM php:7.1.9-fpm

# Install any custom system requirements here
RUN apt-get update && apt-get install -y libmcrypt-dev zip unzip git mysql-client \
    && docker-php-ext-install mcrypt pdo_mysql

WORKDIR /var/www 
```

Enter fullscreen mode Exit fullscreen mode

至于 MySQL 数据库和 Redis，预构建的映像足以满足本演示。我们不必定制它们。

### 在 Docker-compose 文件中定义服务

现在，我们创建一个`docker-compose.yml`。这应该在项目的根目录下。

```
version: '2'
services:
 web:
  build:
   context: ./
   dockerfile: web.docker
  container_name: web
  ports:
   - "8080:80"
  volumes:
   - ./:/var/www
  links:
   - app
 app:
  build:
   context: ./
   dockerfile: app.docker
  volumes:
   - ./:/var/www
  depends_on:
   - mysql
   - redis
  environment:
   - "APP_ENV=${APP_ENV}"
   - "APP_DEBUG=${APP_DEBUG}"
   - "APP_KEY=${APP_KEY}"
   - "DB_CONNECTION=${DB_CONNECTION}"
   - "DB_HOST=${DB_HOST}"
   - "DB_PORT=${DB_PORT}"
   - "DB_DATABASE=${DB_DATABASE}"
   - "DB_USERNAME=${DB_USERNAME}"
   - "DB_PASSWORD=${DB_PASSWORD}"
   - "CACHE_DRIVER=${CACHE_DRIVER}"
   - "QUEUE_DRIVER=${QUEUE_DRIVER}"
   - "REDIS_HOST=${REDIS_HOST}"
   - "REDIS_PASSWORD=${REDIS_PASSWORD}"
   - "REDIS_PORT=${REDIS_PORT}"
 mysql:
  image: mysql:5.7
  environment:
   MYSQL_DATABASE: ${DB_DATABASE}
   MYSQL_USER: ${DB_USERNAME}
   MYSQL_PASSWORD: ${DB_PASSWORD}
  volumes:
    - ./mysql:/var/lib/mysql
  ports:
   - "3306:3306"
 redis:
  image: redis:3.0
  ports:
    - "6379:6379"
  volumes:
   - ./redis:/data 
```

Enter fullscreen mode Exit fullscreen mode

让我们花一点时间来理解上面`docker-compose.yml`的内容。

我们正在使用的 docker-compose 语法的版本。

服务是我们应用程序的不同部分，例如(app，redis，mysql)。

`build`:它定义了一个字符串，该字符串包含构建上下文的路径和引用我们的 dockerfile 的 Dockerfile 参数

`container_name`:用于指定容器建成后的名称

`environment`:用于将环境变量传递给容器。

> **注意:** Docker compose 会让变量在[中定义。服务配置中可用的 env](https://docs.docker.com/compose/environment-variables/#the-env-file) 。这意味着您可以在`docker-compose.yml`中自动访问`.env`文件中的 laravel 配置。

`image`:用于指定一个预构建的映像，从这个映像开始构建容器。

`Ports`:用于将容器的端口映射到主机的端口。在`app`服务中，我们将主机上的端口`8080`映射到容器中的端口`80`。

`volumes`:用于将容器的目录挂载到主机的目录。在这个例子中，docker 将在`app`服务中创建`/var/www`目录，并将本地目录中的文件指向它。这在开发过程中非常有用，如果您对源代码进行了更改，这些更改会立即生效。

`depends_on`:确保`mysql`和`redis`在`app`服务之前启动。

在执行期间，docker-compose 在容器之间构建了一个网络，这样每个容器都可以通过使用在`docker-compose.yml`中定义的名称来相互引用。

### 创建一个 DockerIgnore 文件

一个`dockerignore`文件类似于`gitignore`文件。使用`dockerignore`文件，您可以指定从构建中排除的文件和文件夹，这些文件不会被复制到映像中。

让我们用
下面的代码在根文件夹中创建一个`dockerignore`文件

```
app.docker
web.docker
docker-compose.yml
.env 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Compose 构建并运行项目

为了构建项目，我们导航到项目根目录并运行:

```
docker-compose build 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一个类似这样的控制台输出:
[![Docker-compose build console output](img/33ebca9a5193320c65850cf7e16612bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TFENlhjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/samueljames/image/upload/c_fit%2Cw_651/v1525294761/docker-compose_build.png)

现在，我们可以继续运行我们的服务了:

```
docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

为了验证这些服务确实在运行，我们发出了`docker ps`命令。这将列出所有正在运行的容器。

```
 docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
7275af6e2f3f        laravelapp_web      "nginx -g 'daemon of…"   9 seconds ago       Up 6 seconds        0.0.0.0:8080->80/tcp     web
2ce0817bd07e        laravelapp_app      "docker-php-entrypoi…"   10 seconds ago      Up 8 seconds        9000/tcp                 laravelapp_app_1
5a85911e6ecd        mysql:5.7           "docker-entrypoint.s…"   58 seconds ago      Up 11 seconds       0.0.0.0:3306->3306/tcp   laravelapp_mysql_1
41739940ec82        redis:3.0           "docker-entrypoint.s…"   58 seconds ago      Up 10 seconds       0.0.0.0:6379->6379/tcp   laravelapp_redis_1 
```

Enter fullscreen mode Exit fullscreen mode

最后，将你的网络浏览器指向`http://localhost:8080/`，你应该会看到一个默认的 laravel 主页，如下所示。

[![Laravel-home-page](img/15f98f422bbd863cfe88fd7a656da9f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oOkiEEyd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/samueljames/image/upload/v1525296644/laravel-home-page.png) 
因为我们已经将项目的目录挂载到应用程序容器的目录中，所以对源代码所做的更改会自动应用。

## 进一步阅读

[Docker 备忘单](https://github.com/wsargent/docker-cheat-sheet)
[撰写入门](https://docs.docker.com/compose/gettingstarted/)
[Docker 入门:初学者的关键概念](https://www.udemy.com/docker-quick-start/)

你有评论或注意到一个错误吗？请随意将它们放在这里，或者通过 [Linkedin](https://www.linkedin.com/in/samuel-james-abiodun/) 与我联系