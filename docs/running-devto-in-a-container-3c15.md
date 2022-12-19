# 在容器中运行开发工具

> 原文：<https://dev.to/chuck_ha/running-devto-in-a-container-3c15>

# 新岗位，更好的 UX！

[![chuck_ha image](img/8057d9874d283b8f118240a5f78d6bb2.png)](/chuck_ha) [## 在容器中运行 dev.to 第 2 部分

### 查克·哈

#container#docker#devto#webdev](/chuck_ha/running-devto-in-a-container-part-2-l77)

# 原始帖子在这里

[Dev.to 开源](https://github.com/thepracticaldev/dev.to)！厉害！让应用程序运行起来相对容易，所以要依靠 dev.to 团队来获得好的文档和干净的 rails 项目。

本文将带您了解如何在容器中运行 dev.to 的开发版本。

## 建筑

dev.to 需要一个 web 服务器和一个数据库才能在开发模式下运行。它可能需要更多的生产服务，但这已经超出了目前的范围。

### 数据库

该数据库需要首先存在，因为它是应用程序的依赖关系。数据库容器和应用程序容器将位于同一个网络中，因此它们可以通过 tcp 进行通信。

数据库状态将保存在我们的主机(本例中是我的笔记本电脑)的磁盘上。我喜欢将一个数据目录挂载到 postgres 容器中，这样当我的容器重启时，我就有了一些持久性。这里有一些可选的阅读材料，可能会让下一部分更加清晰。

```
# create the data directory
mkdir data

# create the docker network that will be shared by db and app containers
docker network create devto

# launch postgres
#   `--rm` removes the container when it stops running. This just helps us clean up
#   `--network devto` connects this container to our created network
#   `--name db` names the container and is shown in the output of `docker ps`
#   `-e POSTGRES_PASSWORD=devto` sets an environment variable which will set the password we will use to connect to postgres
#   `-e POSTGRES_USER=devto` sets the env var which will define the user that will connect to postgres
#   `-e POSTGRES_DB=PracticalDeveloper_development` sets the env var that defines the default database to create
#   `-v $(PWD)/data:/var/lib/postgresql/data` mounts the data directory we created above into the postgres container where all the data will live
#   `postgres:10` runs postgres using the latest stable 10 release (example: v10.5)
docker run --rm --network devto --name db -e POSTGRES_PASSWORD=devto -e POSTGRES_USER=devto -e POSTGRES_DB=PracticalDeveloper_development -v $(PWD)/data:/var/lib/postgresql/data postgres:10 
```

### web 应用程序

我们必须稍微修改一下代码，以使其与下面的 docker 文件一起工作。以下是我所做的更改:

1.  将`gem "tzinfo-data"`添加到`Gemfile`(我认为这是 ubuntu 相关的，还不是 100%确定)
2.  在`config/database.yaml`的默认数据库配置中设置`url: <%= ENV['DATABASE_URL'] %>`
3.  在测试配置下，注释掉同一文件中的`host: localhost`

在我完成这项工作后，我发现已经存在一个[问题](https://github.com/thepracticaldev/dev.to/issues/299)和一个 [WIP 拉动请求](https://github.com/thepracticaldev/dev.to/pull/296)。本文中介绍的方法是第一遍，需要清理，但我试图弄清楚到底发生了什么。

```
FROM ubuntu:18.04

ADD . /root/dev.to
WORKDIR /root/dev.to/

# Set up to install ruby
RUN apt update && apt install -y autoconf bison build-essential libssl-dev libyaml-dev libreadline-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm5 libgdbm-dev

# This uhh helps when you run the container in interactive mode
RUN echo 'export PATH=/root/.rbenv/bin:/root/.rbenv/shims:$PATH' >> ~/.bashrc

# install rbenv-installer
RUN apt install -y curl git && \
    export PATH=/root/.rbenv/bin:/root/.rbenv/shims:$PATH && \
    curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash

# install rbenv
RUN export PATH=/root/.rbenv/bin:/root/.rbenv/shims:$PATH && \
    rbenv install && \
    echo 'eval "$(rbenv init -)"' >> ~/.bashrc

# Install gems and yarn
RUN export PATH=/root/.rbenv/bin:/root/.rbenv/shims:$PATH && \
    gem install bundler && \
    gem install foreman && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update &&\
    apt install -y yarn libpq-dev && \
    bundle install && \
    bin/yarn 
```

使用正确的 Algolia 键/应用程序 id 修改下面的命令，然后构建并运行 docker 映像。

```
docker build . -t dev.to:latest

# setting the various timeouts to large numbers 10000 since the docker version of this app and database tend to be *extremely* slow.
# -p 3000:3000 exposes the port 3000 on the container to the host's port 3000\. This lets us access our dev environment on our laptop at http://localhost:3000.
docker run -it --rm --network devto -p 3000:3000 -e RACK_TIMEOUT_WAIT_TIMEOUT=10000 -e RACK_TIMEOUT_SERVICE_TIMEOUT=10000 -e STATEMENT_TIMEOUT=10000 -e ALGOLIASEARCH_API_KEY=yourkey -e ALGOLIASEARCH_APPLICATION_ID=yourid -e ALGOLIASEARCH_SEARCH_ONLY_KEY=yourotherkey -e DATABASE_URL=postgresql://devto:devto@db:5432/PracticalDeveloper_development dev.to:latest /bin/bash

> bin/setup
> bin/rails server
... 
```

然后打开你的笔记本电脑。

如果你有困难，请留下评论，我会更新这个帖子！

这是让 dev .在 Kubernetes 上运行的系列文章的第 1 部分。敬请期待下一篇文章！