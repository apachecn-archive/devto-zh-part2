# 关于 DNS 的实际问题–edns 0

> 原文：<https://dev.to/dannypsnl/practical-issue-about-dns--edns0-3e9p>

我必须为我们的 DNS 服务器创建一个检查器。

我们的 DNS 服务器将通过`TXT`返回其配置。

码想:

```
package main

import "github.com/miekg/dns"

// func main
m := dns.Msg{}
// `google.com.` is correct format, `.` is required!
m.SetQuestion("google.com.", dns.TypeTXT)
c := dns.Client{}
// write out port 53(:53) is required, dns package we used at here won't automatically fix port to 53.
// Unlike some cli tool such as Dig
response, _, err := c.Exchange(&m, "8.8.8.8:53")
for _, t := range r.Answer {
    txt := t.(*dns.TXT)
    // dns.TXT.Txt is []string
    for _, t := txt.Txt {
        // t is string, now you can use it
        println(t)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们的配置流太大，超过了 UDP 的限制！我们必须得到它。

RFC 6891 :

> 通过 UDP [RFC1035]发送时，传统 DNS 消息的大小被限制为 512 个八位字节。

经过研究，我发现我们可以通过 EDNS0 扩展 DNS 包的大小。

RFC 6891 :

> EDNS(0)指定了一种方法来通告其他功能，如更大的响应大小功能，这是为了帮助避免截断的 UDP 响应，这反过来会导致 TCP 上的重试。因此，它支持传输这些较大的数据包，而无需借助 TCP 进行传输。

代码为:

```
m.SetEdns0(4096, true) 
```

Enter fullscreen mode Exit fullscreen mode

在`SetEdns0` :

```
func (dns *Msg) SetEdns0(udpsize uint16, do bool) *Msg {
    e := new(OPT)
    e.Hdr.Name = "."
    e.Hdr.Rrtype = TypeOPT
    e.SetUDPSize(udpsize)
    if do {
        e.SetDo()
    }
    dns.Extra = append(dns.Extra, e)
    return dns
} 
```

Enter fullscreen mode Exit fullscreen mode

改变 UDP 大小的代码是`e.SetUDPSize`，我们来看看:

```
func (rr *OPT) SetUDPSize(size uint16) {
    rr.Hdr.Class = size
} 
```

Enter fullscreen mode Exit fullscreen mode

`OPT.Hdr`的类型是`RR_Header`，然后我们深入到 **RFC 6891** ，在第 5 页你可以找到:

```
The fixed part of an OPT RR is structured as follows:

+------------+--------------+------------------------------+
| Field Name | Field Type | Description |
+------------+--------------+------------------------------+
| NAME | domain name | MUST be 0 (root domain) |
| TYPE | u_int16_t | OPT (41) |
| CLASS | u_int16_t | requestor's UDP payload size |
| TTL | u_int32_t | extended RCODE and flags |
| RDLEN | u_int16_t | length of all RDATA |
| RDATA | octet stream | {attribute,value} pairs |
+------------+--------------+------------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，`RR_Header.Class`将改变 UDP 有效负载的大小。庆祝一下！！！

更多信息:

*   [RFC 2671](https://tools.ietf.org/html/rfc2671)
*   [RFC 6891](https://tools.ietf.org/html/rfc6891)
*   [男性千克/dns](https://github.com/miekg/dns)