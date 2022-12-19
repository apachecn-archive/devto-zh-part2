# 这一招让你在 Gitlab 上无限的 CI 分钟。

> 原文：<https://dev.to/imichael/this-one-trick-gives-you-unlimited-ci-minutes-on-gitlab-e92>

Gitlab 慷慨地向 Gitlab.com 的所有用户提供每月 2000 分钟的免费通话时间(感谢 Gitlab！).我一天部署很多次，所以在几天之内用掉所有 2K 分钟是很容易的。

以下是解决这个问题的方法:

*   启动预装 Docker 的新计算实例。我推荐[数字海洋](https://m.do.co/c/f1de666e40fd)，不过 [Vultr](https://www.vultr.com/?ref=7495712) 也不错。
*   用实际服务器 URL 中的值替换`CI_SERVER_URL`值。转到您项目的 CI 设置，并在“Runners”部分下获取您的注册令牌。然后更新 yml 文件中的`REGISTRATION_TOKEN`。
*   将下面的代码保存到一个名为`docker-compose.yml`的文件中。
*   运行`docker-compose up -d`

```
version: '2'
services:
  dind:
    restart: always
    privileged: true
    volumes:
    - /var/lib/docker
    image: docker:18.06.0-ce-dind
    command:
    - --storage-driver=overlay2

  runner:
    restart: always
    image: gitlab/gitlab-runner:alpine
    volumes:
    - ./gitlab/runner:/etc/gitlab-runner:Z
    environment:
    - DOCKER_HOST=tcp://dind:2375

  register-runner:
    restart: 'no'
    image: gitlab/gitlab-runner:alpine
    volumes:
    - ./gitlab/runner:/etc/gitlab-runner:Z
    command:
    - register
    - --non-interactived
    - --locked=false
    - --name=Docker Runner
    - --executor=docker
    - --docker-image=docker:18.06.0-ce-dind
    - --docker-volumes=/var/run/docker.sock:/var/run/docker.sock
    environment:
    - CI_SERVER_URL=https://XXXXXXXX.XXXXX
    - REGISTRATION_TOKEN=XXXXXXX 
```

Enter fullscreen mode Exit fullscreen mode