# 了解 nginx(代理、反向代理、负载平衡)

> 原文：<https://dev.to/legobox/understanding-nginx-proxying-reverse-proxying-load-balancing-1pjd>

> 这件作品最初发布在我的博客上，地址是 [legobox](https://medium.com/legobox/understanding-nginx-proxying-reverse-proxying-load-balancing-67f182573832)

## 背景

许多人认为 Nginx 只是一个 web 服务器，是的，它是一个 web 服务器，而且，Nginx 首先是一个代理引擎和负载平衡服务器机制，它能够作为一个 web 服务器工作，使用任何你喜欢的后处理解释器，无论是 uWSGI、php-fpm 等。

我们中的许多人都听说过 nginx，我们中的一些人正期待着了解它是什么，在这篇文章中，我们将很好地了解如何使用 nginx 作为负载平衡器和通用代理传递器。

## 预申请人

在启动并运行 nginx 之前，满足以下条件通常是很重要的(但不是强制性的),

*   你希望学习和理解更多的 DevOps
*   你已经了解了什么是 web 服务器
*   你以前用过电脑。😏

把这些都记下来，然后你就可以走了。

## 代理

我们要检验的第一个场景和结构是代理的概念。一般来说，代理就是将一台服务器放在另一台服务器前面，让所有请求通过第一台服务器到达第二台服务器。

在 nginx 中，这是通过使用`proxy pass`指令实现的。

```
 // basic proxy pass
    location / {
      proxy_pass http://ourserver2here.com
    } 
```

Enter fullscreen mode Exit fullscreen mode

关于代理通行证，有许多技巧，这些技巧对于理解设置代理通行证系统很重要，让我们来看看书中的一些技巧。

**匹配前缀**

```
 location /match/here {
      proxy_pass http://ourserver2here.com;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的设置中，在代理传递定义中，服务器端没有给出 URI。在这种情况下，诸如 **/match/here/please** 的请求将作为[https://ourserver2here/match/here/please](https://ourserver2here/match/here/please)被发送到 our server 2 here。很方便吧？

## 反向代理

这是一种特别有趣的代理类型，它是一种代理系统，发送到服务器(姑且称之为 A)的请求实际上被发送到另一个服务器(姑且称之为 B ),该服务器位于第一个服务器(服务器 A)的前面，并继续将消息中继到 A，并做出响应，就像 A 正在响应一样。

这里有一个更正式的描述。

> 在计算机网络中，**反向代理**是一种**代理**服务器，它代表客户端从一个或多个服务器检索资源。然后，这些资源被返回给客户端，就好像它们来自 Web 服务器本身一样。

这是一个虚拟的描述

[![source - wikipedia - such good people](img/d530d9f8c6d3498ed0a2a045ee059f3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZcwB_Ojj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_E03B7F3068651149406121A3CCB672B40D6EDF37D962EC28850BABB322302CCA_1527048473974_1200px-Reverse_proxy_h2g2bob.svg.png)

有了 nginx，我们可以做到这一点，甚至更进一步，听说过 ngrok 吗，也许你听过，也许没有，两种方式都有，

> Ngrok 是一种服务，允许您通过域代理服务本地主机应用程序，通过公共 URL 公开它。

有了反向代理，并在你的网站上为这类业务建立一个子域，你就可以拥有自己的 ngrok，而不用经常更换域名之类的东西。

要做到这一点，第一步是在您的服务器上设置 nginx，代理从您的特定子域或域(您认为方便的任何域)到服务器上特定端口的连接。

```
 server {
      listen 80;

      server_name coolsubdomain.mainname.com;

      location / {
        proxy_pass http://127.0.0.1:5000; #in this case we picked the port 5000
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在您的计算机上，假设我们的应用程序在本地主机端口 8000(实际上可以是任何端口)上运行，继续使用 ssh，我们运行到服务器端口的反向隧道。

```
 // run a reverse proxy to your server via ssh
    ssh -R 5000:127.0.0.1:8000 youruser@mainname.com 
```

Enter fullscreen mode Exit fullscreen mode

这将设置反向代理，因此任何发往 coolsubdomain.mainname.com 的请求实际上都是发往您的本地主机。

## 负载均衡

负载平衡是 nginx 解决的另一个问题，它可以处理大量的请求，如果我们有几个服务器在运行我们的项目，我们可以使用上游来平衡它们之间的负载。

Nginx 使用上游来平衡负载，默认情况下，它有一些算法来处理负载平衡机制。

这些算法是由指令指定的，可以注意如下。

*   **(round robin)** :在没有其他平衡指令的情况下使用的默认负载平衡算法。上游上下文中定义的每个服务器依次被传递请求。

*   **least_conn** :指定新连接应该总是给予具有最少数量的活动连接的后端。这在到后端的连接可能持续一段时间的情况下特别有用。

*   **ip_hash** :这个平衡算法根据客户端的 ip 地址将请求分发到不同的服务器。前三个二进制八位数用作决定服务器处理请求的关键。结果是客户端每次都倾向于由同一个服务器提供服务，这有助于会话的一致性。

*   **hash** :这个平衡算法主要用于 Memcached 代理。基于任意提供的散列关键字的值来划分服务器。这可以是文本、变量或它们的组合。这是唯一需要用户提供数据的平衡方法，数据是应该用于散列的密钥

在设置时，为了实现负载平衡，它可能看起来像这样。

```
 # http context
    upstream backend_hosts {
      least_conn;
      server host1.example.com;
      server host2.example.com;
      server host3.example.com;
    }

    # next we set up our server
    server {
      listen 80;
      server_name example.com;
      location /proxy-me {
        proxy_pass http://backend_hosts;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以给一个特定的主机增加权重，这样它们就可以按照一定的比例处理比其他主机更多的连接。

```
 ...
    upstream backend_hosts {
      least_conn;
      server host1.example.com weight=3;
      server host2.example.com;
      server host3.example.com;
    }
    ... 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们还可以使用 nginx 实现很多其他的事情，这只是冰山一角。设置 web 服务器和代理服务器通常会有点麻烦，但事实并非如此。

在这一类别下的下一篇文章中，我将探索如何使用 caddy 来设置和运行它，这是一种 web 服务器技术，旨在使设置 web 服务器和代理引擎的过程更加容易。