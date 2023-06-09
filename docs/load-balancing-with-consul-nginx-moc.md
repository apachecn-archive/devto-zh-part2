# 使用 Consul & Nginx 实现负载平衡

> 原文：<https://dev.to/hmartinezdev/load-balancing-with-consul-nginx-moc>

联合内容！用领事& Nginx 检查原[负载平衡。](https://hectormartinez.dev/posts/load-balancing-consul-nginx/)

最近几周，我使用了 Docker、Consul 和其他工具，所以我尝试用一个 API 对多个 Docker 容器进行负载平衡。我决定与你分享我是如何建立这个系统的，所以我写了这篇博文。我希望它在某些方面对你有用。我们将使用**consult**、 **Docker** 、 **Docker compose** 、 **Nginx** 、 **Python** 和 **Supervisord** 来创建一个负载平衡系统，当负载平衡的组扩大或缩小时，该系统会更新其配置。

## 要求

对您的机器的唯一要求是 Docker 和 Docker compose。你可以遵循 docker 安装指南[这里](https://www.docker.com/community-edition#download)。要安装 Docker compose，您可以使用`pip install docker-compose`。记住使用虚拟环境，这样你就不会污染你的全球 python 环境。

## 概述

[![](img/a73cee29a6867c5f14fb0081adae93c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QZv_9pLn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/overview-diagram.png)

在系统中，至少有 5 个容器在运行:

*   1 个负载平衡器
*   1 个 API(图中有两个)
*   3 台咨询服务器(形成一个集群)

每个容器都有自己的咨询代理运行，在那里注册服务。然后当地领事与领事团联系。每次更新 API 服务组时，负载平衡器都会更新其配置，因此它知道应该将请求重定向到哪些容器。

## 基础图像

因为我们所有 Docker 容器都将运行公共服务，所以我们可以创建一个基本映像。因为我们将在 Docker 容器中运行多个服务，所以我们将使用服务管理器。在这种情况下，Docker 在他们的文档中给出的一个选项是使用 **Supervisord** 来[管理多个服务](https://docs.docker.com/config/containers/multi-service_container/)。

因此，我们的基本图像将包含:

*   主管:管理流程。
*   领事代理:将与领事集群通信。
*   注册服务:将服务注册到当地咨询代理的“一次性”流程。

### 基础 Dockerfile

```
# Dockerfile
FROM ubuntu:18.04

# Same exposed ports than consul
EXPOSE 8301 8301/udp 8302 8302/udp 8500 8600 8600/udp 8300

# unzip and wget for consul installation, python3 for supervisord
RUN apt-get update && apt-get install unzip wget python3 python3-distutils -y

# Pip the PyPA way, setuptools and wheel (note the --no-cache-dir to prevent pollution)
RUN wget -q https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py && \
   python3 /tmp/get-pip.py pip setuptools wheel --no-cache-dir

# Supervisord from PyPI doesn't support Python 3, so I download the master branch from GitHub
RUN python3 -m pip install https://github.com/Supervisor/supervisor/archive/master.zip --no-cache-dir && \
   mkdir -p /var/log/supervisor /etc/supervisor/conf.d/

# Download consul and move it to the PATH
ENV CONSUL_URL https://releases.hashicorp.com/consul/1.2.2/consul_1.2.2_linux_amd64.zip
RUN wget -q $CONSUL_URL -O /tmp/consul.zip && unzip /tmp/consul.zip && mv /consul /bin/consul && \
   mkdir /etc/consul.d/

# Configuration for starting the consul process, for the one shot registering and for
# supervisord itself
COPY consul.conf /etc/supervisor/conf.d/consul.conf
COPY register.conf /etc/supervisor/conf.d/register.conf
COPY supervisord.conf /etc/supervisor/supervisord.conf
ENTRYPOINT ["supervisord", "-c", "/etc/supervisor/supervisord.conf"]

# Cleanup!
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 
```

Dockerfile 文件:

1.  暴露一些端口用于咨询通信。
2.  安装 unzip，python 和 wget，这样我们就可以安装和运行东西了。
3.  python 的安装包:`pip`、`setuptools`、`wheel`。
4.  安装 supervisord 来管理进程。
5.  安装领事。
6.  为超级用户添加配置文件。
7.  设置入口点以使用 supervisord。
8.  清理包列表和临时目录。

### 超级用户配置

```
# supervisord.conf
[unix_http_server]
file = /tmp/supervisor.sock ; the path to the socket file

[supervisord]
logfile = /var/log/supervisor/supervisord.log
logfile_maxbytes = 50MB ; max main logfile bytes b4 rotation; default 50MB
logfile_backups = 10 ; # of main logfile backups; 0 means none, default 10
loglevel = info ; log level; default info; others: debug,warn,trace
pidfile = /var/run/supervisord.pid
nodaemon = true
minfds = 1024 ; min. avail startup file descriptors; default 1024
minprocs = 200 ; min. avail process descriptors;default 200
directory = /tmp

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl = unix:///tmp/supervisor.sock ; use a unix:// URL for a unix socket

[include]
files = /etc/supervisor/conf.d/*.conf 
```

文件中有两个值得注意的地方:`nodaemon = true`，所以它会在前台和`[include]`段运行来加载`/etc/supervisor/conf.d/`里面的文件。

### 执政官+监督官

```
 # consul.conf
[program:consul]
command = consul agent -retry-join=%(ENV_CONSUL_SERVER)s -data-dir /tmp/consul -config-dir /etc/consul.d
stdout_capture_maxbytes = 1MB
redirect_stderr = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log 
```

我们运行 Consul 代理，它将尝试连接到定义的`CONSUL_SERVER`。此外，我们还设置了数据和配置目录。最后，我们将 stdout 和 stderr 重定向到文件`/var/log/supervisor/consul.log`。

### 注册+主管

```
# register.conf
[program:register]
command = register
startsecs = 0
autorestart = false
startretries = 1
redirect_stderr = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log 
```

该服务将负责将容器中运行的服务注册到本地 Consul。该服务期望在`PATH`中有一个名为`register`的可执行文件。由于将只运行一次，可执行文件本身应该小心或重试注册服务。

### 建筑形象

我们把所有这些文件放到一个文件夹中，我的文件夹名为`base-image`，在里面运行:

```
$ docker build . -t base-image:latest 
```

这将创建 Docker 图像，以便您可以在其他图像中使用它或从它启动一个容器。

## API

有了基础映像，我们可以在它的基础上构建 API。我已经使用 Python 和框架 Flask 创建了 API，当然，您也可以用您想要的语言来实现它。

### 带烧瓶的 API

```
# app.py
import socket

from flask import Flask
from flask import jsonify

app = Flask( __name__ )

@app.route("/hostname")
def hostname():
   return jsonify({"hostname": socket.gethostname()})

if __name__ == ' __main__':
   app.run() 
```

该 API 在`/hostname`中包含一个端点，它将返回 Docker 容器的主机名，因此我们可以确定负载平衡器是否正常工作。

### 注册脚本

```
# register.py
#! /usr/bin/env python3
import time

import requests

consul_register_endpoint = "http://localhost:8500/v1/agent/service/register"

template = {
   "name": "api",
   "tags": ["flask"],
   "address": "",
   "port": flask_run_port,
   "checks": [
      {"http": "http://localhost:5000/hostname", "interval": "5s"}
   ]
}

for retry in range(10):
   res = requests.put(consul_register_endpoint, json=template)
   print("Attempt num:", retry, "Response Status:", res.status_code)
   if res.status_code == 200:
      print("Registering successful!")
      break
   else:
      print(res.text)
   time.sleep(1)
else:
   print("Run out of retires. So something went wrong.") 
```

我已经使用 Python 实现了 register 可执行文件。它将尝试在本地咨询代理中注册服务 10 次，之后它将停止。这没有什么神奇的:一个简单的 PUT 请求到 Consul 的`/v1/agent/service/register`,传递正确的有效载荷。这里，我们告诉 Consul 检查与`/hostname`端点联系的服务的健康状况，但是您可以为健康状况检查创建另一个端点。

## 烧瓶+监控器

```
# flask.conf
[program:flask]
command = flask run --host=0.0.0.0
directory = /app/
stdout_capture_maxbytes = 1MB
redirect_stderr = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log 
```

我们将服务设置为从`/app/`目录开始，在那里我们将复制 API 的文件。我们使用`flask run --host=0.0.0.0`启动 API，因此我们应该设置环境变量`FLASK_APP`，我们将在 Dockerfile 中这样做。

### API Dockerfile

```
# Dockerfile

FROM base-image:latest

ENV PYTHONUNBUFFERED=1 LC_ALL=C.UTF-8 LANG=C.UTF-8 FLASK_APP=app.py
RUN mkdir /app
WORKDIR /app
RUN python3 -m pip install Flask requests -U
ADD . /app/

COPY flask.conf /etc/supervisor/conf.d/flask.conf
COPY register.py /bin/register
RUN chmod +x /bin/register 
```

因为我们在基本映像中有很多功能，所以这里我们只安装我们的 API，定义几个变量，安装一些依赖项，并复制注册脚本和服务配置。

### 运行和测试 API

现在我们有了构建 API 映像并运行它的所有文件:

```
$ docker build . -t api:latest

# We should define the CONSUL_SERVER, so it wont crash
$ docker run -d --rm -e CONSUL_SERVER=localhost --name api -p 5000:5000 api:latest
$ curl localhost:5000/hostname
{"hostname":"f9e373e68d2e"} 
```

又做了一件事！让我们跟随:)

## 负载均衡器

### 负载平衡器 Dockerfile

```
# Dockerfile
FROM base-image:latest

# APT tasks
RUN apt-get update && apt-get install gnupg -y

# Install nginx
COPY nginx.list /etc/apt/sources.list.d/nginx.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABF5BD827BD9BF62
RUN apt-get install nginx -y

# Consul template
RUN wget -q https://releases.hashicorp.com/consul-template/0.19.5/consul-template_0.19.5_linux_amd64.zip -O consul-template.zip && \
   unzip consul-template.zip && mv consul-template /bin/consul-template && rm consul-template.zip
RUN mkdir -p /etc/consul-templates/
COPY load-balancer.tpl /etc/consul-templates/load-balancer.tpl

# Our application
RUN python3 -m pip install requests --no-cache-dir
RUN useradd nginx
COPY nginx.conf /etc/nginx/nginx.conf
COPY default.conf /etc/nginx/conf.d/default.conf

# Supervisor files
COPY consul-template.conf /etc/supervisor/conf.d/consul-template.conf
COPY supervisor-nginx.conf /etc/supervisor/conf.d/nginx.conf

COPY register.py /bin/register

# Apt cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 
```

除了 Nginx 之外，负载平衡器也需要咨询模板。此外，由于我们是从官方的 Nginx 库安装 Nginx，我们需要将它们添加到列表中，并添加它们的密钥。我们还复制了 Nginx 的注册脚本和配置文件。

### 领事模板+主管

```
# consul-template.conf
[program:consul-template]
priority = 1
command = consul-template -template "/etc/consul-templates/load-balancer.tpl:/etc/nginx/conf.d/load-balancer.conf:supervisorctl restart nginx"
stdout_capture_maxbytes = 1MB
redirect_stderr = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log 
```

这里“神奇”的事情发生了:我们运行`consul-template`将模板呈现到 nginx 配置目录中，并运行`supervisorctl`重新启动 Nginx 服务，因此配置被重新加载。

### 负载平衡器模板

```
# load-balancer.tpl
{% raw %}
upstream backend {
   least_conn;
   {{ range service "api|passing" }}
   server {{ .Address }}:{{ .Port }};{{ end }}
}
{% endraw %}
server {
   listen 5000;
   server_name localhost;

   location / {
      proxy_pass http://backend;
   }
} 
```

在模板中，我们获得了“api”名称下所有服务的地址和端口。我们通过“通过”状态来过滤它们，所以我们不会向负载平衡添加任何不健康的 API。

### Nginx + Supervisord

```
# supervisor-nginx.ini
[program:nginx]
command = nginx
autostart = true
autorestart = unexpected
exitcodes = 0
redirect_stderr = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log 
```

启动 Nginx！

### Nginx 配置文件

```
# default.conf
server {
   listen 80;
   server_name localhost;

   location / {
      root /usr/share/nginx/html;
      index index.html index.htm;
   }

   error_page 500 502 503 504 /50x.html;
   location = /50x.html {
      root /usr/share/nginx/html;
   }
} 
```

默认文件，服务于 80 Nginx 的欢迎页面。

```
# nginx.conf
user nginx;
worker_processes 1;
daemon off;

error_log /dev/stdout warn;
pid /var/run/nginx.pid;

events {
   worker_connections 1024;
}

http {
   include /etc/nginx/mime.types;
   default_type application/octet-stream;

   log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                     '$status $body_bytes_sent "$http_referer" '
                     '"$http_user_agent" "$http_x_forwarded_for"';

   access_log /dev/stdout main;

   sendfile on;
   keepalive_timeout 65;
   include /etc/nginx/conf.d/*.conf;
} 
```

Nginx 的主配置文件。注意`daemon off`和`include *`。前者将阻止 nginx 进入后台，后者将包含放置在`/etc/nginx/conf.d/`中的文件，consul-template 将在那里呈现模板。此外，我们将输出和错误发送到`/dev/stdout`，以便 Supervisord 获取日志。

```
# nginx.list
deb http://nginx.org/packages/ubuntu/ bionic nginx
deb-src http://nginx.org/packages/ubuntu/ bionic nginx 
```

存储库…

### 注册可执行

```
# register.py
#! /usr/bin/env python3
import requests
import time

consul_register_endpoint = "http://localhost:8500/v1/agent/service/register"

template = {
   "name": "nginx",
   "tags": ["nginx"],
   "address": "",
   "port": 80,
   "checks": [
      {
         "http": "http://localhost:80",
         "interval": "5s"
      }
   ]
}

for retry in range(10):
   res = requests.put(consul_register_endpoint, json=template)
   print("Attempt num:", retry, "Response Status:", res.status_code)
   if res.status_code == 200:
      print("Registering successful!")
      break
   else:
      print(res.text)
   time.sleep(1)
else:
   print("Run out of retires. So something went wrong.") 
```

在这种情况下，我们使用 80 端口来检查健康状况，而请求将被发送到 5000。

## 复合坞站

拼图的最后一块:`docker-compose.yml`文件，它包含了我们所有的架构。

```
# docker-compose.yml
version: '3.7'
services:
   server-1:
      image: consul
      command: consul agent -server -bootstrap-expect=3 -data-dir /tmp/consul -node=server-1

   server-2:
      image: consul
      command: consul agent -server -bootstrap-expect=3 -data-dir /tmp/consul -retry-join=server-1 -node=server-2

   server-3:
      image: consul
      command: consul agent -server -bootstrap-expect=3 -data-dir /tmp/consul -retry-join=server-1 -node=server-3

   consul-ui:
   image: consul
   command: consul agent -data-dir /tmp/consul -retry-join=server-1 -client 0.0.0.0 -ui -node=client-ui
   ports:
      - 8500:8500

   api:
   image: api:latest
   environment:
      - CONSUL_SERVER=server-1

   load-balancer:
   image: load-balancer:latest
   environment:
      - CONSUL_SERVER=server-1
   ports:
      - 5000:5000 
```

docker compose 定义了一系列服务:

1.  3 个这样运行的 consul 服务器，连接到名为`server-1`的 docker(可能更少)。
2.  1 个 consul 代理以作为 UI 运行，向本地计算机公开其端口 8500。
3.  将`CONSUL_SERVER`定义为`server-1`的 API。
4.  将`CONSUL_SERVER`定义为`server-1`并且 5000 端口对本地机器开放的负载平衡器。

## 运行整个

现在是时候运行 docker-compose 了！

```
$ (cd base-image && docker build . -t base-image:latest)
$ (cd api && docker build . -t api:latest)
$ (cd load-balancer && docker build . -t load-balancer:latest)
$ docker-compose up -d
$ firefox http://localhost:8500 
```

您可能会看到 Consul UI 出现错误。请耐心等待，刷新一下，您应该会看到类似这样的内容:

[![](img/dcfb77c288cb84961d505052b7637bab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KBqGxqOF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/consul-ui.png)

如果您没有看到这一点，请使用`docker-compose logs [load-balancer|api]`检查日志，这将输出超级用户日志。要查看每个服务日志，请运行`docker-compose exec [load-balancer|api] supervisorctl`并使用 Supervisord 交互式控制台进行操作。

如果一切运行正常，您应该会看到这个工作。

```
$ curl http://localhost:5000/hostname
{"hostname":"XXXXXXXXXX"} 
```

## 缩放 API

下一步是扩展我们的 API，并检查负载平衡器是否得到更新。我们可以在缩放之前检查 Nginx 配置文件。

```
$ docker-compose exec load-balancer cat /etc/nginx/conf.d/load-balancer.conf
upstream backend {
   least_conn;

   server 172.19.0.3:5000;
}

server {
   listen 5000;
   server_name localhost;

   location / {
      proxy_pass http://backend;
   }
} 
```

现在，我们运行:

```
$ docker-compose up -d --scale api=3 
```

还将创建三个 API。

```
$ docker-compose exec load-balancer cat /etc/nginx/conf.d/load-balancer.conf
upstream backend {
   least_conn;

   server 172.19.0.9:5000;
   server 172.19.0.8:5000;
   server 172.19.0.3:5000;
}
[...] 
```

太好了！配置文件现已更新:)

[![](img/88c865db4aa2512e780df7793a8bfd7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OfEhUvkf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/consul-ui-scaling.png)

Consul UI 中 API 的数量也增加了。如果我们向本地主机发起大量请愿:5000…

```
$ curl http://localhost:5000/hostname
{"hostname":"6215fd5a5651"}
$ curl http://localhost:5000/hostname
{"hostname":"16f52ff4f50e"}
$ curl http://localhost:5000/hostname
{"hostname":"ef97072c7cca"} 
```

有效:)

## 缩减 API

现在想象一下，服务收到的请求越来越少，所以它自动缩减 API 容器的数量。我们将使用 Docker compose:
来完成它

```
$ docker-compose up -d --scale api=2 
```

其中一个 API 容器将被停止并移除。配置文件已更新，curls 将只返回两个主机名。

```
$ curl http://localhost:5000/hostname
{"hostname":"1c3b38502212"}
$ curl http://localhost:5000/hostname
{"hostname":"ef97072c7cca"} 
```

## 总结

我们使用 Consul、Consul 模板和 Nginx 创建了一个带有负载平衡器的系统，当负载平衡组发生变化时，它会进行更新。在我的例子中，我用 Python 和 Flask 构建了 API，但是用其他框架实验和构建你自己的测试 API！我们在 Docker 容器中运行，由 Docker compose 编排。您可以在以下网址查看该存储库

我希望你学到了新的东西！如果没有，留下评论，求内容！