# 在容器中运行 dev.to 第 2 部分

> 原文：<https://dev.to/chuck_ha/running-devto-in-a-container-part-2-l77>

在我的上一篇文章中，我提到了建立一个 docker 图像，但是它太匆忙了，我甚至不会在这里链接它。

本文旨在改善 Dockerfile 文件的混乱状况。

[提交](https://github.com/chuckha/dev.to/commit/db3dc2fefd7f3adba16ad1532c78049d09be4336)可以在我的 dev.to repo 的 fork 中找到。

为了让这个版本的 dev.to 在本地运行，请遵循以下步骤(不需要克隆):

```
# Setup a data directory for postgres
mkdir data

# Create the network that is shared between containers
docker network create devto

# Run the database
docker run --rm --network devto --name db -e POSTGRES_PASSWORD=devto -e POSTGRES_USER=devto -e POSTGRES_DB=PracticalDeveloper_development -v $(PWD)/data:/var/lib/postgresql/data postgres:10 
```

然后在另一个终端中，对应用服务器执行以下步骤:

```
# Setup the database and ensure dependencies are met
docker run --rm -it --network devto -e CONNECT_TIMEOUT=30 -e DATABASE_URL=postgresql://devto:devto@db:5432/PracticalDeveloper_development chuckdha/dev.to:latest bin/setup

# Run the webserver
docker run --rm -it --network devto -p 3000:3000 -e CONNECT_TIMEOUT=30 -e DATABASE_URL=postgresql://devto:devto@db:5432/PracticalDeveloper_development chuckdha/dev.to:latest bin/rails s 
```

然后在网页浏览器中访问 [http://localhost:3000](http://localhost:3000) 。

让我知道你的想法！