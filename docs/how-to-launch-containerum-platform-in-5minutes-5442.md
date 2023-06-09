# 如何在 5 分钟内启动 Containerum 平台

> 原文：<https://dev.to/voskvv/how-to-launch-containerum-platform-in-5minutes-5442>

[![containerum](img/dfd00530eb98f40e7abaa34407351b2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VL0l-m4K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qgaxqpaf65j0m290tns8.png)

Kubernetes 是 docker 容器的一个强大的编排者，拥有数量可观的用户和遍布全球的大型社区。然而，当涉及到配置和维护时，它的复杂性是出了名的。难怪社区正忙于开发新的扩展和插件来促进集群管理。

除了在生产中广泛使用的成熟平台，还有一些有趣的发展中项目。Containerum 是一个基于 Kubernetes 的平台，最初是作为一个基于云的商业产品推出的，但最近作为开源发布。

> 我认为这是私有软件在 DevOps 市场的自然发展，因为(我相信)开源和 DevOps 是天生的一对。

所以。Containerum 是一个基于 Kubernetes 的项目，提供了一些有趣的特性。我使用在线版本有一段时间了，我立刻喜欢上了它的简单 CI/CD 管道配置(我用 Travis 构建并部署了一个用 gulp 和 hugo 构建的网站)。开源版本提供了相同的特性，但是在探索它的特性之前，让我们试着安装它。我将在 Digital Ocean 上使用一台 3 RAM 1 CPU 的机器进行测试。开始吧！

## 先决条件

要启动 Containerum，您需要

*   工作中的 Kubernetes 集群(1.5 或更高)
*   头盔已安装
*   nginx 入口控制器(参见本指南)

## 安装

在本指南中，我们将做三件事:

1.  安装 nginx 入口控制器
2.  为入口控制器创建服务
3.  安装集装箱

### Nginx 入口控制器

我尝试了几种 nginx 入口控制器，我更喜欢的是 Kubernetes 资源库中的入口控制器。要安装它，请运行:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/mandatory.yaml 
```

搞定了。

### 为入口控制器创建服务

在我们安装了入口控制器之后，有必要创建一个用于连接入口控制器和 Containerum 入口的服务。用以下内容创建一个`yaml`文件:

```
apiVersion: v1
kind: Service
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
spec:
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
  - name: https
    port: 443
    targetPort: 443
    protocol: TCP
  selector:
    app: ingress-nginx
  externalIPs:
  - 127.0.0.1 
```

> 不要忘记在最后一行添加您机器的外部 IP 地址，而不是`127.0.0.1`。

将其另存为`ingress-svc.yaml`并从同一目录运行:

```
kubectl apply -f ingress-svc.yaml 
```

搞定了。

### 安装集装箱

好，现在让我们安装 Containerum。要安装所有组件，请运行:

```
helm repo add containerum https://charts.containerum.io
helm repo update
helm install containerum/containerum --version 1.0.17-rc.2 
```

现在检查所有 pod 是否都在运行:

```
kubectl get pods 
```

[![pods](img/08ecda8770d7cd4ded74d7155cf880d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UmnKlvnQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/azsv0etkuo56ko0bi00s.png) 
我们可以看到，Containerum 正在运行。现在让我们访问 Web 用户界面。

### 访问网络用户界面

要访问 Web UI，向您的`hosts`文件添加一个条目。打开`/etc/hosts`:

```
sudo nano /etc/hosts 
```

并将您的计算机的 IP 地址添加到主机列表中:

```
127.0.10.1 local.containerum.io api.local.containerum.io 
```

其中 127.0.10.1 是机器的 IP 地址。

现在转到`local.containerum.io`

[![containerum](img/dfd00530eb98f40e7abaa34407351b2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VL0l-m4K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qgaxqpaf65j0m290tns8.png)

恭喜你！您刚刚在集群中安装了 Containerum 平台！可以使用默认的登录/密码:
登录:[admin @ local . container um . io](mailto:admin@local.containerum.io)T3】密码:verystrongpassword

我将继续探索该平台，敬请关注！

感谢阅读！
弗拉德