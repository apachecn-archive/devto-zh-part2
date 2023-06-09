# 如何将 Ember 应用程序部署到 kubernetes

> 原文：<https://dev.to/oivoodoo/how-to-deploy-ember-app-to-kubernetes-2i2m>

作为我未完成的项目 [observerio](//github.com/observerio/observer-server) 的一部分，我正在制作我的第一个 ember 仪表板，当我完成了原型的工作后，我决定将它作为 pod 部署在数字海洋的 kubernetes 集群中。为什么 kubernetes 可能是节省了很多时间，如果你是单独的开发者，没有足够的钱来支持这个非盈利的想法。

在第一次部署时，我发现构建 ember 应用程序的发布版本非常容易，但在浏览器中打开仪表板时，我发现我的路由无法正常工作。在谷歌搜索解决方案后，有人建议改变 serve ember 应用程序上的 nginx 设置。

[docker . released](https://github.com/observerio/observer-server/blob/master/web/Dockerfile.release)pod 使用:

```
FROM alpine:3.6

COPY dist/ /app

WORKDIR /app

CMD ["tail", "-f", "/dev/null"] 
```

Enter fullscreen mode Exit fullscreen mode

如何更改 nginx 设置？我开始思考这个问题。我不知道 k8s 的内部内容，我的下一步是探索 kubernetes 集群中运行的进程的容器。在入口舱里，我找到了 nginx 模板，它用在了我的 ember 舱上。

[nginx.tmpl](https://github.com/kubernetes/ingress-nginx/blob/master/rootfs/etc/nginx/template/nginx.tmpl)

为了确保我将拥有与安装的入口容器相同的模板，我从容器中复制了 [nginx.tmpl](https://github.com/observerio/observer-server/blob/master/deployment/app-deploy/ingress/templates/nginx.tmpl#L458) 并放在那里(行:458):

```
{{ if and (eq $path "/") (eq $server.Hostname "observer.rubyforce.co") }}
            try_files $uri $uri/ /index.html?/$request_uri;
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

然后通过配置图更改入口控制器以使用新模板，例如[部署](https://github.com/observerio/observer-server/blob/master/deployment/app-deploy/ingress/03-deployment.yml#L67)

或许对某些人也有帮助。

感谢阅读！