# 如何使用 Docker 运行 Laravel

> 原文：<https://dev.to/kyleparisi/how-to-run-laravel-using-docker-iko>

Laravel 有在本地运行 laravel 的官方文档[这里](https://laravel.com/docs/5.6/installation)。文档没有涉及如何使用 docker。 [Laradock](https://github.com/laradock/laradock) 是一个很好的解决方案，然而它试图提供天底下的一切。我不需要那个。我需要简单，这样我才能编码。

在我以前的雇主那里，我想出了一个非常巧妙的测试平台，这样我就可以用 typescript 进行端到端的测试。这篇文章将描述我的方法。

因此，我需要提供的第一件事是一个 docker 图像。在这里可以找到。从那里可以用下面的配置将一个`docker-compose.yml`文件添加到您的 laravel 项目中。

```
version: '2'
services:
  app:
    image: kyleparisi/larasible
    ports:
      - 80:80
    environment:
      - APP_ENV=testing
    volumes:
      - ./:/var/www/default

  mysql:
    image: mysql:5.7
    ports:
      - 3306:3306
    environment:
      - MYSQL_DATABASE=homestead
      - MYSQL_USER=homestead
      - MYSQL_PASSWORD=secret
      - MYSQL_ROOT_PASSWORD=secret 
```

Enter fullscreen mode Exit fullscreen mode

有一件事我很少看到有人回答，那就是如何做一个`composer install`而不遇到主机和容器之间的权限问题。我后来发现，您可以附加主机用户 id 作为所有者，这将允许主机和容器都使用`vendor`文件。

```
#!/usr/bin/env bash

docker run --rm \
    --user $(id -u):$(id -g) \
    --volume $PWD:/var/www/default \
    --volume $HOME/.composer:/.composer \
    --workdir /var/www/default \
    --entrypoint "composer" \
    kyleparisi/larasible "$@" 
```

Enter fullscreen mode Exit fullscreen mode

我通常将这个 bash 脚本添加到`./scripts/composer.sh`中，这样你就可以运行所有你想要的典型命令。`./scripts/composer.sh install`比如。这很好，因为除了 docker 之外，您不再需要对主机的任何依赖。对于前端依赖项，也可以做同样的事情。

```
#!/usr/bin/env bash

docker run --rm \
    --volume $PWD:/usr/src/app \
    --workdir /usr/src/app \
    node:8 npm "$@" 
```

Enter fullscreen mode Exit fullscreen mode

因此，将这两个脚本添加到您的 laravel 项目中的一个`./scripts/`目录中，并使它们可执行。

```
chmod +x ./scripts/composer.sh
chmod +x ./scripts/npm.sh 
```

Enter fullscreen mode Exit fullscreen mode

向您的 laravel 项目添加一个`.env.testing`文件，它将在我们的测试平台中使用。

```
APP_ENV=testing
APP_KEY=base64:blahblah
APP_DEBUG=true APP_LOG_LEVEL=debug
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret

BROADCAST_DRIVER=log
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_DRIVER=smtp
MAIL_HOST=mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET= 
```

Enter fullscreen mode Exit fullscreen mode

现在吃肉和土豆。

```
#!/usr/bin/env bash

set -e

 [ -f ".env.testing" ] || $(echo Please make an .env.testing file and run: php artisan key:generate --env=testing; exit 1)
export $(cat .env.testing | grep -v ^# | xargs);
echo Starting services
docker-compose up -d
echo Host: 127.0.0.1
until docker-compose exec mysql mysql -h 127.0.0.1 -u $DB_USERNAME -p$DB_PASSWORD -D $DB_DATABASE --silent -e "show databases;"
do echo "Waiting for database connection..."
  sleep 5
done echo Installing dependencies
./scripts/npm.sh install
./scripts/composer.sh install
echo Seeding database
rm -f bootstrap/cache/*.php
docker-compose exec app php artisan migrate --env=testing && echo Database migrated
docker-compose exec app php artisan db:seed --env=testing && echo Database seeded 
```

Enter fullscreen mode Exit fullscreen mode

将上面的脚本作为`testrig.sh`添加到您的 laravel 项目的根目录，并使其可执行。这个脚本完成了运行 laravel 项目所需的一切。它启动服务器，确保数据库被引导，安装依赖项，运行数据库迁移，以及运行数据库种子。这些服务在标准端口上向您的计算机公开。

```
./testrig.sh 
```

Enter fullscreen mode Exit fullscreen mode

脚本完成后，请访问`http://localhost`查看 laravel 欢迎屏幕。Mysql 可以在端口`3306`上找到。如果您有时髦的端到端测试，您可以将您的测试命令添加到`testrig.sh`文件的末尾。比如`npm test`。

从这里，您可以使用这个测试平台进行您的“测试驱动开发”或常规开发。当你完成一天的工作后，清理是非常简单的。

```
docker-compose stop && docker-compose rm 
```

Enter fullscreen mode Exit fullscreen mode

希望你会发现这个方法比官方选项更快，也比`laradock`更简单。

[文件的要旨。](https://gist.github.com/kyleparisi/b1fa7e0da8a5ac64c6c340c72f67ba49)