# 使用 git 和 CircleCI 部署到多个环境

> 原文：<https://dev.to/hugo__df/deploy-to-multiple-environments-with-git-and-circleci-5g49>

使用 GitHub、CircleCI 和 Heroku 以简单的方式轻松部署到多个环境中。

持续集成是很棒的，但是有时候你需要在自动部署合并和产品发布之间有一个缓冲。用 CircleCI 做到这一点需要一些 git 分支争论和几行 bash 脚本。我们将想象一个场景，其中部署是微不足道的(即我们就假装在用 Heroku)。对于更复杂的构建步骤，我们仍然应该能够遵循类似的原则。这不是 CircleCI 2.0 工作流教程，它更像是 git-flow/CircleCI 的混合体，有两个(或更多)环境发布到 CircleCI 并由 CircleCI 自动部署。

我要感谢 Chris Fidao，以及这条推文:

[![🔥 Git is great, but not for what Linus probably intended. Despite its intentions, git(hub|lab|bucket) is actually used for: 1\. backup (and distribution) 2\. automation 3\. release management 4\. a hundred other things ..... xxx. version control https://t.co/GLUYkppSLs &mdash; Chris Fidao (@fideloper) https://twitter.com/fideloper/status/1020330385333530624 20 July 2018](img/4c6d2133221068e42a8e055c4f63a102.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uysmXzjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codewithhugo.com/img/2018-07-30-chris-fidao-tweet-screenshot.jpg)

我们将介绍如何使用 GitHub + CircleCI 实现部署自动化和发布管理。

*   [分支机构设置🌳](#a-branch-setup)
*   [工作流程🏞](#the-workflow)
*   [发布脚本🛫](#release-scripts)
    *   [登录 Heroku(可选)🔑](#logging-in-to-heroku-optional)
*   [12(ish)因子 app🏗](#12ish-factor-app)
    *   [注入配置和秘密💉](#injecting-config-and-secrets)
    *   [运行部署🛬](#run-that-deploy)

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 分支机构设置🌳

我们需要一个自动部署的`develop`和`master`分支。我们的默认分支应该是 develop(即。所有的拉请求都应该合并到。就像跑步一样简单:

```
$ git checkout -b develop
$ git push -u origin develop
# There's usually already a master branch otherwise:
$ git checkout -b master
$ git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

我们使用分支是因为这是 CircleCI 唯一理解的原语。在 TravisCI 或 GoCD 上，您可以为每个环境设置“管道”,但是 CircleCI 工作流不能为不同的环境手动触发，所以使用 git 分支是最简单的。

## 工作流🏞

1.  创建功能/任务分支
2.  完成任务，使代码处于待合并状态
3.  从特性/任务分支打开一个 PR 到`develop`
    1.  CircleCI 运行测试/lint 或其他任何东西(不包括在本文中)
    2.  自动检查都是绿色✅
4.  回顾
5.  PR 合并到`develop`
    1.  CircleCI 再次运行自动检查
    2.  如果所有检查都为绿色，CircleCI 将部署到开发/试运行环境
6.  要部署到生产中，发布必须是手动的
    1.  将`develop`合并为`master`
    2.  CircleCI 再次运行自动检查
    3.  如果所有检查都是绿色的，CircleCI 将部署到生产环境

为了使这个过程更容易，我们将有一些发布脚本来自动执行步骤 6(正确合并很容易出错)和一些 CircleCI 配置来执行步骤 5a-b 和 6b-c。

## 发布脚本🛫

下面是`release-production.sh`，我们可以用它来合并开发→主控的变更:

```
#!/bin/bash
set -e
set -u
RELEASE_FROM="develop"
RELEASE_TO="master"
CURRENT_BRANCH="`git branch | grep \* | cut -d ' ' -f2`"
echo "Checking out to '${RELEASE_FROM}' branch and pulling latest"
git checkout ${RELEASE_FROM} 
git pull
echo "Checking out to '${RELEASE_TO}' branch and pulling latest"
git checkout ${RELEASE_TO} 
git pull
read -p "Are you sure you want to merge '${RELEASE_FROM}' into '${RELEASE_TO}'? (y/n)" -n 1 -r
echo if [[$REPLY =~ ^[Yy]$ ]]
then git merge ${RELEASE_FROM} --ff-only
    git push
fi git checkout ${CURRENT_BRANCH} 
```

Enter fullscreen mode Exit fullscreen mode

下面是它的具体操作步骤:

*   保存当前分支名称
*   签出到我们发布的分支(`develop`)
*   拉最新的
*   签出到我们要发布到的分支(`master`)
*   拉最新的
*   合并前提示
*   合并
    *   `--ff-only`，意味着我们以“快进”方式运行所有合并，这意味着我们不会得到合并提交，这意味着不会有合并提交
*   发布前提示
*   推
*   重置到我们最初所在的分支

### 登录 Heroku(可选)🔑

为了存储秘密，我们将使用 CircleCI 环境变量设置，并通过 UI 设置`HEROKU_EMAIL`和`HEROKU_TOKEN`(设置→构建设置→环境变量)。要获得你的 Heroku 令牌运行`heroku auth:token`。要登录 Heroku，请在`login-heroku.sh`中使用以下内容:

```
cat > ~/.netrc << EOF machine api.heroku.com
        login $HEROKU_EMAIL password $HEROKU_TOKEN machine git.heroku.com
        login $HEROKU_EMAIL password $HEROKU_TOKEN  EOF # Add heroku.com to the list of known hosts
mkdir ~/.ssh
ssh-keyscan -H heroku.com >> ~/.ssh/known_hosts 
```

Enter fullscreen mode Exit fullscreen mode

## 12(ish)因子 app🏗

我们希望以某种方式管理配置，适用于[https://12factor.net/](https://12factor.net/)所描述的所有环境

### 注入配置和秘密💉

`setup-env.sh`

*   打开`CIRCLE_BRANCH`，有条件地设置一些变量(`ENVIRONMENT`、`HEROKU_APP`，不设置其他变量(`NODE_ENV`):

```
case $CIRCLE_BRANCH in
    "develop")
        export ENVIRONMENT="dev"
        export HEROKU_APP="some-app"
        ;;
    "master")
        export ENVIRONMENT="production"
        export HEROKU_APP="some-other-app"
        ;;
esac
export NODE_ENV="production" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们必须在这里设置一些秘密，我们会做如下事情:

```
case $CIRCLE_BRANCH in
    "develop")
        export MY_SECRET=${MY_SECRET_DEV}
        export HEROKU_APP="some-app"
        ;;
    "master")
        export MY_SECRET=${MY_SECRET_PRODUCTION}
        ;;
esac 
```

Enter fullscreen mode Exit fullscreen mode

其中`MY_SECRET_DEV`和`MY_SECRET_PRODUCTION`是通过 CircleCI 环境变量设置的(设置→构建设置→环境变量)。

### 运行部署🛬

`deploy-heroku.sh`:

*   从`setup-env`读取设置，添加 Heroku 遥控器并将当前分支推送到 Heroku 上的`master`

```
set -e
set -u
source ./setup-env.sh
echo "Pushing branch ${CIRCLE_BRANCH} to app ${HEROKU_APP}"
git remote add heroku https://git.heroku.com/${HEROKU_APP}.git
git push heroku ${CIRCLE_BRANCH}:master 
```

Enter fullscreen mode Exit fullscreen mode

*   为了对已部署和未部署的内容进行某种记录，我们希望在 Heroku 上设置`COMPARE_URL`和版本号(`BUILD_NUM`)，这需要 Heroku CLI:

```
if [! -L /usr/local/bin/heroku];
then wget https://cli-assets.heroku.com/branches/stable/heroku-linux-amd64.tar.gz
    sudo mkdir -p /usr/local/lib /usr/local/bin
    sudo tar -xvzf heroku-linux-amd64.tar.gz -C /usr/local/lib
    sudo ln -s /usr/local/lib/heroku/bin/heroku /usr/local/bin/heroku
fi source infra/scripts/setup-env.sh
heroku config:set BUILD_NUM=${CIRCLE_BUILD_NUM} COMPARE_URL=${CIRCLE_COMPARE_URL} -a ${HEROKU_APP} 
```

Enter fullscreen mode Exit fullscreen mode

总之，我们以下面的`.circleci/config.yml` :
结束

```
version: 2
jobs:
    deploy:
    docker:
        - image: circleci/node:10.5.0 # replace with the image you need
    steps:
        - checkout
        - run:
            name: Log in to Heroku
            command: bash ./login-heroku.sh
        - run:
            name: Install Heroku CLI
            command: |
            wget https://cli-assets.heroku.com/branches/stable/heroku-linux-amd64.tar.gz
            sudo mkdir -p /usr/local/lib /usr/local/bin
            sudo tar -xvzf heroku-linux-amd64.tar.gz -C /usr/local/lib
            sudo ln -s /usr/local/lib/heroku/bin/heroku /usr/local/bin/heroku
        - run:
            name: Deploy heroku app
            command: bash infra/deploy-heroku.sh
        - run:
            name: Set BUILD_NUM and COMPARE_URL on Heroku to CIRCLECI values
            command: |
            source ./setup-env.sh
            heroku config:set BUILD_NUM=${CIRCLE_BUILD_NUM} COMPARE_URL=${CIRCLE_COMPARE_URL} -a ${HEROKU_APP}

workflows:
    version: 2
    ci:
    jobs:
        - deploy:
            filters:
            branches:
                only:
                - develop
                - master
            # You should probably be running
            # some checks before you deploy
            # requires:
            # - test
            # - lint 
```

Enter fullscreen mode Exit fullscreen mode

这不是如何设置您的配置项的详尽描述，但这是一个开始。

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

请随时给我发短信，地址是[hi@codewithhugo.com](//hi@codewithhugo.com)，或者推特 [@hugo__df](https://twitter.com/hugo__df) 。

[奥利弗·罗斯](https://unsplash.com/@fairfilter?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)