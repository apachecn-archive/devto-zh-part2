# 不要在开发中使用 docker 来运行你的应用

> 原文：<https://dev.to/maxim/don-t-use-docker-to-run-your-app-in-development-50n2>

在开发中使用 docker 可能非常方便，但是在 docker 中运行您的实际应用程序(您知道，您正在编写的应用程序)会带来各种麻烦。

1.  装载的卷速度慢且容易出错
2.  您需要技巧和快捷方式来运行容器中的任何控制台/调试命令
3.  docker 中代码变化的实时更新是不可靠的
4.  docker 的运行速度较慢
5.  docker 中的依赖关系更新较慢
6.  docker 的网络更加复杂

真正让我惊讶的是，团队经常不考虑显而易见的事情:直接在你的机器上运行你的应用程序。我发现这是开发设置的最佳点。让我们看看它会是什么样子。

## 1。对数据库和外部服务使用 docker-compose

在你的应用程序的根目录下创建一个`docker-compose.yml`文件，并且只在其中声明你的数据库。例如，该文件为您提供

*   Postgres on `localhost:5432`
*   律政巾帼第三季第 20 集
*   假 S3 上`localhost:9000`

```
version: '3'

services:
  postgres:
    image: postgres:10.3-alpine
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
  redis:
    image: redis:3.2.11-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
  minio:
    image: minio/minio
    volumes:
      - minio-data:/data
    ports:
      - "9000:9000"
    entrypoint: sh
    command: -c "mkdir -p /data/dev /data/test && /usr/bin/minio server /data"
    environment:
      MINIO_ACCESS_KEY: access_key
      MINIO_SECRET_KEY: secret_key

volumes:
  postgres-data:
  redis-data:
  minio-data: 
```

Enter fullscreen mode Exit fullscreen mode

## 2。将 [asdf](https://github.com/asdf-vm/asdf) 用于语言运行时

在 app 的根目录下创建一个`.tool-versions`文件。下面是一个关于 elixir 和节点设置的例子。

```
elixir 1.6.4-otp-20
erlang 20.2.4
nodejs 10.8.0 
```

Enter fullscreen mode Exit fullscreen mode

[Asdf](https://github.com/asdf-vm/asdf) 就像 rvm，nvm，和其他版本管理器的组合。它有[一个它可以管理的事情的广泛列表](https://github.com/asdf-vm/asdf-plugins#plugin-list)。

## 3。设置一切

现在，您可以通过运行
来引导应用程序

```
asdf install
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

在另一个终端上，你运行应用程序本身:

```
mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。现在，您可以享受快速简单的开发设置，而无需放弃直接与应用程序交互的所有便利，中间没有容器。

## 加成:如何让本地 Rails 和 dockerized Postgres 一起工作？

最酷的是你的`database.yml`可以提交给回购，它看起来总是一样的:

```
default: &default
  adapter: postgresql
  username: postgres
  host: localhost

development:
  <<: *default
  database: myapp_dev

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myapp 
```

Enter fullscreen mode Exit fullscreen mode

然而，在 Rails 中使用这种设置有一个小问题。当试图安装 pg gem 或运行`rake db:structure:dump`命令时，可能会出现错误。这两种操作都依赖于本地安装的 postgres。要解决这个问题，只需将 postgres 添加到您的`.tool-versions`中——asdf 支持它。您将不会实际运行这个 postgres，只是使用它的 cli 作为客户端，并满足 pg 的依赖关系。