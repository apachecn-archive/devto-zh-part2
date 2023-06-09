# Memcached 安装和 PHP Memcache 示例

> 原文：<https://dev.to/hakan/memcached-installation-and-php-memcache-example-4i4l>

具有高流量和高 CRUD 操作的 Web 服务器将承受很重的负载。在这种情况下，如果编写的代码能够足够有效地使用资源，那么在高负载下首先需要的就是缓存机制。

什么是 Memcahced？

Memcached 是一个低成本、开源、高性能的分布式缓存系统，为流行的博客服务 LiveJournal 编写，通常用于 Web 应用程序。这是一个简单但功能强大的缓存系统。今天，脸书被积极利用并为发展做出了贡献。流行的网络应用和服务(维基百科、Youtube、Flickr)也在积极使用。

Memcached 是一个内存存储。所有缓存都在内存上。因此，关键数据不应该只存储在 memcached 上。memcached 上的数据存储为键值对。Memcached 是一个救命恩人，当 Web 应用程序特别是具有高级数据库操作时。

**Memcached 安装**

在基于 Debian 的 Linux 发行版中；

```
apt-get install php-memcached
apt-get install memcached 
```

在基于 Fedora/RedHat 的 Linux 发行版中；

```
yum install php-memcached
yum install memcached 
```

用这个命令可以很容易地安装 memcached 和 PHP-memcached 包。Memcached 在 TCP/IP 上工作。可以在任何软件之间提供与 memcached 的通信。

正在启动 memcached 守护程序

```
memcached -d -m 512 -l 127.0.0.1 -p 11211 -u nobody 
```

该命令将启动一个 memcached 服务，该服务使用 512 MB 的缓存空间并监听 TCP 和 UDP 端口 11211。初始化 memcached 服务时创建的缓存空间是固定的。类似于操作系统架构中的内存管理，如果缓存空间已满，它将覆盖第一个写入的数据。您可以在 x86 系统上定义最大 4GB 的缓存空间。

启动 memcached 后获取有关服务的更多详细信息；

```
echo "stats settings" | nc localhost 11211 
```

默认情况下，memcached 键的长度为 250 个字符。值最大为 1MB。

**PHP Memcache 示例**

用 PHP 连接到 memcached

```
$memcache = new Memcache;
$memcache->connect('127.0.0.1', 11211) or die ("Unable to connect to Memcached"); 
```

在 memcached 上写入数据；

```
//$exampleValue = 2;
//$exampleValue = "Example String";
$exampleValue = array('0','1','2','3');

$memcache->set('exampleKey', $exampleValue, false, 1800);
//The first parameter is the key, the second parameter value, the third parameter, memcached compression is true/False, the fourth parameter is the cache time in seconds 
```

获取 memcached 上的键值；

```
$exampleValue = $memcache->get('exampleKey'); 
```

在一个示例场景中，在我们的 web 应用程序中不经常更改的值存储在数据库中。每当呈现页面或在每个 API 调用中，都需要从数据库中读取该值。让我们用 memcached 保存缓存，而不是每次都从数据库中读取这个值。

```
//Database connections etc.

$memcache     = new Memcache;
$memcache->connect('127.0.0.1', 11211) or die ("Unable to connect to Memcached");

$exampleValue = $memcache->get('exampleKey');

if($exampleValue === true){
    return $exampleValue;
}
else{
    $exampleValue = $db->get_var(sprintf("SELECT exampleValue FROM %s WHERE id = '%d'",'exampleTable',1));
    if($exampleValue){
        $memcache->set('exampleKey', $exampleValue, false, 1800);
        return $exampleValue;
    }
    else{
        return false;
    }
} 
```

这样，磁盘 I/O 操作将因数据库而减少，通过内存直接访问可以实现性能增益。Memcached 的正确实现、要在 Memcached 上缓存的数据的正确选择，以及对可能产生争用情况的进程的预先检测，可以减少 Web 服务器上可能出现的负载。