# 使用 Gitlab 和 Scotty 持续部署您的前端。射流研究…

> 原文：<https://dev.to/imichael/continuously-deploy-your-frontend-using-gitlab-and-scottyjs--15j8>

我对 Gitlab 的热爱继续！这里有一个简短的帖子，向你展示如何使用 Docker、Gitlab 和一个叫做 [Scotty 的伟大工具，将你的 React/Vue/Angular 单页应用程序持续部署到 S3 和 Cloudfront。JS](https://github.com/stojanovic/scottyjs) 。

首先将`AWS_ACCESS_KEY_ID`和`AWS_SECRET_ACCESS_KEY`保存到 Gitlab 环境变量中(我推荐一个特殊的 IAM 角色)。然后将下面的文件保存到您的存储库中，并将占位符文本更新为对您的项目有意义的内容。

下面的 Dockerfile 执行一个多阶段构建，构建前端代码(Create-React-App)，然后将其全部复制到 Scotty 可以访问的目录中。

```
#Dockerfile
FROM node:8.11.3-slim as node_builder

MAINTAINER @iMichael

WORKDIR /app

COPY /src  /app/src

RUN cd /app/src && \
    npm install && \
    npm run build

FROM node:8.11.3-slim as deployer

RUN groupadd -r react && useradd -r -g react react

RUN npm install scottyjs --global

COPY --from=node_builder /app/src/build /tmp/build

USER react

ENTRYPOINT ["scotty"] 
```

Enter fullscreen mode Exit fullscreen mode

下面是将这一切联系在一起的 Gitlab 配置。用您的团队坚持的任何特殊逻辑来更新它(比如只有当所有测试都通过主分支时才进行部署)。

```
#.gitlab-ci.yml
image: docker:latest

services:
  - docker:dind

stages:
- build
- push
- deploy

variables:
  REACT_TAG_NAME: registry.gitlab.com/<user>/<project>/<container-name>:$CI_COMMIT_REF_NAME
  S3_BUCKET: xxxx

before_script:
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com

build:react:
  artifacts:
    paths:
      - docker-images
  stage: build
  script:
    - mkdir docker-images
    - docker build --pull -t $REACT_TAG_NAME -f path/to/your/Dockerfile .
    - docker save $REACT_TAG_NAME > docker-images/react.tar

push:react:
  stage: push
  script:
    - docker load -i docker-images/react.tar
    - docker push $REACT_TAG_NAME
  only:
    - master

deploy:react:
  stage: deploy
  script:
    - docker run \
      --rm  \
      -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
      -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  \
      $REACT_TAG_NAME  \
      --source /tmp/build/ \
      --region us-east-1 \
      --bucket $S3_BUCKET \
      --spa 
```

Enter fullscreen mode Exit fullscreen mode

现在每次在 Gitlab 上推送 master(并且所有测试都通过)。您的代码将通过 Cloudfront URL 部署到 AWS，您可以将该 URL CNAME 到您自己的域。

非常感谢 Scotty.js 完成了所有繁重的工作！