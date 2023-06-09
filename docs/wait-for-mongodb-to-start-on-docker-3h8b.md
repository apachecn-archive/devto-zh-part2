# 等待 MongoDB、Postgres 或 MySql 在 Docker 上启动

> 原文：<https://dev.to/hugodias/wait-for-mongodb-to-start-on-docker-3h8b>

[最初发布于我的博客](https://blog.hdias.dev/wait-mongodb-mysql-on-docker)

*快速提示此处*。

我一直在努力让这个东西工作，所以我需要与你们分享。

假设你有一个依赖于 **MongoDB** 容器的应用:

```
version: "3.3"
services:
  web:
    build: .
    volumes:
      - "./app:/src/app"
    ports:
      - "3030:3000"
    links:
      - mongo
    depends_on:
      - mongo
  mongo:
    container_name: mongo
    image: mongo
    volumes:
      - ./data:/data/db
    ports:
      - "27017:27017"
volumes:
  mongo_data: 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，它是一个 **NodeJS** 应用程序。

MongoDB 启动起来非常慢，所以当应用程序启动时，链接的容器已经准备好了，但数据库本身还没有准备好。

要解决这个问题，您可以使用一个脚本来显式地等待 MongoDB 服务启动。

将此添加到您的`Dockerfile`

```
FROM node:latest

RUN mkdir /src

WORKDIR /src
ADD app/package.json /src/package.json
RUN npm install EXPOSE 3000

## THE LIFE SAVER
ADD https://github.com/ufoscout/docker-compose-wait/releases/download/2.2.1/wait /wait
RUN chmod +x /wait

## Launch the wait tool and then your application
CMD /wait && npm start 
```

Enter fullscreen mode Exit fullscreen mode

现在改变你的`docker-compose.yml`文件并增加一个`WAIT_HOSTS`部分

```
version: "3.3"
services:
  web:
    build: .
    volumes:
      - "./app:/src/app"
    ports:
      - "3030:3000"
    links:
      - mongo
    depends_on:
      - mongo
    environment:
      WAIT_HOSTS: mongo:27017
  mongo:
    container_name: mongo
    image: mongo
    volumes:
      - ./data:/data/db
    ports:
      - "27017:27017"
volumes:
  mongo_data: 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

您可以将其用于其他任何服务，如`Postgres`、`MySQL`等...

为此你只需要使用逗号:

```
version: "3"

services:

  mongo:
    image: mongo:3.4
    hostname: mongo
    ports:
      - "27017:27017"

  postgres:
    image: "postgres:9.4"
    hostname: postgres
    ports:
      - "5432:5432"

  mysql:
    image: "mysql:5.7"
    hostname: mysql
    ports:
      - "3306:3306"

  mySuperApp:
    image: "mySuperApp:latest"
    hostname: mySuperApp
    environment:
      WAIT_HOSTS: postgres:5432, mysql:3306, mongo:27017 
```

Enter fullscreen mode Exit fullscreen mode

希望有帮助