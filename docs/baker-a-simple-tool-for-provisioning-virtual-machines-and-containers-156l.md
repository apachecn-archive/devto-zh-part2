# Baker，一个配置虚拟机和容器的简单工具。

> 原文：<https://dev.to/chrisparnin/baker-a-simple-tool-for-provisioning-virtual-machines-and-containers-156l>

见见贝克！-配置虚拟机和容器的简单工具。

Baker 允许您快速创建用于开发和运行代码的开发环境。有了一个工具，你就有了流浪者、docker、ansible 和 grunt 这样的任务运行者的功能。

这个例子为使用 mkdocs 创建了一个简单的开发环境，mkdocs 是 python 中的一个静态站点生成器。

```
name: baker-docs
container: 
  ports: 8000
lang:
  - python2
commands:
  build: mkdocs build
  serve: mkdocs serve -a 0.0.0.0:8000
  gh-deploy: mkdocs gh-deploy 
```

Enter fullscreen mode Exit fullscreen mode

使用 docker 快速配置虚拟机。

```
name: docker-env
vm:
  ip: 192.168.8.8
tools:
  - docker 
```

Enter fullscreen mode Exit fullscreen mode

要使用 baker，只需在当前代码库中放置一个`baker.yml`文件，然后运行`baker bake`。您可以运行`baker ssh`来访问您的环境，或者使用`baker run`在环境中运行命令。您的代码可以在 baker 环境中通过共享文件夹访问。

要了解更多详细信息，请查看。