# 如何为 Python Web 应用程序编写 docker 文件

> 原文：<https://dev.to/hasurahq/how-to-write-dockerfiles-for-python-web-apps-5bmn>

[![](../Images/6733bdfacc7611309af0f932b868b932.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oCBV0z3G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/how-to-write-dockerfiles-for-python-web-apps-6d173842ae1d/1-8rsXezmgl9VTA4zqCcUsfw.jpeg)

## TL；速度三角形定位法(dead reckoning)

这篇文章充满了从简单的 docker 文件到 Python 应用程序的多阶段生产构建的例子。以下是本指南内容的快速总结:

*   使用适当的基本映像(debian 用于开发，alpine 用于生产)。
*   在开发期间使用`gunicorn`进行热重装。
*   优化 Docker 缓存层——按照正确的顺序放置命令，以便仅在必要时执行`pip install`。
*   使用`flask`静态和模板文件夹提供静态文件(通过 React/Vue/Angular 生成的包)。
*   使用多阶段`alpine`构建来缩小生产的最终图像尺寸。
*   # Bonus——使用 gunicorn 的`--reload`和`--reload_extra_files`在开发过程中监视文件(包括 html、css 和 js)的变化。

如果你想直接跳到代码，请查看 [GitHub repo](https://github.com/praveenweb/python-docker) 。

## 目录

1.  简单的 Dockerfile 文件和。dockerignore
2.  gunicorn 热重装
3.  运行单个 python 脚本
4.  提供静态文件
5.  单阶段生产制造
6.  多阶段生产制造

让我们假设一个简单的目录结构。该应用程序名为 python-app。顶层目录有一个`Dockerfile`和一个`src`文件夹。

您的 python 应用程序的源代码将位于`src`文件夹中。它还包含`requirements.txt`文件中的应用程序依赖关系。为了简洁起见，我们假设 server.py 定义了一个运行在端口 8080 上的 flask 服务器。

```
python-app
├── Dockerfile
└── src
    └── server.py
    └── requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 1。简单的 Dockerfile 文件示例