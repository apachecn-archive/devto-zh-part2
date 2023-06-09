# HTTP/2 真的值得吗？

> 原文：<https://dev.to/david_j_eddy/is-http2-really-worth-it-1m2e>

TL；大卫:是的，但是...

## 背景

自 1991 年发布 0.9 版本以来，HTTP 一直是构建 web 的基础协议。它几乎是每个网络通信设备所依赖的主干技术。24 年来，HTTP 基本上保持不变。但是随着时间的推移，科技也在进步。2015 年 HTTP2 向全世界发布。随着加密作为技术的一个强制性部分，它带来了安全性；通过多路复用，它提高了对资源请求的响应速度，通过推送功能，服务器甚至可以在客户机请求资源之前就对资源做出响应。所有这些以及更多的因素结合在一起，形成了一个令人印象深刻的协议版本。

[![](img/ed14e0e2d481e98187227598a1b89d93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---JSfMcRc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/HP_revised_8-4_Illo_1.png)

## 配置

我使用一个非常基本的 LEMP 堆栈设置了一个基本的 web 服务器，然后在 5 分钟内以每秒 10 个请求的速度滥用它。这提供了一组特定硬件的预期性能基准。然后我配置了一个 SSL 认证(HTTP/2 需要的)。随后对 Nginx 配置进行小的编辑，以相同的负载攻击相同的服务器。结果是 HTTP 1.1 和版本 2 之间的一对一比较。[回购可以在 GitHub](https://github.com/davidjeddy/how_effective_is_http2) 上评论。

<figure>[![](img/532476c1e973a576cb1b15580bf967fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s3354gY0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/htop.png) 

<figcaption>每秒 10 个用户持续 5 分钟，可怜的小 web 服务器。</figcaption>

</figure>

## 好的，坏的，丑陋的

#### 好人

性能显著提高。其中多路复用并行响应而不是串行响应的许多图像。此外，平均回复率似乎下降了约 10%

<figure>[![](img/3a1e7bd752c3ea97ee2044fc5d94c960.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PoVYhYIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/http_http2_results_1.png) 

<figcaption>反应越多，速度越快。没有我想的那么多，但仍然是一个进步。</figcaption>

</figure>

#### 坏事

这并不是一件坏事，但是 HTTP/2 需要 SSL 和相关的配置。几年前，SSL 认证曾经非常昂贵。感谢[让我们加密](https://letsencrypt.org/)，这不再是一个大问题。我不确定我是否会在大规模生产环境中使用 Lets Encrypt，但它能完成工作。了解 Linux 操作和 Nginx 配置。

<figure>[![](img/1d958f62524ac604b21b8de6403d5a37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7bKP1vCK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/http_http2_results_2.png) 

<figcaption>哦，看那个。更顺畅的结果处理和更快的响应。</figcaption>

</figure>

#### 丑陋的

性能提升没有我希望的那么多。其主要原因是 SSL 的处理。它确实需要 CPU 开销来验证和处理流量加密。如果你想变得有趣，可以将 SSL 处理卸载到一个负载均衡器，从而使服务器只处理请求。

<figure>[![](img/e11c0bad2a49f875f8da0333af34a042.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hsok8zWx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/http_http2_results_3.png) 

<figcaption>仅 HTTP:超过 100 个资源失败。100 以下的 HTTP/2。很好。</figcaption>

</figure>

## 额外资源

*   [谷歌网络基础 HTTP/2 简介](https://developers.google.com/web/fundamentals/performance/http2/)
*   [https://en.wikipedia.org/wiki/HTTP/2](https://en.wikipedia.org/wiki/HTTP/2)
*   [https://www . digital ocean . com/community/tutorials/how-to-set-up-nginx-with-http-2-support-on-Ubuntu-18-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-with-http-2-support-on-ubuntu-18-04)
*   [https://http2.akamai.com/](https://http2.akamai.com/)

[![](img/f419359cd03b4762b57eef5ec1f645ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k9R3A1xG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/HP_revised_8-4_Illo_2.png)

## 结论

总而言之，HTTP/2 缩短了响应时间，加强了加密，并且不难启用。使用更高级的特性(如 push)和一个缓存系统 HTTP/2 是对互联网技术的一个惊人的改进。

额外图片来自[https://chadhagen.com/](https://chadhagen.com/)。去帮帮他们吧，查德的工作太棒了。)