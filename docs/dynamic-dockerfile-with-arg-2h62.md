# 带参数的动态 Dockerfile

> 原文：<https://dev.to/stevoperisic/dynamic-dockerfile-with-arg-2h62>

有时你需要一个动态的 docker 文件。例如，您正在运行一个微服务，它要求每个环境都有不同的 API 调用:开发、试运行和生产。如果您的服务依赖于环境变量来区分调用哪个 API，那么您通常在 docker 文件中使用:

```
ENV environment=production 
```

这很好，但是，如果`environment`需要根据构建目标进行更改，我们该怎么办呢？您必须在每次构建之前手动更新 Dockerfile 文件。

我们可以使用 ARG 变量来代替，并更新`environment` ad 构建时间的值。请考虑以下情况:

```
ARG environment
ENV environment=${environment:-production} // set a default in case the ARG isn't passed 
```

我们上面所做的是声明一个构建参数`environment`，然后使用该值在 Docker 构建映像中填充 ENV 变量环境。考虑以下构建命令:

```
docker build -t myTag --build-arg environment=staging 
```

在这种情况下，我们首先获取 build 参数的值，然后将其值传递给 ENV 变量，这样它将计算为“staging”。但是，如果我们不指定构建参数，而只是运行:

```
docker build -t myTag 
```

然后,`ENV environment`的值将是“生产”,正如我们为默认值所设置的那样。

当您有两个以上的环境要构建，并且必须支持与这些环境紧密耦合的不同服务器端呈现或 API 调用时，这将非常有帮助。

更多详情请查看 [Docker docs](https://docs.docker.com/engine/reference/builder/#using-arg-variables)

谢谢！