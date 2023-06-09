# 使用 Docker 和 Nginx 进行横向扩展

> 原文：<https://dev.to/usamaashraf/scaling-out-with-docker-andnginx-315g>

12 条戒律中的第 10 条指出，我们的开发环境应该尽可能与生产环境相似。如果不这样做，在调试关键的实时问题时会导致许多困境。

这方面的一个重要步骤是模拟一个标准的分布式生产设置:*一个位于多个后端服务器实例前面的负载平衡器，在它们之间分配传入的 HTTP 流量。*

**本文不是对 Docker、Compose 或 Nginx** 的介绍。而是建立上述分布式系统的指南，假设我们已经安装了 Docker，并且熟悉图像、容器等。我会尽量提供足够多的关于 Compose 和 Nginx 的信息，这样我们就可以不被通常伴随它们的噪音所困扰。

我们的每个后端服务器实例(simple Node.js 服务器)和 Nginx 负载均衡器都将被托管在基于 Docker 的 Linux 容器中。如果我们坚持使用 3 个后端服务器和 1 个负载均衡器，我们将需要管理/供应 4 个 Docker 容器，Compose 是一个很好的工具。

### **我们想要什么**

[![](img/4930a06c8aadcdfc2a11107d3f6e16d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qIugYQT5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ArfsMMw_wvO5j6hiMCt3WtA.png)

下面是我们的目录结构:

```
docker-nginx/
       backend/
              src/
                 index.js
                 package-lock.json  
              Dockerfile
       load-balancer/
              nginx.conf
              Dockerfile
       docker-compose.yml 
```

**src** 目录将包含我们的服务器端代码，在这里是一个简单的 Hello World Node (Express)应用程序(当然你的后端可以是任何东西):

```
// index.js
const express = require('express');
const app = express();

app.get('/', (req, res) =>  {      
   console.log('I just received a GET request on port 3000!');
   res.send('Hello World!');
});

app.listen(3000, () => console.log('I just connected on port 3000!')); 
```

[package-lock.json](https://gist.github.com/UsamaAshraf/1c1219fb44b8d044fb6ce8f67ef6a10b#file-package-lock-json) 除了一个 [Express](https://expressjs.com) 依赖之外什么都没有。

### **设置 docker 文件和撰写配置**

`backend/Dockerfile`将用于构建一个映像，然后由 Compose 用于提供 3 个相同的容器:

```
# Use one of the standard Node images from Docker Hub FROM node:boron
# The Dockerfile's author LABEL Usama Ashraf
# Create a directory in the container where the code will be placed RUN mkdir -p /backend-dir-inside-container
# Set this as the default working directory.
# We'll land here when we SSH into the container. WORKDIR /backend-dir-inside-container
# Our Nginx container will forward HTTP traffic to containers of 
# this image via port 3000\. For this, 3000 needs to be 'open'. EXPOSE 3000 
```

`load-balancer/Dockerfile`:

```
# Use the standard Nginx image from Docker Hub FROM nginx
# The Dockerfile's author LABEL Usama Ashraf
# Copy the configuration file from the current directory and paste 
# it inside the container to use it as Nginx's default config. COPY nginx.conf /etc/nginx/nginx.conf
# Port 8080 of the container will be exposed and then mapped to port
# 8080 of our host machine via Compose. This way we'll be able to 
# access the server via localhost:8080 on our host. EXPOSE 8080

# Start Nginx when the container has provisioned. CMD ["nginx", "-g", "daemon off;"] 
```

`load-balancer/nginx.conf`:

```
http {

 events { worker_connections 1024; }
 upstream localhost {
    # These are references to our backend containers, facilitated by
    # Compose, as defined in docker-compose.yml   
    server backend1:3000;
    server backend2:3000;
    server backend3:3000;
 }
 server {
    listen 8080;
    server_name localhost;
    location / {
       proxy_pass http://localhost;
       proxy_set_header Host $host;
    }
  }
} 
```

这是一个基本的 Nginx 配置文件。如果有人想获得更多高级选项的帮助，请发表评论，我很乐意提供帮助。

`docker-compose.yml`:

```
version: '3.2'
services:
  backend1:
      build: ./backend
      tty: true
      volumes:
        - './backend/src:/backend-dir-inside-container'
  backend2:
      build: ./backend
      tty: true
      volumes:
        - './backend/src:/backend-dir-inside-container'
  backend3:
      build: ./backend
      tty: true
      volumes:
        - './backend/src:/backend-dir-inside-container'
  loadbalancer:
      build: ./load-balancer
      tty: true
      links:
          - backend1
          - backend2
          - backend3
      ports:
          - '8080:8080'
volumes:
  backend: 
```

无需深入细节，下面是对我们的组合配置的一些见解:

单个组合*服务*通常使用一个图像，由 Dockerfile 定义。当我们构建服务时，映像被构建为容器。
*如果你是 Docker 新手，但熟悉虚拟机，那么这个类比可能会有帮助:VirtualBox (Compose)使用操作系统(映像)的 ISO 文件来启动正在运行的虚拟机(容器)。一个服务至少由一个运行的容器组成。*

`build`告诉 Compose 在哪里寻找 docker 文件来为服务构建映像。
`tty`只是告诉容器保持运行，即使 Dockerfile 中没有通过 CMD 指定的守护进程。否则，它会在配置后立即关闭(我知道这听起来很奇怪)。
`volumes`在我们的例子中定义了将服务器端代码放在容器中的何处(过于简化)。卷是容器中的一种存储机制，并且[不是一个微不足道的特性](https://docs.docker.com/storage/volumes/)。
`links`做两件事:确保 loadbalancer 服务不会启动，除非后端服务已经启动。它允许 backend1、backend2 和 backend3 在 loadbalancer 中用作引用，这是我们在 nginx.conf.
`ports`中所做的。指定主机端口和容器端口之间的映射。容器的 8080 将接收向主机上的 localhost:8080 发出的客户端请求。

### **发射**

在`docker-nginx`中运行`sudo docker-compose up --build`(或者无论你的项目根是什么)。在所有的服务启动后，运行`sudo docker ps`，你会看到类似下面的东西，一个所有刚刚启动的容器的列表:
[![](img/13ae6cec6e4c77f2c1e7d1b8a07ac418.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_zcpMhMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tz67blas6m9c39jngymw.png)

**让我们通过 SSH 进入一个后端容器**并**启动我们的节点服务器**。在我们的浏览器中点击`localhost:8080`5 次后，我们得到的是:
[![](img/419b8f472bd878280153c851e5f1eb92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oHgpYm8E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g4xzvb08nm0savg3h3rr.png)

当然*浏览器在主机上点击 8080，主机已经映射到 Nginx 容器上的 8080，Nginx 容器又将请求转发到后端容器的端口 3000。因此，日志显示端口 3000 上的请求。*

在您的主机上打开两个新的终端，SSH 到另外两个后端容器，并启动两个容器中的节点服务器:

`sudo docker exec -it dockernginx_backend1_1 bash`
T1】

`sudo docker exec -it dockernginx_backend3_1 bash`
T1】

**在你的浏览器上点击`localhost:8080`多次(快速)，你会看到请求被分配到 3 个服务器上！**
现在你可以模拟会话、跨多台服务器的缓存持久性、并发性问题，甚至可以大致了解我们向外扩展时可以实现的吞吐量增加(例如，通过使用 [wrk](https://github.com/wg/wrk) )。

所有这些都在一个地方。

为了在生产中部署类似的合成设置，我推荐[这个](https://docs.docker.com/compose/production/)；如果你需要出口到 Kubernetes，你可能会喜欢 [kompose](https://github.com/kubernetes/kompose) 。

*注意:更有经验的人可能会提出 [Compose 扩展正在运行的服务](https://docs.docker.com/compose/reference/scale/)的能力，并询问是否有可能配置 Nginx 以便在运行时自动扩展:有点像。Nginx 守护进程将不得不重启(停机),当然我们需要一种动态编辑和添加到上游服务器组的方法，这当然是可能的，但在这种情况下，这是一个徒劳的麻烦。如果需要更多服务器实例，请添加更多服务并重建映像。*