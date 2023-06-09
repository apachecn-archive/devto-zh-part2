# 使用 Gitlab 自动构建 Docker 图像并将其推送到注册表中。

> 原文：<https://dev.to/imichael/automagically-build-and-push-docker-images-to-a-registry-using-gitlab-276p>

这是一个如何自动构建 Docker 映像并推送到 Gitlab(免费)上您自己的 Docker 注册表的配置示例。在这个例子中，我构建并测试了一个运行 Django 的 Docker 映像，但是同样的基本思想也适用于任何应用程序。

```
image: docker:latest

services:
  - docker:dind

stages:
- build
- test
- push

variables:
  DJANGO_TAG_NAME: registry.gitlab.com/<username>/<repo>/<image>:$CI_COMMIT_REF_NAME
  DJANGO_SETTINGS_MODULE: path.to.your.settings
  DATABASE_URL: postgres://postgres:xxxxxxx@postgres/postgres

before_script:
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com

build:django:
  artifacts:
    paths:
      - docker-images
  stage: build
  script:
    - mkdir docker-images
    - docker build --pull -t $DJANGO_TAG_NAME -f path/to/your/Dockerfile .
    - docker save $DJANGO_TAG_NAME > docker-images/app.tar
  only:
    - master
test:django:
  stage: test
  script:
    - docker load -i docker-images/app.tar
    - docker run --name test-postgres -e POSTGRES_PASSWORD=xxxxxxx -d postgres
    - docker run --rm --link test-postgres:postgres -v $(pwd):/<DOCKER WORKDIR> -e DJANGO_SETTINGS_MODULE=$DJANGO_SETTINGS_MODULE -e DATABASE_URL=$DATABASE_URL $DJANGO_TAG_NAME python manage.py test -v 2

push:django:
  stage: push
  script:
    - docker load -i docker-images/app.tar
    - docker push $DJANGO_TAG_NAME
  only:
    - master 
```

Enter fullscreen mode Exit fullscreen mode