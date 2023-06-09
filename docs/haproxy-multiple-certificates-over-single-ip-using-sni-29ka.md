# 使用 SNI 在单个 IP 上代理多个证书。

> 原文：<https://dev.to/dviejopomata/haproxy-multiple-certificates-over-single-ip-using-sni-29ka>

# 使用 SNI 通过单个 IP 代理多个证书

你好！，我是一名 fullstack/devops 开发人员，我将开始分享周围问题的解决方案。在过去的两年里，我专注于 Kubernetes/Docker、NodeJS、Java 和 Angular/React。我将在下一篇文章中写软件解决方案。

我在 haproxy 中遇到了一个路由问题，我不知道为什么，我让 haproxy 使用 [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) (服务器名称指示)进行路由，这是 TLS 协议的一个扩展，对于在握手开始时获取主机名非常有用，因此我可以在多个后端上进行负载平衡，而无需握手。但是我发现，如果您使用 SNI 进行路由，并在多个主机名中提供相同的证书，您不会总是得到相同的结果。

为了看得更清楚，让我们举个例子。

具有 SNI 路由的 haproxy 的配置可能是这样的:

```
global
  log /dev/log    local0
  log /dev/log    local1 notice
  stats socket /haproxy-admin.sock mode 660 level admin
  stats timeout 30s
  daemon
defaults
  maxconn 1000
  mode http        
  log global
  option dontlognull # bind *:443 ssl crt .
  timeout http-request 5s
  timeout connect 5000
  timeout client 2000000 # ddos protection
  timeout server 2000000 # stick-table type ip size 100k expire 30s store conn_cur

frontend env_ssl_frontend
  bind *:443
  mode tcp
  option tcplog
  tcp-request inspect-delay 10s
  tcp-request content accept if { req_ssl_hello_type 1 }
  use_backend bk_app1 if { req.ssl_sni -m end app1.domain.com }
  use_backend bk_app2 if { req.ssl_sni -m end app2.domain.com }
  use_backend bk_app3 if { req.ssl_sni -m end app3.domain.com }

backend bk_app1
  balance source
  mode tcp
  option ssl-hello-chk
  server main 127.0.0.1:3000
backend bk_app2
  balance source
  mode tcp
  option ssl-hello-chk
  server main 127.0.0.1:3001
backend bk_app3
  balance source
  mode tcp
  option ssl-hello-chk
  server main 127.0.0.1:3002 
```

Enter fullscreen mode Exit fullscreen mode

前端看起来非常清晰，我们让它在 TCP 模式( [L4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) )下监听 443，并路由到 bk_app1、app2 或 app3，这取决于来自客户端 Hello 的 server_name，然后将流量发送到相应的应用。

在后端服务器中，你必须终止握手，否则握手将无法进行。

您可以通过安装 Docker 并使用之前的配置安装一个名为 haproxy.cfg 的文件来轻松测试这一点，而无需安装 ha proxy:

```
docker run -p 443:443 --name haproxy -v "${PWD}":/usr/local/etc/haproxy:ro haproxy:1.8 
```

Enter fullscreen mode Exit fullscreen mode

如果您想查看正在发送的服务器名称，您可以使用 [Wireshark](https://www.wireshark.org/)

[![Wireshark image](img/92f189e0484c430fe609baf36ac31ad6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rElaOz-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5trtj8h3xmtntr31b4pj.png)

使用此配置，如果您在 bk_app1 和 bk_app2 中提供相同的证书，您将基于当前握手被重定向。因此，您访问 app1.domain.com，然后访问 app2.domain.com，然后 app2.domain.com 将路由到 bk_app1。

所以你总是通过 SNI 路由，你必须根据证书重定向到相同的后端！

希望有帮助！

我打算写关于微服务、CI/CD 和 Kubernetes 的文章。所以，如果你想让我谈论任何主题，请随意提问！