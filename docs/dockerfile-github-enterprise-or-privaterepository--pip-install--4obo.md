# Dockerfileで Github 企业或私人仓库から皮普安装する

> 原文：<https://dev.to/yurfuwa/dockerfile-github-enterprise-or-privaterepository--pip-install--4obo>

如果使用的是 enterprise github，经常会想用 pip 安装自己的模块。
在构建私有文档图像时，从私有库进行 pip 的目的。

*   python:3.6 之类的 docker 的情况下，因为 ssh-agent 没有站起来，所以要站起来，一口气拿去注册密钥。
*   不写入 known_hosts 的话，就会作为不知道的网站向 interactive 咨询，被踢。

```
RUN eval `ssh-agent` && ssh-add -k any_your_key && mkdir -p ~/.ssh && ssh-keyscan -t rsa github.enterpise.domain > ~/.ssh/known_hosts \ && pip install --no-cache-dir -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

因为 RUN 后密钥注册会飞，所以如果不想在 Dockerimage 内包含密钥的话，也可以在 pip 后删除。