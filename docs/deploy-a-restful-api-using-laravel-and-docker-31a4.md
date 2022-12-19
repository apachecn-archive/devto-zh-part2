# 使用 Laravel 和 Docker 部署 RESTful API

> 原文：<https://dev.to/jfernancordova/deploy-a-restful-api-using-laravel-and-docker-31a4>

[![docker-laravel-api-dev](img/6418bbf4cac88bfbef4f79e876c38bb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SyMT5DFd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aajawuadam0fwye5yqyh.png)

## 入门

*   API Laravel 样板文件 5.5
*   阿帕奇 2
*   关系型数据库
*   Xdebug
*   码头工人

你可以用最快、最简单、最有趣的方式管理 RESTful API！并且具有开源代码依赖性。

然而，您可以为更好的性能做出贡献，目标是将重点放在避免配置和安装 docker 的代码上。

### 先决条件

*   [码头工人](https://docs.docker.com/install/)
*   [坞站组成](https://docs.docker.com/compose/install/)

## 环境

### 开发或本地模式

*   yml:自动生成文件夹，并需要本地工作空间上的-dev 依赖项，包括 Xdebug。注意:yaml 文件有一个名为“XDEBUG_MODE”的键，默认情况下，这个 yaml 的值为 true (1)以安装它。

您可以欣赏工作区上自动生成的依赖项！

### 用 Docker 模式玩

[![Play With Docker](img/8a370d05ade5965e5f73cd692a650c50.png)T2】](http://play-with-docker.com?stack=https://raw.githubusercontent.com/jfernancordova/docker-laravel-api-dev/master/docker-compose-pwd.yml)

*   yml:您可以使用它来可视化这个环境并测试它，但是，您应该查看容器列表来验证健康检查。

```
# container lists
docker ps
# make sure that the docker dockerlaravelapidev_php_1 or php container is (healthy),
normally the process begins in starting mode (health: starting) 
```

Enter fullscreen mode Exit fullscreen mode

### 生产模式

*   如果您要使用这个 yaml，请确保在之前生成迁移！，您可以修改入口点来生成它们。

## 部署

### Docker 环境

克隆此存储库，并基于您的 orchestrator 运行以下命令。

### 蜂群模式

克隆该存储库并运行以下命令:

```
cd docker-laravel-api-dev/
docker stack deploy -c docker-compose-dev.yml docker-laravel-api-dev
# wait for the HEALTHCHECK in healthy mode 
```

Enter fullscreen mode Exit fullscreen mode

### 复合坞站

克隆该存储库并运行以下命令:

```
cd docker-laravel-api-dev/
docker-compose -f docker-compose-dev.yml up --build -d 
or to see the logs 
docker-compose -f docker-compose-dev.yml up 
# wait for the HEALTHCHECK in healthy mode 
```

Enter fullscreen mode Exit fullscreen mode

## 运行测试

您有一个 [Travis](https://travis-ci.org/) 管道来应用持续集成和其他技术来测试这个环境。

您可以从[脚本文件夹](https://raw.githubusercontent.com/jfernancordova/docker-laravel-api-dev/master/scripts/runtests.sh)中修改 runtests.sh

在[管道](https://raw.githubusercontent.com/jfernancordova/docker-laravel-api-dev/master/.travis.yml)中插入作业、指令

### Github 资源库

[码头-拉威尔-api-dev](https://github.com/jfernancordova/docker-laravel-api-dev)

#### API 样板参考

*   laravel-api-boilerplate-jwt

## 作者

*   何塞·费尔南多·科尔多瓦

## 执照

这个项目是在 MIT 许可下授权的——更多细节见 [LICENSE.md](https://github.com/jfernancordova/docker-laravel-api-dev/blob/master/LICENSE.md) 文件。