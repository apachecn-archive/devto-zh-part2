# 在 Docker 中指定用户和组

> 原文：<https://dev.to/acro5piano/specifying-user-and-group-in-docker-i2e>

如果我们使用 Docker 作为我们的开发环境，我们将使用`docker`命令来生成文件。

例如

```
docker run --rm ruby:2.4 bundle exec rails g ...
docker-compose run --rm web composer install 
```

Enter fullscreen mode Exit fullscreen mode

问题是这些命令生成的文件的所有者和组总是`root`。

注意:这个问题可能只发生在 Linux 的 Docker 上。麦克没有。Windows 未知。

# 解决方案 1: Dockerfile

我们可以在`Dockerfile`中设置所有者和群组。
官方文件说我们可以通过`USER postgres`来做，但是我们也可以通过`:`来定组。

```
# Dockerfile

USER 1000:1000 
```

Enter fullscreen mode Exit fullscreen mode

但是，这种方式指定了所有者和组 id。我找到了更灵活的解决方案。

# 方案二:命令自变量

`docker run -u 1000:1000 debian bash`

这样我们就可以用类似`$UID`这样的 shell 变量来设置用户和组。
或`getent group staff | cut -d: -f3`。

# 方案三:在 docker-compose.yml 中指定

我在找这个。

原理与解决方案 2 相同，因此下面的配置工作良好:

```
# docker-compose.yml

web:
  image: ruby:2.4
  user: "${UID}:${GID}" 
```

Enter fullscreen mode Exit fullscreen mode

那就跑

`UID=${UID} GID=${GID} docker-compose up`

或者，将这些变量导出为环境变量，忽略`bash: UID: readonly variable`

```
export UID=${UID}
export GID=${GID}
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

# 环境

*   Arch Linux
*   坞站 1.12.3 linux/amd64

# 偏离主题

我最喜欢的桌面 Linux 是 Arch Linux。

Arch 的软件包管理系统总是提供任何软件包的最新版本。

有了 Docker 容器，任何主机都可以。

# 参

*   [https://docs . docker . com/compose/reference/envvars/](https://docs.docker.com/compose/reference/envvars/)
*   [http://stack overflow . com/questions/35093256/how-do-I-pass-a-argument-along-with-docker-compose-up/35107585 # 35107585](http://stackoverflow.com/questions/35093256/how-do-i-pass-an-argument-along-with-docker-compose-up/35107585#35107585)
*   [https://stack overflow . com/questions/10910096/what-the-command-to-get-groupid-of-a-group-name-in-MAC-or-Linux](https://stackoverflow.com/questions/10910096/what-is-the-command-to-get-groupid-of-a-group-name-in-mac-or-linux)