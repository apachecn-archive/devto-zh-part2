# Gitlab 存储库的免费 CI(使用 Docker-Compose )?

> 原文：<https://dev.to/einenlum/a-free-ci-using-docker-compose-for-your-gitlab-repositories-6po>

*这篇文章最初发表在我的[博客](https://www.einenlum.com/articles/free-ci-gitlab)上。*

如果你像我一样，你使用 Github 作为公共存储库和组织拥有的私有存储库，但是对于你自己的私有存储库，你使用 Gitlab。事实上，Gitlab 提供了免费的无限私有库并完成了这项工作。

在进行了大量的提交之后，您决定在每次合并请求(是的，合并请求)时自动化您的测试是非常好的。我们这里说的是 Gitlab 语言)是被创造出来的。

然后你认为你可以像使用 Github 库一样使用 CircleCI，[特别是因为你知道如何在它上面使用 docker-compose](https://dev.to/einenlum/testing-your-app-with-docker-compose-on-circleci-1ll)。你登录你的 CircleCI 账号，进入集成页面，意识到 [Gitlab 集成少了](https://circleci.com/integrations/)。Github 在那里，Bitbucket 也在，但是这个地区没有 Gitlab。妈的。[你仍然可以在 CircleCI 的网站](https://circleci.com/ideas/?idea=CCI-I-248)上投票支持 Gitlab 整合的想法，但是在他们决定改变之前，你会被卡住。

您决定列出所有可以与 Gitlab 一起工作并符合您期望的免费 CI 服务。然后你意识到 Gitlab 提供 CI 服务。[在阅读了网站上所有不清楚的广告功能后](https://about.gitlab.com/features/gitlab-ci-cd/) ( **对 Gitlab 的广告:你有一个很好的产品，我们只是不知道你的报价有多酷，因为你网站上的一切都很模糊。真遗憾**你开始想，也许他们甚至为使用社区版免费私有库的人提供了一个免费的 CI。它似乎被限制在每月 2000 分钟，坦白地说，这是一个非常慷慨的交易。我们今天的问题更加精确:有没有可能使用 docker-compose 来构建整个堆栈并在 CI 中运行您的测试？没错。

首先，(非常糟糕……)文档说您需要启用一些共享的运行器，但是在我的例子中，默认情况下已经这样做了，所以我想您也不需要这样做。下一个。

现在我们只需要在项目的根目录下创建一个`.gitlab-ci.yml`文件。[这对于一般用途](https://docs.gitlab.com/ce/ci/yaml/)来说是很有文档记录的，但是在文档中没有 docker-compose 的踪迹。看来我们只需要把它安装成 python 包([谢谢堆栈溢出！](https://stackoverflow.com/questions/39868369/run-docker-compose-build-in-gitlab-ci-yml/42697808#42697808))然后我们就可以使用它了。我们还需要使用一些模糊的图像:docker 和 docker:dind 服务。好吧，如果有用，为什么不呢。

```
image: docker

services:
  - docker:dind

before_script:
  - apk add --no-cache py-pip
  - pip install docker-compose
  - docker-compose build
  - docker-compose up -d
  # here we can install our dependencies (composer, yarn…)

tests:
  script:
    # launch our tests
    - docker-compose exec php phpspec run --format pretty
    - docker-compose exec php phpunit
    - docker-compose exec php behat 
```

Enter fullscreen mode Exit fullscreen mode

实际上很简单。我只是在执行`docker-compose exec`时遇到了一些交互 TTY 的问题(我在本地和 CircleCI 上都没有)。可能因为 pip 用的版本是[加回归的那个](https://github.com/docker/compose/issues/5696#issue-299103105)？我不知道。无论如何，您可以通过在命令中添加一个`-T`标签来解决这个问题。

```
image: docker

services:
  - docker:dind

before_script:
  - apk add --no-cache py-pip
  - pip install docker-compose
  - docker-compose build
  - docker-compose up -d
  - docker-compose exec -T php composer install

tests:
  script:
    - docker-compose exec -T php phpspec run --format pretty
    - docker-compose exec -T php phpunit
    - docker-compose exec -T php behat 
```

Enter fullscreen mode Exit fullscreen mode

这应该能行。希望有帮助。测试愉快。

***更新*** :其实可能我说的太快了…
**我对网络有意见。容器不能互相通信…也许我可以玩网络选项(network_mode？).如果码头专家在这里，我会喜欢一些帮助:)**

*图片由[帕拉达索斯](https://www.flickr.com/photos/paradasos/7234825030/) (CC by/nc)*