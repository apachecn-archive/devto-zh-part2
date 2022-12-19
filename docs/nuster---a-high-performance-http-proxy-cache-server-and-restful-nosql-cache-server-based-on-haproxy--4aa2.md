# 基于 HAProxy 的高性能 HTTP 代理缓存服务器和 RESTful NoSQL 缓存服务器

> 原文：<https://dev.to/nuster/nuster---a-high-performance-http-proxy-cache-server-and-restful-nosql-cache-server-based-on-haproxy--4aa2>

大家好，我已经基于 HAProxy 创建了一个高性能的 HTTP 代理缓存服务器和 RESTful NoSQL 缓存服务器: [nuster](https://github.com/jiangwenyuan/nuster) ，请尝试一下并开始:)

# nuster

[维基](https://github.com/jiangwenyuan/nuster/wiki) | [英语](//README.md) | [中文](//README-CN.md) | [日本語](//README-JP.md)

基于 HAProxy 的高性能 HTTP 代理缓存服务器和 RESTful NoSQL 缓存服务器。

# 目录

*   [简介](#introduction)
*   [性能](#performance)
*   [入门](#getting-started)
*   [用途](#usage)
*   [指令](#directives)
*   [缓存](#cache)
*   [NoSQL](#nosql)
*   [经理](#manager)
    *   [统计数据](#stats)
    *   [启用禁用规则](#enable-and-disable-rule)
    *   [更新 ttl](#update-ttl)
    *   [吹扫](#purging)
*   [商店](#store)
*   [样本获取](#sample-fetches)
*   [常见问题解答](#faq)

# 简介

nuster 是基于 HAProxy 的高性能 HTTP 代理缓存服务器和 RESTful NoSQL 缓存服务器。
它与 HAProxy 100%兼容，并充分利用 ha proxy 的 ACL 功能，根据请求、响应或服务器状态的内容提供细粒度的缓存策略。

## 特性

### 作为 HTTP/TCP 加载器的平衡器

nuster 可以像 HAProxy 一样用作 HTTP/TCP 负载均衡器。

*   HAProxy 的所有功能都是继承的，100%与 HAProxy 兼容
*   负载平衡
*   HTTPS 在前端和后端都支持
*   HTTP 压缩
*   HTTP 重写和重定向
*   HTTP 修复
*   HTTP2
*   监视
*   粘性
*   ACL 和条件
*   内容切换

### 作为 HTTP 缓存服务器

nuster 也可以像 Varnish 或者 Nginx 一样作为 HTTP 代理缓存服务器来缓存动态和静态的 HTTP 响应。

*   来自 HAProxy 的所有功能(HTTPS、HTTP/2、ACL 等)
*   极快
*   强大的动态缓存能力
    *   基于 HTTP 方法、URI、路径、查询、头、cookies 等
    *   基于 HTTP 请求或响应内容等
    *   基于环境变量、服务器状态等
    *   基于 SSL 版本、SNI 等
    *   基于连接速率、数量、字节等
*   缓存管理
*   缓存清除
*   缓存统计
*   缓存 TTL
*   磁盘持久性

### 作为 RESTful NoSQL 缓存服务器

nuster 也可以用作 RESTful NoSQL 缓存服务器，使用 HTTP `POST/GET/DELETE`来设置/获取/删除键/值对象。

它可以用作位于应用程序和数据库(如 Memcached 或 Redis)之间的内部 NoSQL 缓存，也可以用作位于最终用户和应用程序之间的面向用户的 NoSQL 缓存。它支持头文件和 cookies，所以你可以将每个用户的数据存储到同一个端点。

*   来自 HAProxy 的所有功能(HTTPS、HTTP/2、ACL 等)
*   条件缓存
*   内部 KV 缓存
*   面向用户的 RESTful 缓存
*   支持任何类型的数据
*   只要支持 HTTP，就支持所有编程语言
*   磁盘持久性

# 表现

nuster 非常快，一些测试显示 nuster 在使用单核时几乎比 nginx 快三倍，在使用所有内核时几乎比 nginx 快两倍，比 varnish 快三倍。

参见[详细基准](https://github.com/jiangwenyuan/nuster/wiki/Web-cache-server-performance-benchmark:-nuster-vs-nginx-vs-varnish-vs-squid)

# 入门

## 下载

从[下载](//Download.md)页面下载稳定版供生产使用，否则 git 克隆源代码。

## 打造

```
make TARGET=linux-glibc USE_LUA=1 LUA_INC=/usr/include/lua5.3 USE_OPENSSL=1 USE_PCRE=1 USE_ZLIB=1
make install PREFIX=/usr/local/nuster 
```

Enter fullscreen mode Exit fullscreen mode

> 使用`USE_PTHREAD_PSHARED=1`来使用 pthread 库
> 
> 如果不必要，省略`USE_LUA=1 LUA_INC=/usr/include/lua5.3 USE_OPENSSL=1 USE_PCRE=1 USE_ZLIB=1`

详见 [HAProxy 安装](https://dev.toINSTALL)。

## 创建一个配置文件

最小配置文件:`nuster.cfg`

```
global
    nuster cache on data-size 100m
    nuster nosql on data-size 200m
    master-worker # since v3
defaults
    mode http
frontend fe
    bind *:8080
    #bind *:4433 ssl crt example.com.pem alpn h2,http/1.1
    use_backend be2 if { path_beg /_kv/ }
    default_backend be1
backend be1
    nuster cache on
    nuster rule img ttl 1d if { path_beg /img/ }
    nuster rule api ttl 30s if { path /api/some/api }
    server s1 127.0.0.1:8081
    server s2 127.0.0.1:8082
backend be2
    nuster nosql on
    nuster rule r1 ttl 3600 
```

Enter fullscreen mode Exit fullscreen mode

nuster 监听端口 8080 并接受 HTTP 请求。
请求从`/_kv/`开始到后端`be2`，你可以向`/_kv/any_key`向`set/get/delete` K/V 对象发出`POST/GET/DELETE`请求。
其他请求转到后端`be1`，并将被传递到服务器`s1`或`s2`。在这些请求中，`/img/*`将被缓存 1 天，`/api/some/api`将被缓存 30 秒。

## 开始

`/usr/local/nuster/sbin/nuster -f nuster.cfg`

## 码头工人

```
docker pull nuster/nuster
docker run -d -v /path/to/nuster.cfg:/etc/nuster/nuster.cfg:ro -p 8080:8080 nuster/nuster 
```

Enter fullscreen mode Exit fullscreen mode

# 用法

nuster 基于 HAProxy，所有来自 HAProxy 的指令都在 nuster 中得到支持。

## 基本

你可以在上面的配置文件中找到四个基本的`section`:`global`、`defaults`、`frontend`和`backend`。

*   全球的
    *   定义进程范围的参数，通常是特定于操作系统的参数
    *   为了使用缓存或 nosql 功能，必须在本节中声明`nuster cache on`或`nuster nosql on`
*   默认
    *   定义所有其他`frontend`、`backend`部分的默认参数
    *   并且可以在特定的`frontend`或`backend`段被覆盖
*   前端
    *   描述一组接受客户端连接的监听套接字
*   后端
    *   描述代理将连接以转发传入连接的一组服务器
    *   必须在此部分声明`nuster cache on`或`nuster nosql on`
    *   `nuster rule`必须在这里声明

您可以定义多个`frontend`或`backend`段。如果声明了`nuster cache|nosql off`或者没有声明`nuster cache|nosql on|off`，nuster 就像 HAProxy 一样，充当 TCP 和 HTTP 负载平衡器。

虽然`listen`是一个完整的代理，它的前端和后端部分组合在一个部分中，但是你不能在`listen`中使用 nuster，使用`frontend`和`backend`对。

您可以在`/doc`和[在线 HAProxy 文档](https://cbonte.github.io/haproxy-dconv/)中找到 HAProxy 文档

## 作为 TCP 加载器的平衡器

```
frontend mysql-lb
    bind *:3306
    mode tcp
    default_backend mysql-cluster
backend mysql-cluster
    balance roundrobin
    mode tcp
    server s1 10.0.0.101:3306
    server s2 10.0.0.102:3306
    server s3 10.0.0.103:3306 
```

Enter fullscreen mode Exit fullscreen mode

## 作为 HTTP/HTTPS 加载器的平衡器

```
frontend web-lb
    bind *:80
    #bind *:443 ssl crt XXX.pem
    mode http
    default_backend apps
backend apps
    balance roundrobin
    mode http
    server s1 10.0.0.101:8080
    server s2 10.0.0.102:8080
    server s3 10.0.0.103:8080
    #server s4 10.0.0.101:8443 ssl verify none 
```

Enter fullscreen mode Exit fullscreen mode

## 作为 HTTP 缓存服务器

```
global
    nuster cache on data-size 200m
frontend fe
    bind *:8080
    mode http
    default_backend be
backend be
    mode http
    nuster cache on
    nuster rule all
    server s1 127.0.0.1:8081 
```

Enter fullscreen mode Exit fullscreen mode

## 作为 RESTful NoSQL 缓存服务器

```
global
    nuster nosql on data-size 200m
frontend fe
    bind *:8080
    mode http
    default_backend be
backend be
    nuster nosql on
    mode http
    nuster rule r1 ttl 3600 
```

Enter fullscreen mode Exit fullscreen mode

# 指令

## 全局:管理员

**语法:**

*nuster manager on | off[uri URI][purge-method method]*

**默认:** *关*

**上下文:** *全局*

启用管理器/统计/清除 API，定义端点和清除方法。

默认情况下，它是禁用的。启用时，记得限制访问(参见[常见问题解答](#how-to-restrict-access))。

详见[经理](#manager)。

### uri

定义端点 URI，默认为`/nuster`。

### 吹扫-方法

定义一个自定义的 HTTP 方法进行清除，默认为`PURGE`。

## 全局:n 主缓存|nosql

**语法:**

*nuster cache on | off[data-size size][dict-size size][DIR][dict-cleaner n][data-cleaner n][disk-cleaner n][disk-loader n][disk-saver n][clean-temp on | off]*

*nuster no SQL on | off[data-size size][dict-size size][DIR][dict-cleaner n][data-cleaner n][disk-cleaner n][disk-loader n][disk-saver n][clean-temp on | off]*

**默认:** *无*

**上下文:** *全局*

确定是否使用 cache/nosql。

将创建一个大小为`data-size + dict-size`的存储区。

除了在请求中创建和销毁的临时数据之外，所有与缓存相关的数据(包括 HTTP 响应数据、密钥和开销)都存储在这个内存区域中，并在所有进程之间共享。
如果无法从该内存区域分配更多内存，则根据定义的规则应该缓存的新请求将不会被缓存，除非释放一些内存。
临时数据存储在内存池中，当池中没有可用内存时，系统会动态分配内存。
全局内部计数器监控所有进程中所有 HTTP 响应数据的内存使用情况，如果计数器超过`data-size`，新请求将不会被缓存。

### 数据-大小

与`dict-size`一起决定存储区的大小。

接受`m`、`M`、`g`、`G`等单位。默认情况下，大小为 1024 * 1024 字节，这也是最小大小。

### 字典-尺寸

确定哈希表使用的内存大小。

接受`m`、`M`、`g`、`G`等单位。默认情况下，大小为 1024 * 1024 字节，这也是最小大小。

注意，它只决定哈希表桶使用的内存，而不是键。事实上，密钥存储在由`data-size`限定的存储区中。

**dict-size(桶数)**与**键数**不同。只要有足够的内存，即使关键字的数量超过 dict-size(桶的数量),新的关键字仍然可以添加到哈希表中。

然而，如果`number of keys`大于`dict-size(number of buckets)`，可能会导致潜在的性能下降。大约的键数乘以 8(正常情况下)作为`dict-size`应该没问题。基本上是越大越好。

启用统计 API 并检查以下统计:

```
dict.nosql.length:              131072
dict.nosql.used:                0 
```

Enter fullscreen mode Exit fullscreen mode

如果`dict.nosql.used`大于`dict.nosql.length`，那么增加`dict-size`将是一个好主意。

> dict-size 将在未来的版本中被删除，在第一个版本中自动调整哈希表的大小将被添加回来。

### dir

指定磁盘持久性的根目录。为了使用磁盘持久性，必须对其进行设置。

### 字典清理器

在 v2.x 之前，删除无效缓存数据、重置 dict 条目等管理器任务是在每个 HTTP 请求中迭代执行的。相应的指示器或指针在每次迭代中增加或前进。

在 v3.x 中，这些任务被移到主流程中，也在迭代中完成，可以设置这些参数来控制某个任务在一次迭代中的次数。

在一次迭代中，检查的条目不超过`dict-cleaner`，无效条目将被删除(默认为 1000)。

### 数据清理器

在一次迭代中，检查的数据不超过`data-cleaner`，无效数据将被删除(默认为 1000)。

当无效数据比率大于 20%时，内部机制会加快清理过程，因此建议不要更改默认值。

### 磁盘清洁器

如果启用了磁盘持久性，数据将存储在文件中。这些文件由主进程检查，如果无效，例如过期，将被删除。

在一次迭代中，检查不超过`disk-cleaner`个文件，无效文件将被删除(默认为 100)。

### 装盘机

nuster 启动后，主进程会将之前存储在磁盘上的数据信息加载到内存中。

在一次迭代中，加载的文件不超过`disk-loader`(默认为 100)。

如果使用`USE_THREAD`，将创建一个单独的线程来加载磁盘文件，该参数被忽略。

### 磁盘保护程序

主进程将定期保存`disk sync`缓存数据。

在一次迭代中，不超过`disk-saver`的数据被检查并保存到磁盘(默认为 100)。

详见[店](#disk)。

### 清洁-温度开|关

在`dir`定义的目录下，会创建一个临时目录`.tmp`来存放临时文件。

使用此选项来确定是否在启动时删除这些临时文件。

默认为`off`。

## 代理:主机缓存|nosql

**语法:**

*主机缓存[开|关]*

*nuster nosql【开|关】*

**默认:** *开启*

**语境:** *后端*

确定是否在此代理上使用 cache/nosql，应定义额外的`nuster rule`。
如果此代理上有过滤器，请将此指令放在所有其他过滤器之后。

## 代理:nuster 规则

**语法:**

*nuster 规则名称[KEY KEY][TTL auto | TTL][EXTEND EXTEND][wait on | off | TIME][use-stale on | off | TIME][inactive off | TIME][CODE CODE][memory on | off][disk on | off | sync][etag on | off][last-modified on | off][if | unless condition]*

**默认:** *无*

**语境:** *后端*

定义规则以指定高速缓存/nosql 创建条件和属性。至少应该定义一个规则。

```
nuster cache on

# cache request `/asdf` for 30 seconds
nuster rule asdf ttl 30 if { path /asdf }

# cache if the request path begins with /img/
nuster rule img if { path_beg /img/ }

# cache if the response header `cache` is `yes`
acl resHdrCache res.hdr(cache) yes
nuster rule r1 if resHdrCache 
```

Enter fullscreen mode Exit fullscreen mode

在同一个部分中声明多个规则是可能的，也是推荐的。顺序很重要，因为匹配过程会在第一次匹配时停止。

```
acl pathA path /a.html
nuster cache on
nuster rule all ttl 3600
nuster rule path01 ttl 60 if pathA 
```

Enter fullscreen mode Exit fullscreen mode

规则`path01`永远不会匹配，因为第一个规则将缓存所有内容。

### 名称

为此规则定义一个名称。从 v5 开始必须是全局唯一的。

### [键](#key-key)键

为 cache/nosql 定义键，它采用一个字符串，该字符串由以下带有`.`分隔符的关键字组合而成:

*   方法:http 方法，GET/POST...
*   方案:http 或 https
*   主机:请求中的主机
*   uri:URL 末尾的第一个斜杠
*   路径:请求的 URL 路径
*   分隔符:“？”如果查询存在，否则为空
*   查询:请求的整个查询字符串
*   header_NAME:表头`NAME`的值
*   cookie _ NAME:cookie 的值`NAME`
*   param_NAME:查询的值`NAME`
*   正文:请求的正文

缓存默认关键字为`method.scheme.host.uri`，NoSQL 默认关键字为`GET.scheme.host.uri`。

示例

```
GET http://www.example.com/q?name=X&type=Y

http header:
GET /q?name=X&type=Y HTTP/1.1
Host: www.example.com
ASDF: Z
Cookie: logged_in=yes; user=nuster; 
```

Enter fullscreen mode Exit fullscreen mode

结果应该是:

*   方法:获取
*   方案:http
*   主持人:[www.example.com](http://www.example.com)
*   uri: /q？name=X&type=Y
*   路径:/q
*   分隔符:？
*   查询:name=X&type=Y
*   标题 _ASDF: Z
*   cookie_user: nuster
*   param_type: Y
*   正文:(空)

所以默认键产生`GET\0http\0www.example.com\0/q?name=X&type=Y\0`，而`key method.scheme.host.path.header_ASDF.cookie_user.param_type`产生`GET\0http\0www.example.com\0/q\0Z\0nuster\0Y\0`。

> `\0`为空字符

如果请求与缓存的 HTTP 响应数据具有相同的键，那么缓存的数据将被发送到客户端。

### ttl 自动|TTL

在密钥上设置一个 TTL，TTL 过期后，密钥将被删除。

接受`d`、`h`、`m`、`s`等单位。默认的 ttl 是`0`，它不会使密钥过期。

当使用`auto`时，ttl 被设置为`cache-control`标题中的`s-maxage`或`max-age`指令的值。

> 不处理`cache-control`的其他指令。

ttl 的最大值是 2147483647。

使用`extend`关键字可以自动扩展 ttl。

### 延伸延伸

自动延长 ttl。

#### 格式

扩展开|关|n1、n2、n3、n4

默认:关闭。

n1、n2、n3、n4:小于 100 的正整数，n1 + n2 + n3 小于 100。它们一起定义了如下四个时隙:

```
time:       0                                                       ttl         ttl * (1 + n4%)
access:     |            A1             |   A2    |   A3    |   A4    |         |
            |---------------------------|---------|---------|---------|---------|
percentage: |<- (100 - n1 - n2 - n3)% ->|<- n1% ->|<- n2% ->|<- n3% ->|<- n4% ->| 
```

Enter fullscreen mode Exit fullscreen mode

如果出现以下情况，ttl 将被扩展:

1.  A4 > A3 > A2
2.  在`ttl`和`ttl * (1 + n4%)`之间发生新的请求

> `on`等于 33，33，33，33

### 等待开|关|时间【仅缓存】

启用时，一次只有一个请求会被传递到后端服务器以创建缓存。其他相同的请求将等待直到创建缓存(`wait on`)或者等待时间到期(`wait TIME`)并被转发到后端服务器。

默认情况下，相同的请求被转发到后端服务器，第一个请求将创建缓存(`wait off`)。

请注意，其他相同的请求不会等到第一个请求完成初始化过程(例如，创建缓存条目)。

> 在 nosql 模式下，没有等待模式。多个相同的 POST 请求按照接收的顺序提供服务，最后一个请求的正文将保存为内容。

等待的最大值是 2147483647。

### use-stale on | off | TIME[仅缓存]

确定在更新旧缓存或后端服务器关闭时是否向客户端提供旧缓存。

当 use-stale 为 on 时，陈旧缓存将用于为客户端提供服务。

当 use-stale 为 off 时，这是默认模式，如果设置了`wait off`，则在更新缓存时，相同的请求将被传递到后端，否则，如果设置了`wait on|TIME`，则等待。

`use-stale TIME`如果缓存由于后端错误而无法更新，则允许使用过时的缓存为客户端提供秒钟的服务。

使用状态的最大值是 2147483647。

### 非活动关闭|时间

确定是否删除时间秒内未被访问的缓存，而不考虑有效性。默认情况下，不活动设置为关闭(0)。

请注意，不保证缓存在非活动时间后会被删除。如果清理进程首先访问缓存，则数据会被删除。如果一个新的请求先出现，那么缓存的最后访问时间被更新，并且缓存不会被删除。对于磁盘文件，不使用文件的 atime，所以当 nuster 重新启动时，最后一次访问时间被设置为加载时间。

不活动的最大值是 2147483647。

### 代码 CODE1，代码 CODE2...

仅当响应状态代码为代码时缓存。

默认情况下，只缓存 200 个响应。您可以使用`all`来缓存所有响应。

```
nuster rule only200
nuster rule 200and404 code 200,404
nuster rule all code all 
```

Enter fullscreen mode Exit fullscreen mode

### 记忆开|关

是否将数据保存到内存，默认打开。

详见[店](#Store)。

### 磁盘开|关|同步

是否将数据保存到磁盘，以及如何保存，默认关闭。

为了使用`disk sync`模式，需要设置`memory on`。

详见[店](#Store)。

### etag 开|关

启用 etag 条件请求处理。如果没有，添加`ETag`标题。

默认关闭。

### 最后修改开|关

启用上次修改的条件请求处理。如果没有，添加`Last-Modified`标题。

默认关闭。

### 如果|除非条件

使用 HAProxy ACL 定义何时缓存。

评估包括两个阶段:请求阶段和响应阶段。

如果出现以下情况，将执行缓存:

1.  请求阶段的评估为真，
2.  请求阶段的评估为假，但在响应阶段为真。

**如果在某个阶段**条件或样本不可用的情况下使用否定，请务必小心

举个例子，

1.  如果请求路径以`/img/`开头，则缓存

    nuster 规则 img if { path_beg /img/ }

这是可行的，因为请求阶段的评估要么为真，要么为假，在响应阶段永远不会为真，因为`path`在响应阶段不可用。

1.  如果响应中的`Content-Type`为`image/jpeg`则缓存

    nuster 规则 jpeg if { res.hdr(内容类型)图像/jpeg }

这将起作用，因为请求阶段的评估总是假的，因为`res.hdr`在请求阶段不可用，并且在响应阶段将为真或假。

1.  如果请求路径以`/img/`开始，并且响应中的`Content-Type`为`image/jpeg`，则缓存

如果您将规则定义为:

```
nuster rule img if { path_beg /img/ } { res.hdr(Content-Type) image/jpeg } 
```

Enter fullscreen mode Exit fullscreen mode

因为`path`在响应阶段不可用，`res.hdr`在请求阶段不可用，所以求值永远不会为真。

为了实现这一点，需要在响应阶段分配`path`以供进一步使用:

```
http-request set-var(txn.pathImg) path
acl pathImg var(txn.pathImg) -m beg /img/
acl resHdrCT res.hdr(Content-Type) image/jpeg
nuster rule r3 if pathImg resHdrCT 
```

Enter fullscreen mode Exit fullscreen mode

或者使用`nuster.path` (v5):

```
nuster rule r3 if { nuster.path -m beg /img } { res.hdr(Content-Type) image/jpeg } 
```

Enter fullscreen mode Exit fullscreen mode

1.  另一个例子，如果请求路径不是以`/api/`开始，则缓存

也不行:

```
acl NoCache path_beg /api/
nuster rule r3 if !NoCache 
```

Enter fullscreen mode Exit fullscreen mode

因为在请求阶段`NoCache`对`/api/`的评价为真，否定为假，这是期望的状态，但是在响应阶段，由于`path`在响应阶段不可用，`NoCache`的评价始终为假，会被缓存为否定`!NoCache`为真。

这将起作用:

```
http-request set-var(txn.path) path
acl NoCache var(txn.path) -m beg /api/
nuster rule r1 if !NoCache 
```

Enter fullscreen mode Exit fullscreen mode

关于 nuster 引入的样本获取，请参见[样本获取](#sample-fetch)。

参见 **7。使用 ACL 并获取样本 [HAProxy 配置](//doc/configuration.txt)中的**部分

# 缓存

nuster 可以像 Varnish 或者 Nginx 一样作为 HTTP 代理缓存服务器来缓存动态和静态的 HTTP 响应。

您可以使用 HAProxy 功能来终止 SSL、规范 HTTP、支持 HTTP2、重写 URL 或修改头等等，以及 nuster 提供的控制缓存的附加功能。

```
global
    nuster cache on data-size 200m
frontend fe
    bind *:8080
    mode http
    default_backend be
backend be
    mode http
    nuster cache on
    nuster rule r1 if { path /a1 }
    nuster rule r2 key method.scheme.host.path.delimiter.query.cookie_userId if { path /a2 }
    nuster rule r3 ttl 10 if { path /a3 }
    nuster rule r4 disk only if { path /a4 }

    server s1 127.0.0.1:8081 
```

Enter fullscreen mode Exit fullscreen mode

当一个请求被接受时，nuster 会逐个检查规则。键将被创建并用于在缓存中查找，如果命中，缓存的数据将被返回给客户端。否则将测试 ACL，如果通过测试，将缓存响应。

# NoSQL

nuster 可以用作 RESTful NoSQL 缓存服务器，使用 HTTP `POST/GET/DELETE`来设置/获取/删除键/值对象。

## 基本操作

### 设定

```
curl -v -X POST -d value1 http://127.0.0.1:8080/key1
curl -v -X POST --data-binary @icon.jpg http://127.0.0.1:8080/imgs/icon.jpg 
```

Enter fullscreen mode Exit fullscreen mode

### 得到

`curl -v http://127.0.0.1:8080/key1`

### 删除

`curl -v -X DELETE http://127.0.0.1:8080/key1`

## 响应

检查状态代码。

*   200 好吧
    *   发布/获取:成功
    *   删除:总是
*   400 错误请求
    *   空值
    *   不正确的 acl、规则等
*   404 未找到
    *   POST:所有规则测试都失败
    *   获取:未找到
*   不允许 405 方法
    *   其他方法
*   500 内部服务器错误
    *   出现任何错误
*   507 存储不足
    *   超过最大数据大小

## 标题

请求中支持的标头

| 名字 | 价值 | 描述 |
| --- | --- | --- |
| 内容类型 | 任何的 | 将在 GET 请求中按原样返回 |
| 缓存控制 | `s-maxage`或`max-age` | 用于在 rule.ttl 为`auto`时设置 ttl |

## 每用户数据

通过在 key 中使用 header 或 cookie，可以将每个用户的数据保存到同一个端点。

```
nuster rule r1 key method.scheme.host.uri.header_userId if { path /mypoint }
nuster rule r2 key method.scheme.host.uri.cookie_sessionId if { path /mydata } 
```

Enter fullscreen mode Exit fullscreen mode

### 设定

```
curl -v -X POST -d "333" -H "userId: 1000" http://127.0.0.1:8080/mypoint
curl -v -X POST -d "555" -H "userId: 1001" http://127.0.0.1:8080/mypoint

curl -v -X POST -d "userA data" --cookie "sessionId=ijsf023xe" http://127.0.0.1:8080/mydata
curl -v -X POST -d "userB data" --cookie "sessionId=rosre329x" http://127.0.0.1:8080/mydata 
```

Enter fullscreen mode Exit fullscreen mode

### 得到

```
curl -v http://127.0.0.1:8080/mypoint
< 404 Not Found

curl -v -H "userId: 1000" http://127.0.0.1:8080/mypoint
< 200 OK
333

curl -v --cookie "sessionId=ijsf023xe" http://127.0.0.1:8080/mydata
< 200 OK
userA data 
```

Enter fullscreen mode Exit fullscreen mode

## 客户端

你可以使用任何支持 HTTP: `curl`、`postman`、python `requests`、go `net/http`等的工具或库。

# 经理

Nuster 可以通过一个管理器 API 来管理，该 API 的端点由`uri`定义，并且可以通过发出 HTTP 请求和一些头来访问

**启用并定义端点 uri 和清除方法**

```
nuster manager on uri /internal/nuster purge-method PURGEX 
```

Enter fullscreen mode Exit fullscreen mode

## 用法矩阵

| 方法 | 端点 | 描述 |
| --- | --- | --- |
| 得到 | /内部/内部 | 获取统计数据 |
| 邮政 | /内部/内部 | 启用和禁用规则，更新 ttl |
| 删除 | /内部/内部 | 高级清除缓存 |
| PURGEX | /任意/真实/路径 | 基本清洗 |

## 统计数据

可以通过向`uri`定义的端点发出 HTTP GET 请求来访问 Nuster stats

### 用法

`curl http://127.0.0.1/nuster`

### 输出

```
**NUSTER**
nuster.cache:                   on
nuster.nosql:                   on
nuster.manager:                 on

**MANAGER**
manager.uri:                    /nuster
manager.purge_method:           PURGE

**DICT**
# The size of the memory used by the cache dict in bytes defined by dict-size
dict.cache.size:                1048576
# The length of the cache dict array
dict.cache.length:              131072
# The number of used entries in the cache dict
dict.cache.used:                0
dict.cache.cleanup_idx:         0
dict.cache.sync_idx:            0
dict.nosql.size:                1048576
dict.nosql.length:              131072
dict.nosql.used:                0
dict.nosql.cleanup_idx:         0
dict.nosql.sync_idx:            0

**STORE MEMORY**
# The size of the cache memory store in bytes, approximate equals to dict-size + data-size
store.memory.cache.size:        2098200576
# The size of used memory of the cache memory store
store.memory.cache.used:        1048960
# The number of stored cache entries
store.memory.cache.count:       0
store.memory.nosql.size:        11534336
store.memory.nosql.used:        1048960
store.memory.nosql.count:       0

**STORE DISK**
store.disk.cache.dir:           /tmp/nuster/cache
store.disk.cache.loaded:        yes
store.disk.nosql.dir:           /tmp/nuster/nosql
store.disk.nosql.loaded:        yes

**STATS**
# The total number of requests
stats.cache.total:              0
# The total number of HIT requests
stats.cache.hit:                0
# The total number of MISS requests
stats.cache.fetch:              0
# The total number of bypass requests
stats.cache.bypass:             0
# The total number of aborted requests
stats.cache.abort:              0
# The total response size in bytes served by cache
stats.cache.bytes:              0
stats.nosql.total:              0
stats.nosql.get:                0
stats.nosql.post:               0
stats.nosql.delete:             0

**PROXY cache app1**
app1.rule.rule1:                state=on  memory=on  disk=off   ttl=10
app1.rule.rule2:                state=on  memory=on  disk=on    ttl=10
app1.rule.rule3:                state=on  memory=on  disk=sync  ttl=10
app1.rule.rule4:                state=on  memory=off disk=on    ttl=10
app1.rule.rule5:                state=on  memory=off disk=off   ttl=10

**PROXY nosql app2**
app2.rule.ruleA:                state=on  memory=on  disk=off   ttl=10
app2.rule.ruleB:                state=on  memory=on  disk=on    ttl=10
app2.rule.ruleC:                state=on  memory=on  disk=sync  ttl=10
app2.rule.ruleD:                state=on  memory=off disk=on    ttl=10
app2.rule.ruleE:                state=on  memory=off disk=off   ttl=10 
```

Enter fullscreen mode Exit fullscreen mode

## 启用和禁用规则

可以在运行时通过管理器 uri 禁用规则。不会处理禁用的规则，也不会处理由该规则创建的缓存。

***标题***

| 页眉 | 价值 | 描述 |
| --- | --- | --- |
| 状态 | 使能够 | 启用规则 |
|  | 使残废 | 禁用规则 |
| 名字 | 规则名称 | 要启用/禁用的规则 |
|  | 代理名称 | 代理名称的所有规则 |
|  | * | 所有规则 |

请记住，如果名称不唯一，**所有具有该名称的**规则都将被禁用/启用。

***例句***

*   禁用规则 r1

`curl -X POST -H "name: r1" -H "state: disable" http://127.0.0.1/nuster`

*   禁用代理 app1b 中定义的所有规则

`curl -X POST -H "name: app1b" -H "state: disable" http://127.0.0.1/nuster`

*   启用所有规则

`curl -X POST -H "name: *" -H "state: enable" http://127.0.0.1/nuster`

## 更新 ttl

改变 TTL。它只影响要缓存的响应的 TTL，**不会**更新现有缓存的 TTL。

***标题***

| 页眉 | 价值 | 描述 |
| --- | --- | --- |
| 电平信号 | 新 TTL | 参见`nuster rule`中的`ttl` |
| 名字 | 规则名称 | 要改变的规则 |
|  | 代理名称 | 代理名称的所有规则 |
|  | * | 所有规则 |

***例句***

```
curl -X POST -H "name: r1" -H "ttl: 0" http://127.0.0.1/nuster
curl -X POST -H "name: r2" -H "ttl: 2h" http://127.0.0.1/nuster 
```

Enter fullscreen mode Exit fullscreen mode

### 更新状态和 TTL

状态和 ttl 可以同时更新

```
curl -X POST -H "name: r1" -H "ttl: 0" -H "state: enabled" http://127.0.0.1/nuster 
```

Enter fullscreen mode Exit fullscreen mode

## 吹扫

有两种清除模式:基本和高级。

*   基本:发送由`purge-method MYPURGE`定义的 HTTP 方法到你想要删除的路径
*   高级:将删除方法发送到由`uri`定义的管理器 uri

### 基本吹扫

该方法删除被请求的特定 url，如下所示:

`curl -XPURGE http://127.0.0.1/imgs/test.jpg`

除了`method`是`GET`之外，创建缓存时以相同的方式创建键。

注意:默认情况下，缓存键包含`Host`如果您缓存类似`http://example.com/test`的请求，并从本地主机清除，您需要指定`Host`头:

`curl -XPURGE -H "Host: example.com" http://127.0.0.1/test`

它对 cache 和 nosql 都有效，它是 nosql 模式下`DELETE`的别名。

### 高级清除:按名称清除

可以通过向管理器 uri 发出 HTTP `DELETE`请求以及一个`name`头来清除缓存。

***标题***

| 页眉 | 价值 | 描述 |
| --- | --- | --- |
| 名字 | nuster 规则名称 | 将清除由规则${NAME}创建的缓存 |
|  | 代理名称 | 代理${NAME}的缓存 |

***例句***

```
# purge all caches of proxy applb
curl -X DELETE -H "name: app1b" http://127.0.0.1/nuster
# purge all caches of rule r1
curl -X DELETE -H "name: r1" http://127.0.0.1/nuster 
```

Enter fullscreen mode Exit fullscreen mode

### 高级清除:由主机清除

您也可以按主机清除缓存，该主机的所有缓存都将被删除:

***标题***

| 页眉 | 价值 | 描述 |
| --- | --- | --- |
| 宿主 | 宿主 | ＄{ HOST } |
| nuster-主机 | 宿主 | nuster-host 的优先级高于 host |
| 方式 | 缓存，nosql | 清除缓存或 nosql 数据 |

***例句***

```
curl -X DELETE -H "nuster-host: 127.0.0.1:8080" http://127.0.0.1/nuster 
```

Enter fullscreen mode Exit fullscreen mode

### 高级清除:按路径清除

默认情况下，查询部分也被用作缓存键，因此如果查询不同，将会有多个缓存。

例如，对于规则`nuster rule imgs if { path_beg /imgs/ }`和请求

```
curl http://127.0.0.1/imgs/test.jpg?w=120&h=120
curl http://127.0.0.1/imgs/test.jpg?w=180&h=180 
```

Enter fullscreen mode Exit fullscreen mode

因为缺省键包含查询部分，所以将有两个缓存对象。

为了删除它，您可以

***一一删除以防你知道所有查询***

```
curl -XPURGE http://127.0.0.1/imgs/test.jpg?w=120&h=120
curl -XPURGE http://127.0.0.1/imgs/test.jpg?w=180&h=180 
```

Enter fullscreen mode Exit fullscreen mode

如果你不知道所有的查询，它就不起作用。

***使用自定义密钥，一旦查询部分不相关就删除***

定义一个类似`nuster rule imgs key method.scheme.host.path if { path_beg /imgs }`的键，这样只创建一个缓存，可以不查询就清除:

`curl -XPURGE http://127.0.0.1/imgs/test.jpg`

如果查询部分是必需的，则它不起作用。

***删除被规则名称***

`curl -X DELETE -H "name: imgs" http://127.0.0.1/nuster`

如果 nuster 规则被定义为类似于`nuster rule static if { path_beg /imgs/ /css/ }`的东西，它就不起作用。

此方法提供了一种仅按路径清除的方式:

***标题***

| 页眉 | 价值 | 描述 |
| --- | --- | --- |
| 小路 | 小路 | 将清除包含${PATH}的缓存 |
| 宿主 | 宿主 | 主机是${HOST} |
| nuster-主机 | 宿主 | nuster-host 的优先级高于 host |
| 方式 | 缓存，nosql | 清除缓存或 nosql 数据 |

***例句***

```
#delete all caches which path is /imgs/test.jpg
curl -X DELETE -H "path: /imgs/test.jpg" http://127.0.0.1/nuster
#delete all caches which path is /imgs/test.jpg and with host of 127.0.0.1:8080
curl -X DELETE -H "path: /imgs/test.jpg" -H "nuster-host: 127.0.0.1:8080" http://127.0.0.1/nuster 
```

Enter fullscreen mode Exit fullscreen mode

### 高级清除:按正则表达式清除

您也可以按正则表达式清除缓存，路径与正则表达式匹配的缓存将被删除。

***标题***

| 页眉 | 价值 | 描述 |
| --- | --- | --- |
| 正则表达式 | 正则表达式 | 路径与＄{ REGEX }匹配的缓存将被清除 |
| 宿主 | 宿主 | 主机是${HOST} |
| nuster-主机 | 宿主 | nuster-host 的优先级高于 host |
| 方式 | 缓存，nosql | 清除缓存或 nosql 数据 |

***例句***

```
#delete all caches which path starts with /imgs and ends with .jpg
curl -X DELETE -H "regex: ^/imgs/.*\.jpg$" http://127.0.0.1/nuster
#delete all caches which path starts with /imgs and ends with .jpg and with host of 127.0.0.1:8080
curl -X DELETE -H "regex: ^/imgs/.*\.jpg$" -H "127.0.0.1:8080" http://127.0.0.1/nuster 
```

Enter fullscreen mode Exit fullscreen mode

**清洗注意事项**

1.  **启用访问限制**

2.  如果有混合标题，使用`name`、`path & host`、`path`、`regex & host`、`regex`、`host`的优先顺序

`curl -X DELETE -H "name: rule1" -H "path: /imgs/a.jpg"`:按名称清除

1.  如果有多余的标头，使用第一个出现的标头

`curl -X DELETE -H "name: rule1" -H "name: rule2"`:由`rule1`进行吹扫

1.  `regex`难道**不是环球**

比如/imgs 下的所有 jpg 文件都应该是`^/imgs/.*\.jpg$`而不是`/imgs/*.jpg`

1.  按代理名称或规则名称或主机或路径或正则表达式清除缓存文件仅在磁盘加载程序进程完成后有效。您可以通过 stats url 查看状态。

# 商店

Nuster(包括 cache 和 nosql)支持不同的后端存储。目前支持内存和磁盘。将增加更多商店。

## 记忆

数据存储在由`data-size`定义大小的存储区中。数据不会保存在内存中，重启后会丢失。

## 磁盘

数据存储在磁盘上，位于`dir`定义的路径下。数据在重新启动后仍然存在。

有 3 种模式:

*   off:默认情况下，禁用磁盘持久性。
*   开:将数据保存到磁盘。
*   要使用此模式，必须设置 sync: `memory on`。首先将数据保存到内存中，然后数据将由主进程同步到磁盘中。一次迭代`disk-saver`数据被检查并保存到磁盘。

# 样本获取

Nuster 引入了以下样本获取

## 【缓存】nuster.cache.hit:布尔值

返回一个 boolean 值，表示是否成功，可以像这样使用

```
http-response set-header x-cache hit if { nuster.cache.hit } 
```

Enter fullscreen mode Exit fullscreen mode

## 【cache | no SQL】nuster . host:string

与 HAProxy `req.hdr(Host)`相同，除了`nuster.host`可用于请求和响应阶段。

## 【cache | no SQL】nuster . uri:string

同 HAProxy `capture.req.uri`。

## 【cache | no SQL】nuster . path:string

与 HAProxy `path`相同，除了`nuster.path`可用于请求和响应阶段。

## 【cache | no SQL】nuster . query:string

与 HAProxy `query`相同，除了`nuster.query`可用于请求和响应阶段。

# 常见问题

## 无法启动:不在主从模式下

在`global`段设置`master-worker`，或者用`-W`启动`nuster`。

## 如何调试？

在`global`段设置`debug`，或者用`-d`启动`nuster`。

与 Nuster 相关的调试消息以`[nuster`开始。

## 如何缓存 POST 请求？

启用缓存规则`key`中的`option http-buffer-request`并设置`body`。

默认情况下，缓存键不包括请求体，记住将`body`放在键字段中。

注意，请求体的大小必须小于`tune.bufsize - tune.maxrewrite - request_header_size`，默认情况下是`16384 - 1024 - request_header_size`。

详见 [HAProxy 配置](//doc/configuration.txt)中**选项 http-buffer-request** 和 **tune.bufsize** 章节。

此外，将它单独放在一个专用的后端也是一个好主意，就像这个例子一样。

## 如何限制访问？

你可以使用强大的 HAProxy ACL，类似这样的东西

```
acl network_allowed src 127.0.0.1
acl purge_method method PURGE
http-request deny if purge_method !network_allowed 
```

Enter fullscreen mode Exit fullscreen mode

## 如何启用 HTTP2

```
bind :443 ssl crt pub.pem alpn h2,http/1.1 
```

Enter fullscreen mode Exit fullscreen mode

# 举例

```
global
    nuster manager on uri /_/nuster purge-method MYPURGE
    nuster cache on data-size 100m
    nuster nosql on data-size 100m
    master-worker # since v3
    # daemon
    # debug
defaults
    retries 3
    option redispatch
    timeout client  30s
    timeout connect 30s
    timeout server  30s
frontend web1
    bind *:8080
    mode http
    acl pathPost path /search
    use_backend app1a if pathPost
    default_backend app1b
backend app1a
    balance roundrobin
    # mode must be http
    mode http

    # http-buffer-request must be enabled to cache post request
    option http-buffer-request

    acl pathPost path /search

    # enable cache for this proxy
    nuster cache

    # cache /search for 120 seconds. Only works when POST/PUT
    nuster rule rpost key method.scheme.host.uri.body ttl 120 if pathPost

    server s1 10.0.0.10:8080
backend app1b
    balance     roundrobin
    mode http

    nuster cache on

    # cache /a.jpg, not expire
    acl pathA path /a.jpg
    nuster rule r1 ttl 0 if pathA

    # cache /mypage, key contains cookie[userId], so it will be cached per user
    acl pathB path /mypage
    nuster rule r2 key method.scheme.host.path.delimiter.query.cookie_userId ttl 60 if pathB

    # cache /a.html if response's header[cache] is yes
    http-request set-var(txn.pathC) path
    acl pathC var(txn.pathC) -m str /a.html
    acl resHdrCache1 res.hdr(cache) yes
    nuster rule r3 if pathC resHdrCache1

    # cache /heavy for 100 seconds if be_conn greater than 10
    acl heavypage path /heavy
    acl tooFast be_conn ge 100
    nuster rule heavy ttl 100 if heavypage tooFast

    # cache all if response's header[asdf] is fdsa
    acl resHdrCache2 res.hdr(asdf)  fdsa
    nuster rule resCache ttl 0 if resHdrCache1

    server s1 10.0.0.10:8080

frontend web2
    bind *:8081
    mode http
    default_backend app2
backend app2
    balance     roundrobin
    mode http

    # disable cache on this proxy
    nuster cache off
    nuster rule all

    server s2 10.0.0.11:8080

frontend nosql_fe
    bind *:9090
    default_backend nosql_be
backend nosql_be
    nuster nosql on
    nuster rule r1 ttl 3600 
```

Enter fullscreen mode Exit fullscreen mode

# 投稿

*   加入发展
*   给出反馈
*   报告问题
*   发送拉取请求
*   传播 nuster

# 执照

版权所有(C) 2017 年至今，[姜](https://github.com/jiangwenyuan)，< koubunen 在 Gmail . com>

保留所有权利。

根据 GPL 许可，与 HAProxy 相同

HAProxy 和其他来源的许可声明:参见相关的个人文件。