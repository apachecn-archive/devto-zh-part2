# 在您自己的定制域上公开 Kubernetes 服务

> 原文：<https://dev.to/peterj/expose-a-kubernetes-service-on-your-own-custom-domain-52dd>

你终于在 Kubernetes 上部署了你的应用程序，并且买了一个很酷的域名——有没有想过如何将你的很酷的域名像[www.mydomain.com](http://www.mydomain.com)一样指向一个在 Kubernetes 上运行的应用程序？好吧，继续读下去，我会试着解释如何做到这一点！

# 需要什么？

在我们开始之前，请确保您具备以下条件:

*   Kubernetes 集群和对它的访问(即你可以部署东西)
*   您在 Kubernetes 集群中运行的应用*
*   注册域名(我使用 Name.com 为我的域名，但任何其他注册商也可以)
*   舵

> 注意:我使用单词 **app** 来表示您在集群中运行的代码，您希望通过域名来访问它。您的代码运行在 Docker 映像和 Kubernetes pod 中，后者是部署的一部分，通过 Kubernetes 服务公开。但是**应用**是我用来指代这一切的。

以下是将域与您的服务联系起来的大致步骤:

*   创建入口资源
*   部署入口控制器
*   更新域记录以指向集群

# 从外部访问 Kubernetes

名为 **Ingress** 的 Kubernetes 资源负责管理对集群内部运行的应用程序的外部访问。通过 ingress，您可以定义规则，告诉 Kubernetes 如何将外部流量路由到您的应用程序。下面是一个入口资源的例子:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
     kubernetes.io/ingress.class: nginx
spec:
  rules:
  - host: www.mycoolapp.mydomain.com
    http:
      paths:
      - path: /
        backend:
          serviceName: mycoolapp
          servicePort: 80 
```

Enter fullscreen mode Exit fullscreen mode

然而，仅有这一资源是不够的，您还需要一个知道如何引导流量的控制器。我将使用 NGINX 入口控制器，但你也可以使用其他控制器(点击这里查看更多文档)。我们将使用[头盔](https://helm.sh)来部署 nginx-ingress 图表，如下所示:

```
helm install stable/nginx-ingress 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令负责安装 NGINX 控制器和默认后端。默认后端是一个“应用”,入口将默认指向它，或者在没有定义服务的情况下指向它。作为 NGINX 控制器的一部分，您还将获得一个 LoadBalancer 类型的服务——这是您将域指向的位置。

> 请注意，在部署控制器时，您可以调整和扭转过多的按钮和旋钮，这里有一个完整的列表。

部署 NGINX 入口控制器后，让我们通过运行以下命令来计算集群的 IP 地址:

```
kubectl get services --all-namespaces 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将列出在所有名称空间中运行的所有 Kubernetes 服务。您感兴趣的是任何类型为 **LoadBalancer** 的服务和任何具有**外部 IP** 集的服务(通常是同一个服务)。下面是该命令的示例输出:

[![virtuous-gopher is the name Helm picked for the NGINX controller](img/8c590268f0ba310d392660c8d1a8625d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5TxQgq_I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AhREGYMrC658q8fiu6UOTkA.png)

上图中`*nginx-ingress-controller`服务旁边的灰色值是您需要的。这是您将域指向的 IP 地址。

或者，如果您想更有趣，您也可以运行下面的命令，该命令将根据服务的类型(LoadBalancer)过滤所有服务，并返回名称和 IP 地址:

```
kubectl get svc --all-namespaces -o jsonpath='{range .items[?(@.spec.type=="LoadBalancer")]}{.metadata.name}:{.status.loadBalancer.ingress[0].ip}{"\n"}{end}' 
```

Enter fullscreen mode Exit fullscreen mode

# 将您的域指向集群

根据您注册域的位置，步骤可能会有所不同，但要点是相同的——您需要创建一个`A`和`CNAME DNS`记录来将您的域(主机)指向集群。

我的注册服务商是 Name.com，但我很确定其他注册服务商也有一些关于如何做的好文件。

[![Heavily redacted view of the DNS records for my domain](img/0d9ca296ed0eb913639d56fe5c11bf78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9VLPA0Jw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AsX7VWV8fB1IBxmAvI8jhqw.png)

我是这样做的:在 Name.com 上，我进入我的域名，打开 DNS 记录选项卡。从那里，我能够添加一个名为`www.mycoolapp.mydomain.com`的主机的 A 记录，作为回答，我输入了我的集群的 IP 地址。

类似地，我创建了一个`CNAME`记录并将`mycoolapp.mydomain.com`指向集群主机名(通常，您可以在您的云提供商设置中找到它)。你不用提供一个`CNAME`，一个`A`记录就够了。注意，如果你不提供`CNAME`，那么`mycoolapp.mydomain.com`将不会解析到你的 app！

# 测试一下！

启动您最喜爱的终端或浏览器，并导航至`mycoolapp.mydomain.com`。tadaaa——您应该能够得到响应(在我的例子中，我部署的应用程序是一个简单的 NGINX 容器，因此是默认的 NGINX 页面)

[![Yes, I own containers.social domain… and bunch more that I’ll never use](img/1f2ec254add23e3be4b218492d5f9f30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N8qtiroT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AN2GNi8lAGWwzru_4MYeHDQ.png)