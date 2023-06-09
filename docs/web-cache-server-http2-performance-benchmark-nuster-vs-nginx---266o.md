# Web 缓存服务器 HTTP/2 性能基准:nuster 与 nginx

> 原文：<https://dev.to/nuster/web-cache-server-http2-performance-benchmark-nuster-vs-nginx---266o>

这是一个比较 [nuster](https://github.com/jiangwenyuan/nuster) 和 nginx 的 HTTP/2 缓存性能的简单基准。

[https://github.com/jiangwenyuan/nuster](https://github.com/jiangwenyuan/nuster)

我用`h2load`测试了 RPS(每秒请求数)。

测试显示，nuster 的速度几乎是 nginx 的三倍。

下面是当请求包含`hello world`数据的`/helloworld` URL 时的结果(RPS)。

| 数据量 | 指挥操舵 | 努斯特 | nginx |
| --- | --- | --- | --- |
| 12(hello world) | One thousand | Three hundred and thirty-eight thousand nine hundred and twenty-four | One hundred and ten thousand four hundred and nineteen |

# 测试环境

## 服务器

使用两个 linux 服务器，服务器 129 上的原始 web 服务器和服务器 130 上的缓存服务器 nuster/nginx。

| 计算机网络服务器 | 港口 | 应用 |
| --- | --- | --- |
| 10.0.0.129 |  | wrk |
| 10.0.0.129 | Eight thousand and eighty | nginx，原始网络服务器 |
| 10.0.0.130 | Eight thousand and eighty-one | nuster，所有内核 |
| 10.0.0.130 | Eight thousand and eighty-three | 坚尼克斯，各种颜色 |

源 web 服务器:设置`server_tokens off;`使 http 头`server`相同。

## 硬件

*   英特尔至强处理器 X5650，2.67GHz(12 核)
*   内存 32GB
*   1Gbps 以太网卡

## 软件

*   厘斯:1708 年 4 月 7 日(核心)
*   h2load: nghttp2/1.30.0
*   nginx: nginx/1.14.0
*   nuster: nuster/1.8.8.2

# 系统设置

## /etc/sysctl.conf

```
fs.file-max                    = 9999999
fs.nr_open                     = 9999999
net.core.netdev_max_backlog    = 4096
net.core.rmem_max              = 16777216
net.core.somaxconn             = 65535
net.core.wmem_max              = 16777216
net.ipv4.ip_forward            = 0
net.ipv4.ip_local_port_range   = 1025       65535
net.ipv4.tcp_fin_timeout       = 30
net.ipv4.tcp_keepalive_time    = 30
net.ipv4.tcp_max_syn_backlog   = 20480
net.ipv4.tcp_max_tw_buckets    = 400000
net.ipv4.tcp_no_metrics_save   = 1
net.ipv4.tcp_syn_retries       = 2
net.ipv4.tcp_synack_retries    = 2
net.ipv4.tcp_tw_recycle        = 1
net.ipv4.tcp_tw_reuse          = 1
net.ipv4.tcp_timestamps        = 1
vm.min_free_kbytes             = 65536
vm.overcommit_memory           = 1 
```

Enter fullscreen mode Exit fullscreen mode

## /etc/security/limits . conf

```
* soft nofile 1000000
* hard nofile 1000000
* soft nproc  1000000
* hard nproc  1000000 
```

Enter fullscreen mode Exit fullscreen mode

# 配置文件

## nuster，所有核心

```
global
    maxconn 1000000
    nuster cache on data-size 1g
    daemon
    nbproc 12
    tune.maxaccept -1
    tune.ssl.default-dh-param 2048
    tune.h2.max-concurrent-streams 1000
defaults
    retries 3
    maxconn 1000000
    option redispatch
    option dontlognull
    timeout client  300s
    timeout connect 300s
    timeout server  300s
    http-reuse always
frontend web1
    bind *:8081 ssl crt asdf.pem alpn h2,http/1.1
    mode http
    default_backend app1
backend app1
    balance roundrobin
    mode http
    nuster cache on
    nuster rule all ttl 0
    server a2 10.0.0.129:8080 
```

Enter fullscreen mode Exit fullscreen mode

## engine，所有颜色

```
user  nginx;
worker_processes  auto;
worker_rlimit_nofile 1000000;
error_log  /var/log/nginx/errorall.log warn;
pid        /var/run/nginxall.pid;
events {
  worker_connections  1000000;
  use epoll;
  multi_accept on;
}
http {
  include                     /etc/nginx/mime.types;
  default_type                application/octet-stream;
  access_log                  off;
  sendfile                    on;
  server_tokens               off;
  keepalive_timeout           300;
  keepalive_requests          100000;
  tcp_nopush                  on;
  tcp_nodelay                 on;
  client_body_buffer_size     128k;
  client_header_buffer_size   1m;
  large_client_header_buffers 4 4k;
  output_buffers              1 32k;
  postpone_output             1460;
  open_file_cache             max=200000 inactive=20s;
  open_file_cache_valid       30s;
  open_file_cache_min_uses    2;
  open_file_cache_errors      on;
  proxy_cache_path /tmp/cache_all levels=1:2 keys_zone=STATIC:10m inactive=24h max_size=1g;
  server {
    listen 8083 ssl http2;
    ssl on;
    ssl_session_cache shared:SSL:5m;
    ssl_session_timeout 1h;
    http2_max_concurrent_streams 1000;
    http2_max_requests 10000000;
    ssl_certificate     a.pem;
    ssl_certificate_key a.key;

    location / {
      proxy_pass        http://10.0.0.129:8080/;
      proxy_cache       STATIC;
      proxy_cache_valid any 1d;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 基准

### nuster

```
$ h2load -n 10000000 -c 1000 -m 1000 -t 1000 https://10.0.0.130:8081/helloworld

finished in 29.51s, 338924.15 req/s, 48.81MB/s
requests: 10000000 total, 10000000 started, 10000000 done, 10000000 succeeded, 0 failed, 0 errored, 0 timeout
status codes: 10000000 2xx, 0 3xx, 0 4xx, 0 5xx
traffic: 1.41GB (1510024000) total, 1.13GB (1210000000) headers (space savings 34.24%), 114.44MB (120000000) data
                     min         max         mean         sd        +/- sd
time for request:    14.51ms       3.21s       2.53s    500.02ms    74.50%
time for connect:    61.12ms       1.26s    672.73ms    332.30ms    63.50%
time to 1st byte:    95.03ms       3.74s       2.68s    777.51ms    74.00%
req/s           :     339.11      459.68      358.92       17.35    76.20% 
```

Enter fullscreen mode Exit fullscreen mode

### nginx

```
$ h2load -n 10000000 -c 1000 -m 1000 -t 1000 https://10.0.0.130:8083/helloworld

finished in 90.56s, 110419.16 req/s, 15.62MB/s
requests: 10000000 total, 10000000 started, 10000000 done, 10000000 succeeded, 0 failed, 0 errored, 0 timeout
status codes: 10000000 2xx, 0 3xx, 0 4xx, 0 5xx
traffic: 1.38GB (1482955210) total, 1.10GB (1182906210) headers (space savings 35.01%), 114.44MB (120000000) data
                     min         max         mean         sd        +/- sd
time for request:     3.98ms      14.65s       5.14s       3.67s    70.30%
time for connect:    69.96ms       6.74s       1.35s       1.08s    88.90%
time to 1st byte:   114.92ms      15.46s       5.67s       4.06s    63.40%
req/s           :     110.43     5143.28      378.40      590.47    92.50% 
```

Enter fullscreen mode Exit fullscreen mode

详情:[https://github.com/jiangwenyuan/nuster](https://github.com/jiangwenyuan/nuster)