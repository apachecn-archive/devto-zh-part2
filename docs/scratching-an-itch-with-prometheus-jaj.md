# 用普罗米修斯挠痒痒

> 原文：<https://dev.to/w0rddriven/scratching-an-itch-with-prometheus-jaj>

不久前我迷上了普罗米修斯。我听说它有一段时间了，知道它很强大，但不太明白所有的东西是如何组合在一起的。文档非常冗长，这是有原因的，但它花了一段时间才能让一切都变得清晰。[这篇文章](https://ordina-jworks.github.io/monitoring/2016/09/23/Monitoring-with-Prometheus.html)是一个相当简洁和广泛的概述，在向我的开发者大脑表达基本概念方面走了很长的路。最简单的形式是，导出器公开一个 HTTP 端点`/metrics`,输出是普罗米修斯格式的统计数据。当您暴露自己的`/metrics`端点并让 Prometheus 使用您生成的统计数据时，Prometheus 的真正威力就显现出来了。[这篇文章也是一个很好的介绍](https://blog.alexellis.io/prometheus-monitoring/)，其中`Building your own exporter`部分在描述一些可能性方面非常有价值。

在找到方向后，我从一个简单前提的原型开始“为什么要单独查看数字海洋中每台服务器的使用图表？为什么不把它放在一个地方呢？”[如何在 Ubuntu 16.04 上安装 Prometheus](https://www.digitalocean.com/community/tutorials/how-to-install-prometheus-on-ubuntu-16-04)是一个非常好的入门，可以让一切快速启动并运行。

在阅读完这篇文章后，我做了一些修改:

*   [普罗米修斯版本 2.3.1](https://github.com/prometheus/prometheus/releases/tag/v2.3.1)
    *   v2.3.x 中有大量性能改进。
*   [node_exporter 版本 0.16.0](https://github.com/prometheus/node_exporter/releases/tag/v0.16.0)
    *   指标命名约定有重大变化。
    *   这个导出器通常与 Grafana 仪表板耦合最多，通常需要修改它们才能正常工作。
*   使用`prometheus:prometheus`获得核心 prometheus 进程的所有权，如`prometheus`或`alertmanager`。
    *   `sudo useradd --no-create-home --shell /bin/false prometheus`
*   使用`prometheus-exporter:prometheus-exporter`表示出口商的所有权。出口商可能应该更加孤立，但我觉得这可能是 YAGNI 的情况。
    *   `sudo useradd --no-create-home --shell /bin/false prometheus-exporter`
*   将 scrape_interval 设置为 1 分钟:`scrape_interval: 1m`。
    *   15 秒仍然是可行的，但我目前不关心非常具体的细节。
    *   这将每分钟进行 4 次调用的负载减少到只有 1 次，减少了 Prometheus 和每个出口商所需的一些开销。

在$dayJob，我们已经使用 Laravel Forge 提供服务器，它有可能使用 mysqld、mariadb、postgres、memcached、redis、beanstalkd、nginx、php-fpm 和 sendmail 的导出器。我选择了分别使用 [node_exporter](https://github.com/prometheus/node_exporter) 、 [mysqld](https://github.com/prometheus/mysqld_exporter) 、 [nginx-vts-exporter](https://github.com/hnlq715/nginx-vts-exporter) 、 [php-fpm](https://github.com/hipages/php-fpm_exporter) 和 [redis](https://github.com/oliver006/redis_exporter) 。为了正确理解最初的前提，在数字海洋中复制更新的监控代理图只需要`node_exporter`。一些导出器只需要很少的设置，只需要设置几个配置变量 systemd 和服务定义。其他出口商如`nginx-vts-exporter`要求从源头构建 nginx。

我计划介绍一系列帖子，这些帖子应该有助于运行一个非常基本的实现。在 Prometheus 生态系统中大量使用 Kubernetes，几乎到了必须使用的地步，但幸运的是，它在传统虚拟机中也能正常工作(tm ),没有任何真正的麻烦。