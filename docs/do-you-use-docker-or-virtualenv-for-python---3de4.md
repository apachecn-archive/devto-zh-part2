# Python 用 Docker 还是 Virtualenv？

> 原文：<https://dev.to/rosejcday/do-you-use-docker-or-virtualenv-for-python---3de4>

最近我写了一篇关于将 Docker 用于 Jupyter 和 Python 用于数据科学的英特尔 Python2 发行版的文章。有了这个，我得到了一些用 virtualenv 代替 Docker 的反馈。

我开始使用 Docker 是为了学习如何创建可以轻松地从一台机器转移到另一台机器的便携环境。这样，Docker 就可以通过使用多个容器将其他服务添加到应用程序中。我目前正在学习使用`docker-compose`来创建一个具有多个容器的应用程序。这允许添加诸如 PostgreSQL、MongoDB、Jupyter 笔记本或 Apache Hadoop 之类的服务。

你通常使用什么，为什么？

### 参考文献

封面图片来源于 [Docker 壁纸](http://crosbymichael.com/docker-wallpapers.html)