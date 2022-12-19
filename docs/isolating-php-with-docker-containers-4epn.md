# 用 Docker 容器隔离 PHP

> 原文：<https://dev.to/johnmccuk/isolating-php-with-docker-containers-4epn>

Docker 是一项伟大的技术。这是一个很好的方法来建立一个开发环境或试验新软件，而不会堵塞你自己的系统。

我用 Apache 和 PHP 的 Docker 服务了一些网页。我怎样才能容易地切换 PHP 版本而不堵塞我的本地机器或者创建多个 Docker 镜像/流浪者盒子？我相信很多开发人员可以很容易地找到解决方案，但是我必须承认我在构思容器解决方案时有困难。抽象的层次造成了精神上的障碍，我确信我不是一个人有这种问题。

我觉得 Apache/PHP 不符合 Docker ***每个容器一个进程*** 的原则。PHP 和 Apache 如此紧密地结合在一起，以至于考虑到[关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns)，这是不可能的。

我真的希望我的 web 服务器容器与编程语言隔离开来。切换到 NGINX 和 PHP-FPM 提供了更清晰的分离，这使我们可以轻松地切换语言版本。如果您有需要不同版本的旧网站，或者您想在升级前试用一个网站，这可能会很方便。

## 设置

所以计划是，当一个请求被发送到 test5.com/index.php 的 url 时，Nginx 容器将把处理 index.php 的请求传递到一个运行 PHP 5 的容器上，显然相当于 test7.com/index.php 的和 PHP 7 的。

首先，向`/etc/hosts`文件添加 2 个条目:

```
127.0.0.1 test5.com
127.0.0.1 test7.com 
```

Enter fullscreen mode Exit fullscreen mode

这些将是我们用来访问 Nginx 容器的地址。

从 GitHub[docker-PHP-isolation-example](https://github.com/johnmccuk/docker-php-isolation-example)下载或者克隆这个库到你系统中合适的地方。所有命令都将从该文件夹中的 CLI 运行。

文件夹结构相对简单:

*   **webroot** -包含一个名为`index.php`的文件的自解释文件夹，该文件包含`phpinfo()`命令。

*   **site.conf** - Nginx 站点配置文件。

*   **坞站-复合. yml** 坞站设置配置文件。

*docker-compose.yml* 文件设置了 3 个容器，都基于 Alpine Linux 映像，所以容器的总容量只有 120Mb。

```
version: '2'
services:
    web:
        image: nginx:alpine
        ports:
            - "8080:80"
        volumes:
            - ./webroot:/var/www/html
            - ./site.conf:/etc/nginx/conf.d/site.conf
        links:
            - php7
            - php5
    php7:
        image: php:7-fpm-alpine
        volumes:
            - ./webroot:/var/www/html
    php5:
        image: php:5-fpm-alpine
        volumes:
            - ./webroot:/var/www/html 
```

Enter fullscreen mode Exit fullscreen mode

主容器是 Nginx 代理，它是唯一具有外部端口(8080)的容器。这个容器代理其他两个容器的任何有效请求，一个运行 php 7 php-fpm，另一个在端口 9000 上运行 php 5 php-fpm。

[![phpIsolationDiagram](../Images/34d80ab71999edbe37fc8671e9a20b46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JirZ8p31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/phpIsolationDiagram-a7498ea5f35c8306a74a36f106161f27-0c130.png)

所有 3 个容器共享 *webroot* 文件夹，Nginx 需要它来服务任何非 PHP 文件，而 PHP 容器需要访问 PHP 文件。

文件包含定义 Nginx 如何处理请求:

```
server {
    listen 80;
    index index.php;
    server_name test7.com;
    root /var/www/html/;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php7:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}

server {
    listen 80;
    index index.php;
    server_name test5.com;
    root /var/www/html/;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php5:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我确信有一种方法可以将这两个地址合并到一个地址中，并使用正则表达式，但是生命太短暂了。

## 奔跑吧

在 CLI 中，导航到您下载存储库的文件夹，并键入`docker-compose up -d`。

这将下载图像，如果没有保存在本地，并启动容器。完成后，键入`docker ps`以确保 3 个容器正在运行。

所以打开你选择的浏览器，输入 test5.com:8080，你应该会看到 PHP 5 的 phpinfo()。

[![Screen-Shot-2017-10-07-at-08.04.07](../Images/d3af3bdb8535e0e0f96a6dcc43db683d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2AR4VGSe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/Screen-Shot-2017-10-07-at-08.04.07-c5d55953237557557f278eafbd413f26-e7328.png)

接下来，输入 test7.com:8080，你应该会看到 phpinfo()，你猜对了，是 PHP 7！

[![Screen-Shot-2017-10-07-at-08.03.49](../Images/def50af5067726e41b5efc0a26438a0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0nvK7mPl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/Screen-Shot-2017-10-07-at-08.03.49-fa6fd10b5835979f416664078d8ea1e9-29566.png)

这就对了，我们可以定义使用哪个版本的 PHP 来处理同一个 index.php 文件，非常有趣...

## 收拾碗筷

刺激够了吗？要关闭正在运行的容器，键入`docker-compose down`，这将关闭并移除容器。

如果要删除下载的图片，先输入`docker images`，再输入`docker rmi IMAGE ID`，例如`docker rmi 4fe1b7f08eb1 8b057b9de580 1b95155d2daf`会删除 id 为 *4fe1b7f08eb1* 、 *8b057b9de580* 和 *1b95155d2daf* 的 3 张图片。

## 终于

将 PHP 作为一种“软件即服务”来提供的概念可能是一个卑微的想法，但我觉得这真的很酷。能够隔离编程语言的版本让我非常感兴趣，这可能是我在未来项目中尝试和整合的事情。

<sub><sup>用 Docker 容器隔离 PHP 第一次出现在我的[博客网站](https://blog.john-mccracken.com)上是在 2017 年 7 月 10 日。</sup></sub>