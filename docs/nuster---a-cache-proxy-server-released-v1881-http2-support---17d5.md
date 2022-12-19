# nuster - A 缓存代理服务器发布版本 1.8.8.1: HTTP/2 支持

> 原文：<https://dev.to/nuster/nuster---a-cache-proxy-server-released-v1881-http2-support---17d5>

[nuster](https://github.com/jiangwenyuan/nuster) ，基于 HAProxy 发布的 v1.8.8.1 的缓存代理服务器，更新至 HAProxy v1.8.8，支持 HTTP/2。

## 简介

Nuster 是一个基于 HAProxy 的高性能缓存代理服务器。
与 HAProxy 100%兼容，充分利用 ha proxy 的 ACL
功能，根据
请求、响应或服务器状态的内容，提供细粒度的缓存策略。其特点包括:

*   HAProxy 的所有功能都是继承的，100%与 HAProxy 兼容
*   强大的动态缓存能力
    *   基于 HTTP 方法、URI、路径、查询、头、cookies 等
    *   基于 HTTP 请求或响应内容等
    *   基于环境变量、服务器状态等
    *   基于 SSL 版本、SNI 等
    *   基于连接速率、数量、字节等
*   极快
*   缓存清除
*   缓存统计
*   缓存管理
*   HTTPS 在前端和后端都支持
*   HTTP 压缩
*   HTTP/2
*   HTTP 重写和重定向

# 表现

Nuster 非常快，一些测试显示 nuster 在使用单核时几乎比
nginx 快三倍，在使用所有内核时几乎比 nginx 快两倍，比
快三倍。

参见[详细基准](https://github.com/jiangwenyuan/nuster/wiki/Web-cache-server-performance-benchmark:-nuster-vs-nginx-vs-varnish-vs-squid)