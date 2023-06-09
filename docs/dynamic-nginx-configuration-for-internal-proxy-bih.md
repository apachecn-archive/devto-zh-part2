# 内部代理的动态 Nginx 配置

> 原文：<https://dev.to/danquack/dynamic-nginx-configuration-for-internal-proxy-bih>

在家庭网络中工作有时很有挑战性，尤其是涉及长期 ip 地址时。在进行 web 和 api 开发时，这带来了一个更大的问题，因为现在只有一个入口。为了解决这个问题，我考虑了许多选择，主要是设置 VPN，这将解决问题，但需要在每个客户端设备上进行额外的设置。相反，我决定研究 nginx 及其反向代理能力。

设计很简单，有一个单独的域，带有一个通配符掩码，可以解析到网络中的各个主机。然后，我想从外部确保 https 无处不在，因此利用[区块链调试器指南](https://levelup.gitconnected.com/how-to-get-certbot-wildcard-certificates-3d25618a81e0)，我在 edge 设置了一个通配符证书，然后允许我的网络内部的非认证流量。

[![Concept Design](img/c57f69a556a3e3e35c67a475d9317d4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nMhb7LvL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lucidchart.com/publicSegments/view/b1562db2-2055-4746-808b-a0ff0dc63e1e/image.png)

然后，我利用 Puppet 作为配置管理工具来部署和确保 nginx 服务持续运行。这也给了我模板化的配置能力。nginx config 用 erb 编写，是一个带有变量输入的标准文本文件，但关键组件是 proxy_pass 语句:

```
proxy_pass https://$subdomain.<%= @internaldomain %>; 
```

Enter fullscreen mode Exit fullscreen mode

一旦这个模板被放下，`<%= @internaldomain %>`将解析为我的内部域名。例如，假设我的内部域名是 home.com，外部域名是 example.com。如果我试图发送一个 http 请求到 foo.example.com，外部 dns 将解析到我的家，然后端口转发到 nginx 服务器。从那里，proxy_pass 语句将首先导出主机名 foo，并将我的请求代理到 foo.home.com。