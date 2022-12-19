# 制作您自己的 GeoIP API

> 原文：<https://dev.to/enzzc/make-your-own-geoip-api-4k11>

## 简介

本文向您展示了如何维护您自己的 GeoIP 数据库并围绕它实现一个 API。

## 数据

这些数据是公开和免费的。它由五个地区互联网注册管理机构(rir)提供。

每个 RIR 定期更新一个包含我们需要的信息的大文件。这些文件符合 [RIR 统计数据交换格式](https://www.apnic.net/about-apnic/corporate-documents/documents/resource-guidelines/rir-statistics-exchange-format/)，该格式可以解析(稍作调整)为一个 CSV 格式，使用管道(`|`)作为分隔符。

对于每个 RIR，这里是文件:(小心，他们有点重)

*   [ARIN](https://ftp.arin.net/pub/stats/arin/delegated-arin-extended-latest)
*   [成熟的 NCC](https://ftp.ripe.net/ripe/stats/delegated-ripencc-extended-latest)
*   [APNIC](https://ftp.apnic.net/stats/apnic/delegated-apnic-extended-latest)
*   [非洲语](https://ftp.apnic.net/stats/afrinic/delegated-afrinic-extended-latest)
*   [甘草](https://ftp.apnic.net/stats/lacnic/delegated-lacnic-extended-latest)

我们对这样的条目感兴趣(来自成熟的 NCC 文件):

```
ripencc|FR|ipv4|2.0.0.0|1048576|20100712|allocated|... 
```

这一行告诉我们 IPv4 块`2.0.0.0/12`被分配给了法国(`FR`)。我们知道它是一个`/12`,因为第五列告诉我们这个块中有多少个 IP 地址(1048576)。下面的公式给你 CIDR 面具:`-log2(1048576) + 32 = 12`。

(IPv6 条目直接给出了 CIDR 掩码，因为在一个“小”块中可以有天文数字的 IPv6 地址，例如，`/64`有 18，446，744，073，709，551，616 个。)

但是，地址的数量在这里很有用，因为我们只对最低的 IP 地址和最高的 IP 地址感兴趣。也就是说，我们有:

*   最低:2.0.0.0
*   最高:2.0.0.0 + 1048576 - 1 = 2.15.255.255

(记住:IPv4 地址只是带符号的 32 位整数。)

因此，我们现在知道范围`2.0.0.0-2.15.255.255`内的任何请求地址都位于法国。

我们可以为每个块建立一个包含最低和最高地址的列表。标准的`ipaddress`模块非常方便地将 IP 地址和整数相加。

下面是一个简单的 Python 解析器:

```
import csv
import ipaddress
import math

def size_to_cidr_mask(c):
    """ c = 2^(32-m), m being the CIDR mask """
    return int(-math.log2(c) + 32)

def parse_rir_file(filename):
    with open(filename) as f:
        rows = csv.reader(f, delimiter='|')
        for r in rows:
            try:
                rir, country_code, ip_version, ip, mask, *_ = r
            except ValueError:
                continue
            if ip == '*':
                continue
            if ip_version == 'ipv4':
                length = int(mask)
                addr = ipaddress.ip_address(ip)
                yield {
                    'ip_low': addr,
                    'ip_high': addr + length - 1,
                    'rir': rir,
                    'country': country_code,
                    'range': ip+'/'+str(size_to_cidr_mask(length)),
                } 
```

函数`parse_rir_file`返回摄取的一个 RIR 文件的迭代器。我们可以将它们合并成一个序列，包含整个世界的块:

```
import itertools as it

data = list(it.chain(
    parse_rir_file('delegated-ripencc-extended-latest'),
    parse_rir_file('delegated-arin-extended-latest'),
    parse_rir_file('delegated-apnic-extended-latest'),
    parse_rir_file('delegated-afrinic-extended-latest'),
    parse_rir_file('delegated-lacnic-extended-latest')
)) 
```

这可能需要一段时间，具体取决于您的硬件...(在我装有 SSD 的笔记本电脑上需要几秒钟)如果你很好奇，现在可以数一数有多少 IPv4 块在使用中；)

## 查找

我们已经建立了自己的数据，现在我们必须对其进行分类。我们还需要在`ip_low` s ( `keys`)上建立一个索引列表，这样我们就可以在上面执行查找，然后从`data`中检索整个条目。

```
data.sort(key=lambda r: r['ip_low'])
keys = [r['ip_low'] for r in data] 
```

一种简单的方法是简单地将每个`low`和`high`与所请求的 IP 地址进行比较，直到`low <= ip <= high`。然而，我们的列表非常庞大！最坏的情况是当一个未分配的 IP 地址被请求时:我们将一无所获地遍历整个列表。

```
def naive_lookup(keys, target):
    last_v = None
    for k,v in enumerate(keys):
        if last_v is None:
            last_v = v
        if last_v <= target < v:
            return k
        last_v = v
    return None 
```

我们不是在寻找一个特定的条目，而是寻找一个符合所请求地址的范围。对于这种工作，一个[二分法](https://en.wikipedia.org/wiki/Bisection_method)适合我们的需要。Python 为此提供了`bisect`模块。

```
def lookup(ip):
    ip = ipaddress.ip_address(ip)
    if not ip.is_global or ip.is_multicast: # Check bogon
        return None
    i = bisect.bisect_right(keys, ip)
    entry = data[i-1]
    assert(entry['ip_low'] <= ip <= entry['ip_high'])
    return entry 
```

也就是说，对于所请求的 IP 地址，操作`bisect_right(keys, ip)`有效地遍历列表以查看地址适合的位置。然而，我们必须确保地址属于一个块，因此有了`assert`。

## API

最后一步:制作一个 API 并托管它。这里有一个使用微框架[瓶子](https://bottlepy.org/)的简单例子。`rir`模块包含了我们上面定义的函数。

```
from bottle import Bottle, route, response
import ipaddress
import rir

app = Bottle()

def valid_ip(ip):
    try:
        ipaddress.ip_address(ip)
        return True
    except ValueError:
        return False

@app.route('/<ip>')
def lookup_ip(ip):
    if not valid_ip(ip):
        response.status = 400
        return {'error': 'Not a valid IPv4: %s' % ip}
    entry = rir.lookup(ip)
    if entry is None:
        return {
            'ip': ip,
            'bogon': True,
        }
    return {
        'ip': ip,
        'ip_low': str(entry['ip_low']),
        'ip_high': str(entry['ip_high']),
        'rir': entry['rir'],
        'country': entry['country'],
        'range': entry['range'],
    } 
```

示例:

```
curl -s localhost:8080/83.167.62.189 | jq 
```

```
{  "ip_low":  "83.167.32.0",  "range":  "83.167.32.0/19",  "rir":  "ripencc",  "country":  "FR",  "ip":  "83.167.62.189",  "ip_high":  "83.167.63.255"  } 
```