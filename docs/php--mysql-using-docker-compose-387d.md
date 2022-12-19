# PHP + MySQL 使用 Docker Compose

> 原文：<https://dev.to/alysivji/php--mysql-using-docker-compose-387d>

*这篇[帖子](https://alysivji.github.io/php-mysql-docker-containers.html)最初发表于 [Siv 脚本](https://alysivji.github.io/)T5*

成为一名软件工程师不仅仅是在特定的编程语言上有效，而是能够使用手边的工具解决任何给定的问题。

这个星期在工作中，我必须扩展一个 WordPress 插件的功能，这样它才能适应我们基于微服务的后端架构。这意味着学习足够的 PHP 来编写一些生产级代码。

我不想在我的本地机器上安装 PHP，所以这是 [Docker](https://www.docker.com/) 的完美用例！在这个[快速点击](https://alysivji.github.io/category/quick-hits.html)中，我将描述如何使用 Docker Compose 创建一个容器化的 PHP + MySQL 开发环境。

[灯堆](https://en.wikipedia.org/wiki/LAMP_(software_bundle))回来了！

* * *

### 指令

*   我们首先为这个项目创建一个文件夹:

    `mkdir lamp-stack && cd lamp-stack`

*   创建另一个子目录`/php`，其中包含以下`index.php`文件:

```
<!-- ./php/index.php -->

<html>
    <head>
        Hello World
    </head>

    <body>
        <?php
            echo "Hello, World!";
        ?>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*   用以下配置填充`docker-compose.yml`:

```
# ./docker-compose.yml

version: '3'

services:
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: my_secret_pw_shh
      MYSQL_DATABASE: test_db
      MYSQL_USER: devuser
      MYSQL_PASSWORD: devpass
    ports:
      - "9906:3306"
  web:
    image: php:7.2.2-apache
    container_name: php_web
    depends_on:
      - db
    volumes:
      - ./php/:/var/www/html/
    ports:
      - "8100:80"
    stdin_open: true
    tty: true 
```

Enter fullscreen mode Exit fullscreen mode

*   我们的目录结构应该如下所示:

```
$  tree
. ├── docker-compose.yml
└── php
    └── index.php 
```

Enter fullscreen mode Exit fullscreen mode

*   在终端中执行`docker-compose up -d`，在浏览器中加载 [http://localhost:8100/](http://localhost:8100/) 。

该学点儿 [P](https://www.w3schools.com/php/default.asp) [H](https://www.tutorialspoint.com/php/index.htm) [P](http://php.net/manual/en/tutorial.php) 了！

##### 备注

*   我们使用[端口转发](https://docs.docker.com/config/containers/container-networking/)从本地机器连接到容器内部。
    *   网络服务器:`http://localhost:8100`
    *   db: `mysql://devuser:devpasslocalhost:9906/test_db`
*   我们的本地目录`./php`，作为`/var/www/html/`被[挂载到](https://docs.docker.com/storage/volumes/)web server 容器中
    *   当我们访问容器内部的网站时，我们本地文件夹中的文件将被提供