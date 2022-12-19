# 如何使用 Docker 进行简单快速的 WordPress 开发

> 原文：<https://dev.to/napolux/how-to-use-docker-for-easy-and-fast-wordpress-development-h62>

不久前，我曾经用这个[“基于流浪者的”环境](http://vccw.cc/)开发我的 WordPress。它写得很好，是开源的，附带了你为 WordPress 创建主题和插件所需的任何东西。

但是我想要更轻的东西。

我需要一个超轻的 WordPress 安装，没有麻烦，部署非常快。Docker Compose 是正确的选择。

*   容器比虚拟机更轻
*   容器是隔离的，但是共享相同的操作系统和库(如果需要的话)
*   如果需要，同一个 docker-compose.yml 文件可以用于开发和生产，使环境在更高层次上兼容。

所以，这是我的`docker-compose.yml`文件，供你使用。

```
version: '3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: password
       MYSQL_DATABASE: wordpress
       MYSQL_USER: user
       MYSQL_PASSWORD: password
   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     volumes:
       - ./wp-content/:/var/www/html/wp-content 
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: user
       WORDPRESS_DB_PASSWORD: password
volumes:
    db_data: 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，你也可以从 GitHub 中克隆它。

让我们深入研究一下这个问题...

**第一行**:我们告诉 docker 我们将在文件的其余部分使用哪个语法版本。你可以在文档中了解更多关于不同版本[的信息。](https://docs.docker.com/compose/compose-file/compose-versioning/)

然后在`services`部分，我们告诉系统我们需要这些图像:

*   MySQL 版本 5.7，有一些“强”凭证:P
*   最新版本

对于 WordPress，我们告诉 WordPress 映像将端口 80 映射到我们的 8000，并将本地`wp-content`文件夹映射到 WordPress 安装中的同一个文件夹。

我们将把我们所有的代码放在文件夹中，以便在 WordPress 管理面板中可用(想想主题和插件)。

因此，要开始运行，您需要做的就是:

*   从[https://www.docker.com/](https://www.docker.com/)安装对接器
*   克隆回购
*   运行`docker-compose up`

打开你的浏览器。转到`http://localhost:8000`，你就可以开始了！

很容易，不是吗？

最初发表@[https://coding.napolux.com](https://coding.napolux.com)