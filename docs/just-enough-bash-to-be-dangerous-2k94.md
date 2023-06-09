# 足够危险的狂欢

> 原文：<https://dev.to/hugo__df/just-enough-bash-to-be-dangerous-2k94>

> 一个 bash 备忘单，适用于只想勉强度日的开发人员。

编写设置、CI 和部署流意味着使用一些旧的 bash 脚本。

尽管我对 Bash(*/讽刺*)的错综复杂深感兴趣，但我一直在 Google 和 StackOverflow 上寻找相同情况的解决方案。

为了避免我自己不得不再次这样做，也为了你的阅读乐趣，它们在这里。

就设置、CI 和部署流程而言，我们会遇到以下危险:

*   [检查文件是否存在](#check-if-file-exists)
*   [检查(符号)链接是否存在](#check-if-a-symbolic-link-exists)
*   [检查是否设置了环境变量](#check-if-environment-variable-is-set)
*   [切换环境变量](#switch-over-an-environment-variable)
*   [提示用户](#prompt-the-user)
*   [奖金](#bonus)
    *   拍你的。env 文件到您的环境中

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 检查文件是否存在

```
if [! -f ./pdfgen/pdfgen]; then echo "Building pdfgen binary"
    npm run --prefix pdfgen build:linux
else echo "Pdfgen binary already exists, skipping build"
fi 
```

Enter fullscreen mode Exit fullscreen mode

## 检查(符号)链接是否存在

```
if [! -L /usr/local/bin/heroku];
then wget https://cli-assets.heroku.com/branches/stable/heroku-linux-amd64.tar.gz
    sudo mkdir -p /usr/local/lib /usr/local/bin
    sudo tar -xvzf heroku-linux-amd64.tar.gz -C /usr/local/lib
    sudo ln -s /usr/local/lib/heroku/bin/heroku /usr/local/bin/heroku
fi 
```

Enter fullscreen mode Exit fullscreen mode

## 检查是否设置了环境变量

```
# long
if [[ -z "${CIRCLE_BRANCH}"] ]; then npm run redis-cli flushall
fi npm run sync

# one-liner
[-z "${CIRCLE_BRANCH}"] && npm run redis-cli flushall; npm run sync 
```

Enter fullscreen mode Exit fullscreen mode

## 切换环境变量

```
case $CIRCLE_BRANCH in
    "develop")
        export ENVIRONMENT="dev"
        export HEROKU_APP=dev-app
        ;;
    "staging")
        export ENVIRONMENT="staging"
        export HEROKU_APP=staging-app
        ;;
    "production")
        export ENVIRONMENT="production"
        export HEROKU_APP=production-app
        ;;
esac 
```

Enter fullscreen mode Exit fullscreen mode

## 提示用户

```
read -p "Are you sure you want to merge 'develop' into 'staging'? (y/N)" -n 1 -r
echo # we like having a new line

if [[$REPLY =~ ^[Yy]$ ]]
then git merge develop --ff-only
  git push
fi 
```

Enter fullscreen mode Exit fullscreen mode

最后一点建议，如果超过几行，尝试使用 JavaScript 或 Python 之类的东西来编写脚本。

在现代 JavaScript/Node 中，我有一些资源可以做到这一点:

*   [ES6 示例:在 docker-compose 中等待 Postgres 的模块/CLI](https://dev.to/hugo__df/es6-by-example-a-modulecli-to-wait-for-postgres-in-docker-compose-902)
*   [如何用 Node 制作漂亮简单的 CLI 应用](https://dev.to/hugo__df/how-to-make-beautiful-simple-cli-apps-with-node-c1b)

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 奖金

### 掴你的。env 文件到您的环境中

我们已经有了`.env`文件，Docker Compose 通常会处理这个问题，但是我们说我们想要在 Docker 之外运行一些东西(并且不使用像 [dotenv](https://github.com/motdotla/dotenv) 这样的东西)。

下面是一个*NIX shell 的代码片段:

```
export $(cat .env | xargs) 
```

Enter fullscreen mode Exit fullscreen mode

[【guilherme Cunha】](https://unsplash.com/@guiccunha?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)