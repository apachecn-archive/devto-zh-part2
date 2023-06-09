# 使用 Nginx、Letsencrypt 和 Capistrano 进行 HSTS 预加载。😎

> 原文：<https://dev.to/sonica/hsts-preloading-using-nginx-letsencrypt-and-capistrano-18l7>

小提示:HSTS 代表 HTTPS 严格的运输安全。

HSTS 是一种防止协议降级攻击的安全策略机制。简单来说，这意味着强迫浏览器在你的网站上总是使用`https`而不是`http`。HSTS 也防止 cookie 劫持，但是我们将在另一篇文章中讨论这个问题。

在下面的帖子中，我们将讨论如何快速配置你的网络服务器(nginx ),在所有后续请求中暗示 HSTS，然后*将这个规则硬编码到 Chrome 和其他主要浏览器中，默认为`https`使用 HSTS 预加载。*

我们现在在 [Bubblin 超级本](https://bubblin.io)上做这个。😇

我推荐阅读 Scott Helme 的博客，了解更多关于 HSTS 预压的信息。

> …希望在其网站上强制使用 SSL/TLS 的主机列表内置在浏览器中。该列表由谷歌编制，Chrome、Firefox 和 Safari 都在使用。这些站点不依赖于 HSTS 响应报头的发布来执行策略，相反，浏览器在任何连接或通信发生之前就已经知道主机需要使用 SSL/TLS。

这里的“预加载”部分是网站管理员提前告诉供应商他们的网站只在`https`上的一种方式。这样，浏览器就可以跳过不安全 http 上的降级请求。

### Nginx 的 HSTS 指令

现在我们使用老产品 [Capistrano](https://capistranorb.com/) 来自动部署 Bubblin，并且只通过 https 来提供图书，所以配置`nginx`到严格的`https`的预加载非常容易。我编辑了由 [capistrano3-nginx](https://github.com/treenewbee/capistrano3-nginx) gem 为 http - > https 重定向选择的模板，并在文件末尾添加了下面的指令到现在我们的[最新 nginx 配置模板](https://gist.github.com/marvindanig/0bdd7d8768cbf5eab2fc4782803df87d)。

```
 # Path to ./config/deploy/templates/nginx_conf.erb on your rails app
# Jump over to the last line!
… 

server {
    listen 80;
    listen [::]:80 ipv6only=on; 
    server_name <%= fetch(:nginx_server_name) %> www.<%= fetch(:nginx_server_name) %>;
    rewrite ^(.*) https://$host$1$request_uri permanent;
}

server {
  listen 443 ssl http2;
  listen [::]:443 ssl http2;  
  server_name www.<%= fetch(:nginx_server_name) %>;

  # Redirection from http --> https is mandatory.
  rewrite ^ https://<%= fetch(:nginx_server_name) %>$request_uri permanent; 

}

server {
  server_name <%= fetch(:nginx_server_name) %>;
  root <%= current_path %>/public;
  try_files $uri/index.html $uri @puma_<%= fetch(:nginx_config_name) %>;

  …
  …

  # Plenty of nginx configuration here.

  # SSL is mandatory for HSTS. We're using 
  # Certbot to manage Letsencrypt for us.

  listen 443 ssl http2; # managed by Certbot
  listen [::]:443 ssl http2;

  # Add HSTS header with preload. This is the line that does it.
  add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";

} 
```

底部的 HSTS 指令`add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";`会这样做，并告诉用户的浏览器在你的网站上总是使用`https`。

提交更改，然后重新部署(cap 生产部署)。现在前往 hstspreload.org。

这是我们提交我们的站点的地方，以便包含在 Chrome 的 HTTP 严格传输安全(HSTS) *预加载列表*中。被硬编码到 Chrome 中的网站列表只有`https`。大多数主流浏览器如 Firefox、Opera、Safari、IE 11 和 Edge 也有基于 Chrome 汇编的 HSTS 预加载列表。为了通过此表格被 HSTS 预加载列表接受并保留，您的站点必须永久满足以下一组要求:

```
1\. Serve a valid certificate.
2\. Redirect from HTTP to HTTPS on the same host, if you are listening on port 80.
3\. Serve all subdomains over HTTPS.
    In particular, you must support HTTPS for the www subdomain if a DNS record for that subdomain exists.
4\. Serve an HSTS header on the base domain for HTTPS requests:
    i. The max-age must be at least 31536000 seconds (1 year).
    ii. The includeSubDomains directive must be specified.
    iii. The preload directive must be specified.
    iv. If you are serving an additional redirect from your HTTPS site, that redirect must still have the HSTS header (rather than the page it redirects to). 
```

这里还应该注意的是，将 HSTS 预加载硬编码到浏览器中并不意味着安全性的所有方面都得到了考虑。它肯定有助于安全，而且在你的网络服务器的速度和性能上有一个小的提高，因为它不再需要确定和在安全和不安全的协议之间切换。

这就是 HSTS 人的全部。❤️

* * *

我是 Sonica Arora，Bubblin Superbooks 的首席技术官，你可以在 Twitter 上关注我。

**附言:**你知道 [Bubblin](https://bubblin.io) 是一种在你的 iPad 上阅读书籍的很酷的新方式吗？