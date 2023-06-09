# 幕后:通过 Traefik.io 向 Airstrip 添加自定义域映射

> 原文：<https://dev.to/firmhouse/behind-the-scenes-adding-custom-domain-mapping-to-airstrip-via-traefikio-dno>

在 [Firmhouse](https://firmhouse.com) 我们有一个名为[airport](https://firmhouse.com/products/airstrip)的产品，可以让人们快速建立一个网站来测试和推出他们的新业务主张。在我们目前的产品 sprint 中，我们正在改进一个功能，允许人们自动将自定义域映射到他们用 Airstrip 构建的网站。这篇文章解释了我们是如何做的。

我们已经有了一个定制的域映射特性，但是还没有完全自动化。它的工作方式是，我们将指导人们与我们的支持团队取得联系。然后，我们会告诉他们将他们的 DNS 记录指向我们的 IP 或 CNAME，然后我们会通过向我们的 [Dokku](https://github.com/dokku/dokku) 实例添加一个站点并重新运行`$ dokku letsencrypt`来手动配置他们的域。

这种工作方式耗时长，需要相当多的手工劳动。我们的用户也没有“即时分级”。如果你要等一天以上，你的自定义域名才能完全正常工作，那就太令人失望了！

我们也遇到了使用 [Dokku Letsencrypt](https://github.com/dokku/dokku-letsencrypt) 插件的限制。主要是:如果添加到我们在 Dokku 上的 airport 应用程序中的一个域设置了错误的 DNS 记录，那么所有的域都将无法更新证书。

我们的新计划是什么？

经过一些研究和考虑，我们将把 [Traefik](https://traefik.io) 添加到我们的基础设施中。Traefik 是一款“云原生边缘路由器”(他们网站上是这么说的)。现在我还不完全确定这是什么意思，但事实证明，在我们当前的基础架构之前，这是一个非常棒的自定义域映射薄层，并且可以同时自动加密这些域！我们现在需要的一切。

Traefik 将被放在我们目前依靠数字海洋水滴运行的基础设施的前面。web 和应用服务器 droplets 通过 Ansible 和 Intercity 进行供应和维护。droplets 运行 Dokku 进行应用部署和网络服务器配置。我们将添加另一个运行 Traefik 的 droplet。

我们将为 Traefik 配置一个由用户添加的自定义域的前端。每当有人在 Airstrip 用户界面中添加他们的自定义域时，我们将向 Traefik REST api 端点发出一个 API 调用。以这种方式创建的所有前端都连接到一个单独的*后端*:我们已经部署好的 Airstrip 应用服务器。然后，Traefik 将负责为添加到 Traefik 配置中的每个自定义域请求新的 LetsEncrypt 证书。

举例来说，这里有一个 Treafik 配置文件来说明这是如何工作的:

```
# Backends
[backends]

  [backends.backend1]

    [backends.backend1.servers]
      [backends.backend1.servers.server1]
        url = "https://airstrip.firmhouse.com:443"
        weight = 1

# Frontends
[frontends]

  [frontends.frontend1]
    backend = "backend1"
    passHostHeader = true

    [frontends.frontend1.routes]
      [frontends.frontend1.routes.route0]
        rule = "Host:traefik.firmhouse.com"

  [frontends.frontend2]
    backend = "backend1"
    passHostHeader = true

    [frontends.frontend2.routes]
      [frontends.frontend2.routes.route0]
        rule = "Host:traefik2.firmhouse.com" 
```

Traefik 能接手我们的工作真是太棒了。我们最初考虑编写一些自定义代码来动态添加/删除 Dokku 的域，或者通过其他方法更新我们的 webserver 和 LetsEncrypt 配置。与此相比，在我们的基础设施前设置一个小 Traefik droplet 只是一个小问题。

下一步是研究如何将一个键/值存储附加到 Traefik，这样我们的动态配置就不会在每次重启 Treafik 服务器或需要升级 droplet 时丢失。