# Docker 中的安全用户

> 原文：<https://dev.to/nownabe/secure-user-in-docker-1b5m>

# TL；速度三角形定位法(dead reckoning)

在 Dockerfile 中创建用户如下:

```
RUN groupadd -g 61000 docker
RUN useradd -g 61000 -l -M -s /bin/false -u 61000 docker 
```

Enter fullscreen mode Exit fullscreen mode

# Docker 中的安全用户

以根用户身份运行 docker 容器是有风险的，因为容器中的根用户与主机根用户具有相同的 uid 0。因此，您必须尽可能将容器中的用户更改为非 root 用户。

最简单有效的方法是在`Dockerfile`中创建一个用户。比如:

```
FROM debian

RUN useradd docker
USER docker

CMD ["bash"] 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用`docker run`的`-u`选项更改用户。使用 Kubernetes，您可以使用 SecurityContext 来修改用户。

这个`Dockerfile`没问题，但是`useradd`有很多选项。让我描述一下 Dockerfile 中应该使用哪些选项。

Dockerfile 经常使用的选项有:

```
$ useradd --help
Usage: useradd [options] LOGIN
       useradd -D
       useradd -D [options]

Options:
  -g, --gid GROUP               name or ID of the primary group of the new
                                account
  -l, --no-log-init             do not add the user to the lastlog and
                                faillog databases
  -m, --create-home             create the user's home directory
  -M, --no-create-home          do not create the user's home directory
  -r, --system                  create a system account
  -s, --shell SHELL             login shell of the new account
  -u, --uid UID                 user ID of the new account
  -U, --user-group              create a group with the same name as the user 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下每个选项。

*   `-g`指定 uid。因为和 uid 一样的 gid 比较好理解，所以应该用`-g`。
*   `-l`看起来不错，因为 lastlog 和 faillog 意义不大。
*   如果你必须在容器 <sup id="fnref1">[1](#fn1)</sup> 中操作 shell，使用`-m`创建主目录。如果没有，使用`-M`。
*   `-r`选项使用户成为系统帐户。uid 是从`/etc/login.defs`配置的，并且没有创建主目录。您不应该使用这个选项，因为您将使用`-u`来指定 uid，并使用`-m/-M`来配置主目录。
*   `-s /bin/false`可以禁止远程登录。即使设置了`/bin/false`或`/bin/nologin`，也可以用`docker exec -u $uid sh`执行 shell。这个选项可以防止直接远程登录，所以你应该使用`-s /bin/false`。
*   `-u`指定 uid。不使用`-u`时，uid 自动分配。为了简单管理，您应该使用此选项。
*   `-U`创建与用户同名的组，但 gid 可以不同于 uid。`-g`优先于`-U`。

总之，您应该使用以下说明来创建用户:

```
RUN groupadd -g 61000 docker
RUN useradd -g 61000 -l -M -s /bin/false -u 61000 docker 
```

Enter fullscreen mode Exit fullscreen mode

如果需要主目录:

```
RUN groupadd -g 61000 docker
RUN useradd -g 61000 -l -m -s /bin/false -u 61000 docker 
```

Enter fullscreen mode Exit fullscreen mode

因为主要发行版保留了从 1000 到 60000 的 uid，所以我建议将 61000 作为 uid。顺便说一句，GKE 的工人节点从 5000 到 60000 预留 uid。如果您使用 5000 作为 uid，它们会发生冲突。

* * *

1.  比如你经常用`bundle exec rails console`。 [↩](#fnref1)