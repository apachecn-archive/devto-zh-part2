# 使用 Travis CI 持续部署到 Firebase 主机

> 原文：<https://dev.to/bartw/continuous-deployment-to-firebase-hosting-using-travis-ci-3lpm>

#### 介绍

昨晚我有了另一个价值数百万美元的网络应用的想法。所以我早上做的第一件事当然是开始构思。得去挣那些钱。

我首先需要的是互联网上的某个地方，我的客户可以开始把他们的钱交给我。我想快速行动，所以每次我改变一些东西，我都想立刻看到结果。

#### 促成因素

Firebase 是谷歌的一个平台，以最少的努力免费提供托管服务。 [Travis](https://travis-ci.org/) 是一个托管的持续集成解决方案，它允许我将我的更改部署到 Firebase。 [GitHub](https://github.com/) 是我存放珍贵代码的地方。

为了构建这个东西，我在笔记本电脑上安装了 [Node.js](https://nodejs.org/en/) 和 [Docker](https://www.docker.com/) 。我真的不需要 Docker，但是我不想安装 [Ruby](https://www.ruby-lang.org/en/) 。

#### 我们去工作吧

*   转到 [Firebase](https://console.firebase.google.com) 并创建一个新项目。
*   在 [GitHub](https://github.com) 上创建一个新的资源库。
*   在本地克隆存储库。
*   创建一个最小的 index.html。

```
mkdir public
touch public/index.html 
```

*   使用 Firebase 工具将 GitHub 存储库连接到 Firebase 项目。初始化时不要覆盖 index.html。

```
npm install -g firebase-tools
firebase login
firebase init 
```

*   生成一个 Firebase CI 令牌(并放在手边)。

```
firebase login:ci 
```

*   转到 [Travis](https://travis-ci.org/) 并激活 GitHub 存储库。
*   使用 Travis CLI 加密 Firebase CI 令牌。

```
# only run the docker command if you don't have and don't want to install Ruby
docker run -it --rm ruby:latest bash
gem install travis
travis encrypt "1/firebase_CI_token" -r githubusername/reponame 
```

*   创建一个. travis.yml 文件，在每次推送 GitHub 后部署到 Firebase。

```
language: node_js
node_js:
  - "node"
script:
  -
deploy:
  provider: firebase
  token:
    secure: "encrypted_firebase_CI_token" 
```

*   提交并将更改推送到 GitHub。
*   观看 Travis 构建和部署。
*   检查 Firebase 主机以查看部署历史。
*   转到该项目的 Firebase 托管域来查看网站。

#### 结论

我刚刚花了半天时间写了一个关于使用 Travis CI 持续部署一个网站到 Firebase 的指南，我还没有开始我的数百万美元的想法。

在 [GitHub](https://github.com/bartw/travisonfire) 上找到代码。

看网站[这里](https://travisonfire-a209c.firebaseapp.com/)。