# 编写 Docker 相关文件的最佳实践

> 原文：<https://dev.to/nickjj/best-practices-when-it-comes-to-writing-docker-related-files-ek3>

**本文最初发布于 2018 年 6 月 19 日:[https://nickjanetakis . com/blog/best-practices-when-to-writing-docker-related-files](https://nickjanetakis.com/blog/best-practices-when-it-comes-to-writing-docker-related-files)**

* * *

下面列出的一切都是基于个人经验和观点。这些对我和我多年从事自由职业时共事过的客户来说都很有效。

同样值得一提的是，这个列表总是根据新的体验而变化，当新的模式/风格出现时，我会更新这个帖子。

## Dockerfile

*   使用阿尔卑斯山作为基础图像，除非由于技术原因而无法使用
*   将版本至少固定到次要版本，例如:`2.5-alpine`而不是`2-alpine`
*   添加一个维护者`LABEL`来跟踪最初制作图像的人
*   如果你真的需要的话，只包括`ARG`和`ENV`指令
*   使用`/app`来存储你的应用程序代码，并将其设置为`WORKDIR`(如果有意义的话)
*   安装软件包时，利用 Docker 的层缓存技术
*   如果你的应用是一项网络服务，除非你有很强的理由不这么做
*   包括一个`wget`驱动的`HEALTHCHECK`(如果有意义的话)
*   在提供您的`CMD`指令时，坚持使用`[]`语法

**这在下面的`docker-compose.yml`一节中有更详细的解释。*

## 码头工-化合物. yml

*   按照您希望服务启动的顺序列出它们
*   按字母顺序排列每个服务的属性
*   用双引号将所有字符串括起来，并使用`{}`作为空散列/字典
*   将版本至少固定到次要版本，例如:`10.4-alpine`而不是`10 alpine`
*   当你需要当前目录的路径时，使用`.`而不是`$PWD`
*   除非需要使用`args`或其他子属性，否则最好使用`build: "."`
*   如果你的服务是一个 web 服务，发布端口`8000`除非它对

##### 按字母顺序排列服务的属性，真的吗？

没错。有些服务需要有一堆属性，而不给它们一个特定的顺序，这意味着您可能会在不同的项目甚至服务之间以不同的方式排序。

这只是多了一件需要考虑的事情，但是通过将它们按字母顺序排列，你不再需要浪费大脑周期来手动分组，你可以快速浏览属性列表。

##### 在 8000 端口上曝光发布？

最后一个需要解释。我经常使用 Flask、Rails、Phoenix 和 Node 应用程序。默认情况下，它们的应用服务器使用不同的端口。

Rails 用 3000，Flask 用 5000，Phoenix 用 4000，大部分 Express apps 用 3000。

当在浏览器中访问这些服务时，你会感到困惑，因为你总是想，等等，是`localhost:3000`还是`localhost:4000`？

通过将 web 服务默认为`8000`,你就不会再怀疑自己了。如果你有一个多服务应用程序，那么端口增加 1，如`8001`、`8002`等..

## 。码头工人

*   不要忘记创建这个文件:D
*   不要忘记添加`.git`文件夹
*   不要忘记添加任何敏感文件，如`.env.production`

## 流行 Web 框架的应用示例

我收集了一些遵循这些最佳实践的示例应用程序。你可以在 https://github.com/nickjj/docker-web-framework-examples 的网站上找到它们。

##### 充分发挥 Docker 撰写基础的例子，可以参考:

*   [烧瓶](https://github.com/nickjj/docker-web-framework-examples/tree/master/flask)
*   [节点/快递](https://github.com/nickjj/docker-web-framework-examples/tree/master/node)
*   [凤凰](https://github.com/nickjj/docker-web-framework-examples/tree/master/phoenix)
*   [轨道](https://github.com/nickjj/docker-web-framework-examples/tree/master/rails)
*   [网络包](https://github.com/nickjj/docker-web-framework-examples/tree/master/webpack)

如果上面没有列出你最喜欢的 web 框架，[打开一个 PR](https://github.com/nickjj/docker-web-framework-examples/blob/master/CONTRIBUTING.md) ！这个 repo 是一个社区的努力，我们可以一起工作来制作高质量的示例应用程序，展示 Dockerizing 流行的 web 框架和库。

你有哪些最佳实践？下面让我知道！