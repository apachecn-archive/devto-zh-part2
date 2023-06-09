# 把加密和对接捆绑在一起

> 原文：<https://dev.to/fdocr/tying-lets-encrypt-and-docker-swarm-together-4j16>

众所周知，在过去的一两年里，人们对 Kubernetes 的兴趣越来越浓厚。众所周知，它的学习曲线很陡，复杂性也相对较高。正因为如此，我对在[码头工人群体](https://docs.docker.com/engine/swarm/)中寻找一个更简单的替代方案感兴趣已经有一段时间了。下面是我的**实验**，用于建立一个能够使用[发布不同服务的 Swarm 集群，让我们轻松加密](https://letsencrypt.org/) SSL 证书**。**

 **## 为什么要蜂群？

我不打算谈论 Kubernetes 带来的好处(实际上是很多好处)，如果感兴趣，你可以自己去看看。我只列出 Docker Swarm 的几个好处，因为它们是我选择开始涉足这个主题的原因:

*   Swarm **在 Docker CE 中打包**,这意味着无需进一步安装
*   一个 Swarm 集群很容易启动和管理，只需要几个命令
*   机器/实例/液滴可以通过一个命令变成工作者节点
***   许多**出色的特性都不包含任何其他“项目”**，例如:
    *   服务发现
    *   服务内的负载平衡
    *   滚动更新**

 **尽管有这些(可能还有其他原因)，对我来说最重要的是:**我想少做些运营**。我不能强调这一点，因为我用这个项目来托管我的[博客](https://visualcosita.xyz/)和其他兼职项目。我的主要目标是**专注于这些服务的开发**，而不是跟上最先进的容器编排。

如果“嗡嗡声”在 Kubernetes 上，而 Swarm 被认为是无聊的技术，那么很好！这符合我的需求，这显然与员工全职从事运营/开发工作的公司不同。

## 我们内部服务网络的网关

因此，你想向公共互联网公开 3 个服务，它们是 3 个 web 应用程序，每个都依赖于 2 个私有服务。服务称为 A、B 和 C，而私人服务称为 A1、A2、B1、B2、C1 和 C2。你拥有域名**a.com**、**b.com**和**c.com**。最后，我们希望这个假设的系统看起来像这样:

[![Service Layout](img/6b91da5fdf7b270663585c0508443431.png "Service Layout")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DNW_CyzE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a1v9i6icd8gmlb1scwtb.png)

## 反向代理分离域请求到各自的服务

有许多不同的方法可以实现这一点。然而，我们确实希望有一种方法可以让每个域都可以使用“让我们加密”来访问 HTTPS。由于这并不容易(至少我觉得不可能)用现有的工具，我编写了一个非常简单的 CLI 来帮助实现自动化: [lecli](https://github.com/fdoxyz/lecli) 。

另外，[基于我不久前发布的一个尝试](https://visualcosita.xyz/post/publishing-services-using-docker-compose-and-nginx-with-https)，我决定在一个小的 NGINX 容器中接收所有传入的请求。这个入口点将处理 TLS 和相应的 A、B 和 C 服务的反向代理。这将实际上使所有服务私有，并且唯一可以访问端口`80` & `443`的面向公共的服务将是反向代理。

因此，实现这一点的一个示例 NGINX 配置片段文件应该是:

```
 http {
    server {
      listen 80 default_server;
      server_name a.com;

      location / {
        return 301 https://$server_name$request_uri;
      }

      location /.well-known/acme-challenge/ {
        proxy_pass http://5.39.70.224/;
      }
    }

    server {
      listen 443 default_server ssl;
      server_name a.com;

      ssl_certificate       /etc/nginx/ssl/swarm.pem;
      ssl_certificate_key   /etc/nginx/ssl/request.pem;

      location / {
        proxy_pass http://service-a:5000;
        proxy_read_timeout  90;
      }
    }

  ...
  # "b.com" and "c.com" should have an almost identical config
  ...

  } 
```

Enter fullscreen mode Exit fullscreen mode

这里，传入的请求被从 HTTP 重定向到 HTTPS **除了**当它们有“让我们加密 HTTP 挑战”表单时，这将很快有意义。这些请求被重定向到一个硬编码的 IP 地址，这将是我们的“支持服务器”的 IP 地址。

将使用加密 SSL 证书从端口`443`为 HTTPS 请求提供服务。由于这个 NGINX 代理将是我们集群中的另一个容器，我们需要从一个 docker 文件中创建它的映像，如下所示:

```
FROM nginx:alpine

COPY nginx.conf /etc/nginx/nginx.conf
COPY certificates/request.pem /etc/nginx/ssl/request.pem
COPY certificates/swarm.pem /etc/nginx/ssl/swarm.pem 
```

Enter fullscreen mode Exit fullscreen mode

我知道这可能看起来像是很多移动的部分，请原谅我。此时，我们有一个 docker 文件来创建一个反向代理，它将做 3 件事:

*   在 3 个网站上将 HTTP 重定向到 HTTPS:a.com、b.com 和 c.com
*   作为服务于每个域的 3 个 webapps 的反向代理(服务发现和负载平衡由群网络管理)
*   将 Let's Encrypt 域验证请求重定向到我们的“支持服务器”

我们现在可以继续创建一个 GitHub 私有 repo，使用`nginx.conf`和`Dockerfile`进行版本控制。我们所需要的是支持服务器定期更新证书( [lecli](https://github.com/fdoxyz/lecli) )，然后构建这个小的反向代理容器并在我们的集群上重新部署它。

## 支持服务器(在数字海洋上)

我知道这是懒惰的命名，甚至可能有更好的方法将这种服务器功能包含到集群中，而不需要单独的服务器实例。但是在试验了不同的方法之后，我在设置的简易性和整洁性之间找到了一个很好的平衡(是的，两者都是主观的)。

最后，许多云提供商会给你 5 美元/月或更少的实例，所以这不是什么大问题...[注册此链接，在 DigitalOcean 上免费获得 10 美元](https://m.do.co/c/a0486648b173),开始你的实验吧！(这也能帮我:winkwink:)

现在回到这个话题，这个支持服务器将有一个 cron 作业来执行 [lecli CLI](https://github.com/fdoxyz/lecli) 。去看看那个回购，看看自述文件，了解它的基本工作原理。除了用`gem install lecli`安装之外，您还需要创建一个`build_proxy.sh`脚本，在 lecli 成功生成新证书之后执行。大概如下:

```
#!/bin/sh
aws ecr get-login --region us-east-1 | cut -d' ' -f 1,2,3,4,5,6,9 | sh
docker build -t 123123123.ecr.us-east-1.amazonaws.com/r-proxy .
docker push 123123123.ecr.us-east-1.amazonaws.com/r-proxy
ssh -i /root/.ssh/swarm_manager root@10.2.2.2 'cd ~/server && bash -s' < deploy_stack.sh
ssh -i /root/.ssh/swarm_manager root@10.2.2.2 docker system prune -f
docker system prune -f 
```

Enter fullscreen mode Exit fullscreen mode

概括地说，该脚本将:

1.  登录到 [AWS ECR](https://aws.amazon.com/es/ecr/) ，您可以用任何其他私有容器注册表来替换它
2.  构建并推送反向代理容器映像
3.  SSH 进入 Swarm Manager(使用私有 IP ),远程执行`deploy_stack.sh`脚本(接下来需要创建)
4.  之后，它将只清理本地和管理器中的悬挂图像

`deploy_stack.sh`文件里面有什么？这只是一个在 Swarm 集群上重新部署堆栈的简单脚本。这意味着将从 ECR 中提取新的容器映像，然后使用一个新的反向代理容器，其中包含更新后的 Let's Encrypt 证书。`deploy_stack.sh`可以看起来像:

```
#!/bin/sh
aws ecr get-login --region us-east-1 | cut -d' ' -f 1,2,3,4,5,6,9 | sh
docker stack deploy -c docker-compose.yml --with-registry-auth stack_name 
```

Enter fullscreen mode Exit fullscreen mode

同样，该脚本在 Swarm manager 上运行，它将获得对 ECR 的访问权限(获取可用的新容器映像)并在集群上重新部署在`docker-compose.yml`文件中定义的堆栈。

到目前为止我还没有讲过如何**布局你的栈**，我也不会讲。你可以阅读 Swarm 文档或者我之前关于 Swarm 集群上的[负载平衡的帖子来获得灵感(坚持使用`docker-compose.yml`文件部分，但是**注意新版本的语法已经出来了**)。](https://visualcosita.xyz/post/publishing-services-using-docker-compose-and-nginx-with-https#compose-file)

## 确保让加密找到验证令牌

所以到现在为止，您应该已经:

*   指向您的集群的域 DNS(稍后将详细介绍)
*   您的反向代理转发到您的支持服务器让我们加密请求以验证域所有权

您现在需要支持服务器来响应这些请求。这可就有窍门了:

```
docker run -d --name simple-server --restart -p 80:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4 
```

Enter fullscreen mode Exit fullscreen mode

现在只需确保简单服务器容器的容量与[挑战 _ 相对 _ 路径](https://github.com/fdoxyz/lecli#lecliyml)相匹配，这样当 Let's Encrypt 请求时，lecli CLI 令牌就可以访问[。](https://github.com/fdoxyz/lecli#the-flow)

就是这样！您的群集的反向代理正在向您的支持服务器转发加密请求。

## 不同的目的(和尺度)不同的方法

我省略了 IP 地址指向您的集群的方式，因为反向代理必须在特定的 IP 地址上，并且您的域 DNS 记录指向那里。是吗？

实现这一点的一个简单方法是将可用的[选项](https://docs.docker.com/compose/compose-file/)用于`docker-compose.yml`服务放置。您可以设置一个专用的工作节点来托管反向代理容器。这样你就知道在哪个 droplet 上找到那个容器，并使用它的 IP 地址作为你的域名的 DNS 记录。

使用这个想法可以*破解*许多不同的解决方案。另一个可能的解决方案是让 3 个节点用于反向代理服务，然后只取一个实例的 IP 地址用于 a.com 的 DNS 记录，另一个 IP 地址用于 b.com 的 DNS 记录，第三个 IP 地址用于 c.com 的 DNS 记录。

不管使用哪种技术，都有优点和缺点，主要是考虑到作为入口点的液滴可能会停机。

由于**我的项目**不会面临大流量，我的解决方案是在主节点上安装反向代理。对于重要的生产设置，不建议这样做，因为如果您唯一的管理器节点关闭，它将带走您唯一的入口点。然而，这是非常容易设置使用的位置限制，从反向代理服务为:

```
placement:
  constraints:
    - node.role == manager 
```

Enter fullscreen mode Exit fullscreen mode

集群拓扑如下图所示，容器中的每个服务分布在工作节点上:

[![Manager Reverse Proxy Layout](img/fb4461e0fe44e06233e4f5bf6e6194d4.png "Manager Reverse Proxy Layout")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--njc8X8c6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/suy6r94bwgqzm1p3coza.png)

不难看出这里的瓶颈或单点故障。如前所述，无论出于何种原因，如果管理节点出现故障，整个集群都将无法运行。在负载很重的情况下，一台机器很难跟上，更何况它是基础设施中如此重要的一部分。

最终，我们都努力拥有拥有数百万用户的服务，对吗？随着我们的发展，我们将被迫扩展我们的基础架构以跟上他们的步伐，因此我确实有一个基于这些相同原则的拓扑，但它将提供一个更具可扩展性的解决方案。

作为免责声明，我还没有把这个建议的解决方案付诸实践，所以它仍然需要在现实生活中进行测试。我对其他方法的建议持开放态度，但[请听我说完这个](https://visualcosita.xyz/img/conspiracy_meme.jpg)...

## 一个负载均衡器在你的负载均衡器前面， [dawg](https://giphy.com/embed/Iiaru8TEGEbQs)

在 Docker Swarm 上实现高可用性时有一些最佳实践，比如拥有多个管理器节点，并且不在其上放置任何工作负载(可在`docker-compose.yml`上配置)。

为了分配反向代理负载，我们可以在每个工作节点上有一个反向代理。这意味着，如果一个节点关闭，我们仍然有所有其他工作节点能够接收任何传入的请求。要在每个 worker 节点上放置一个容器，我们可以启用`mode: global`设置。

这种解决方案的问题是，我们不能再依赖 DNS 记录向所有工作节点的 IP 地址转发。但是在 DigitalOcean 上，我们可以使用他们的[负载均衡服务](https://www.digitalocean.com/products/load-balancer/)(或者像 AWS 上的[ELB](https://aws.amazon.com/es/elasticloadbalancing/)这样的替代服务)

1.  确保用标签“worker node”标记所有 worker node droplets
2.  [使用**直通**策略创建负载平衡器](https://www.digitalocean.com/docs/networking/load-balancers/how-to/ssl-passthrough/)
3.  将负载平衡器设置为将所有传入流量转发给带有“worker node”标签的 droplets

拓扑现在应该类似于下图

[![DigitalOcean Reverse Proxy Layout](img/3e870f5ae5cb058197c7274728cb1b58.png "DigitalOcean Reverse Proxy Layout")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1ZipAPuS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpn2awtd76uh64r8kknf.png)

如果某些工作负载接管了某个节点的反向代理功能，则可以设置更复杂的设置。但是布局和它的设置仍然非常相似，所以现在不需要详细说明。

## 结论

对于大流量的生产系统，我不推荐这样做。有些事情可能会出错，通常在 Kubernetes 中实现会更好。然而，对于副业项目和其他较小的实现来说，这是一种相对简单的管理集群、运行容器工作负载和高效托管 webapps 的方法。

对于具有一般系统管理员/Docker 知识的人来说，这里没有什么可怕的。此外，没有必要跟上快节奏的 Kubernetes 生态系统。同样，不要误解我，在大多数情况下，Kubernetes 很可能是“这项工作的最佳工具”，只是不适合所有人，当然也不适合我(至少目前是这样)。

## CI/CD

我使用 CircleCI 和一点 ducktape(也称为 bash)用 CI/CD 设置了我的存储库。仍然依赖于前面的拓扑示例:对服务“B1”的 PR 将触发 CI。每当 PR 合并到 master 中时，就会触发一个容器构建(您猜对了，在我们的支持服务器中的**)并部署到集群中。这使得建立“容器化服务范式”的所有努力都是值得的。**

关于这一点的博客帖子(比这个集群设置容易得多，我保证从这里开始一切都会顺利)有望在不久的将来发布。在那之前，最好能听到反馈和替代设置，Pura Vida！

这篇文章最初发表于[visualcosita.com](https://visualcosita.com/tying-lets-encrypt-and-docker-swarm-together/)****