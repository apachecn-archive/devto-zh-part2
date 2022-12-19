# 研究 curl:第 2 部分

> 原文：<https://dev.to/captainsafia/looking-into-curl-part-2-idl>

已经是星期三了吗？我想是的。老实说，我差不多是在 auto-pilot 上写这些博客的，所以时间过得真的很快。

我将继续我在周一开始的系列，我将为`curl`分析`strace`。在上一篇博文中，我研究了`curl`进程如何管理域名解析。在这篇博文中，我将从那里继续。

跟踪的下一部分如下所示。

侧边栏:我不知道为什么我说“看起来像”，这正是它。唉，内心独白缺乏精确性。

```
socket(AF_INET, SOCK_DGRAM|SOCK_NONBLOCK, IPPROTO_IP) = 3
[pid 8819] connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("169.254.169.254")}, 16) = 0
[pid 8819] poll([{fd=3, events=POLLOUT}], 1, 0) = 1 ([{fd=3, revents=POLLOUT}])
[pid 8819] sendmmsg(3, [{msg_hdr={msg_name=NULL, msg_namelen=0, msg_iov=[{iov_base="\252_\1\0\0\1\0\0\0\0\0\0\5safia\5rocks\0\0\1\0\1", iov_len=29}], msg_iovlen=1, msg_controllen=0, msg_flags=0}, msg_len=29}, {msg_hdr={msg_name=NULL, msg_namelen=0, msg_iov=[{iov_base="\375\266\1\0\0\1\0\0\0\0\0\0\5safia\5rocks\0\0\34\0\1", iov_len=29}], msg_iovlen=1, msg_controllen=0, msg_flags=MSG_EOR|MSG_CONFIRM|MSG_RST|MSG_MORE|MSG_WAITFORONE|MSG_CMSG_CLOEXEC|0x80200000}, msg_len=29}], 2, MSG_NOSIGNAL) = 2
[pid 8819] poll([{fd=3, events=POLLIN}], 1, 5000 <unfinished ...>
[pid 8818] <... poll resumed> ) = 0 (Timeout) 
```

第一个系统调用启动一个到 IP 地址的连接。IP 地址没有给我任何线索，但我觉得它连接的端口号很有趣:端口 53。我查了一下，发现端口 53 是 DNS 服务器使用的默认端口号。看起来它正在与一个 DNS 服务器建立连接，试图解析域名。稍后通过`sendmmsg`命令发送的有效载荷的内容证实了这一怀疑。

```
[pid 8819] ioctl(3, FIONREAD, [85]) = 0
[pid 8819] recvfrom(3, "\375\266\201\200\0\1\0\2\0\0\0\0\5safia\5rocks\0\0\34\0\1\300\f\0"..., 2048, 0, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("169.254.169.254")}, [28->16]) = 85
[pid 8819] poll([{fd=3, events=POLLIN}], 1, 4940) = 1 ([{fd=3, revents=POLLIN}])
[pid 8819] ioctl(3, FIONREAD, [61]) = 0
[pid 8819] recvfrom(3, "\252_\201\200\0\1\0\2\0\0\0\0\5safia\5rocks\0\0\1\0\1\300\f\0"..., 65536, 0, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("169.254.169.254")}, [28->16]) = 61
[pid 8819] close(3) 
```

堆栈跟踪的下一个块从某个`gai.conf`文件中读取数据。我决定在文档中查找它可能负责什么。`gai.conf`是存储`getaddrinfo`功能配置的文件。在将主机名映射到我们可以连接的 IP 地址时,`getaddrinfo`函数负责大量繁重的工作。存储在`gai.conf`中的配置处理当对`getaddrinfo`的调用返回多个响应时应该做什么。我假设这意味着当一个主机名解析为多个地址时。在这种情况下，`gai.conf`文件允许我们保存关于我们实际想要连接到哪个地址的信息。

```
pid 8819] open("/etc/gai.conf", O_RDONLY|O_CLOEXEC) = 3
[pid 8819] fstat(3, {st_mode=S_IFREG|0644, st_size=2584, ...}) = 0
[pid 8819] fstat(3, {st_mode=S_IFREG|0644, st_size=2584, ...}) = 0
[pid 8819] read(3, "# Configuration for getaddrinfo("..., 4096) = 2584
[pid 8819] read(3, "", 4096) = 0
[pid 8819] close(3) 
```

堆栈跟踪的下一部分有点深奥，对我来说很难解析。我注意到程序正在打开一个类型为`AF_NETLINK`的新套接字。经过研究，我发现这种特殊的连接类型允许内核与用户空间通信。在这种情况下，套接字不便于与外界连接。不幸的是，知道这一点并不能让我了解通过这个套接字实际通信的内容。我正在做一个心理上的尝试，再往上一层，看看再次查看`curl`代码库能告诉我什么。

```
[pid 8819] futex(0x7f21c8972ee4, FUTEX_WAKE_PRIVATE, 2147483647) = 0
[pid 8819] socket(AF_NETLINK, SOCK_RAW, NETLINK_ROUTE) = 3
[pid 8819] bind(3, {sa_family=AF_NETLINK, nl_pid=0, nl_groups=00000000}, 12) = 0
[pid 8819] getsockname(3, {sa_family=AF_NETLINK, nl_pid=8818, nl_groups=00000000}, [12]) = 0
[pid 8819] sendto(3, {{len=20, type=0x16 /* NLMSG_??? */, flags=NLM_F_REQUEST|0x300, seq=1526219697, pid=0}, "\0\0\0\0"}, 20, 0, {sa_family=AF_NETLINK, nl_pid=0, nl_groups=00000000}, 12) = 20
[pid 8819] recvmsg(3, {msg_name={sa_family=AF_NETLINK, nl_pid=0, nl_groups=00000000}, msg_namelen=12, msg_iov=[{iov_base=[{{len=76, type=0x14 /* NLMSG_??? */, flags=NLM_F_MULTI, seq=1526219697, pid=8818}, "\2\10\200\376\1\0\0\0\10\0\1\0\177\0\0\1\10\0\2\0\177\0\0\1\7\0\3\0lo\0\0"...}, {{len=88, type=0x14 /* NLMSG_??? */, flags=NLM_F_MULTI, seq=1526219697, pid=8818}, "\2  \200\0\2\0\0\0\10\0\1\0\n\200\0\2\10\0\2\0\n\200\0\2\10\0\4\0\n\200\0\2"...}, {{len=0, type=0 /* NLMSG_??? */, flags=0, seq=0, pid=0}}], iov_len=4096}], msg_iovlen=1, msg_controllen=0, msg_flags=0}, 0) = 164
[pid 8819] recvmsg(3, {msg_name={sa_family=AF_NETLINK, nl_pid=0, nl_groups=00000000}, msg_namelen=12, msg_iov=[{iov_base=[{{len=72, type=0x14 /* NLMSG_??? */, flags=NLM_F_MULTI, seq=1526219697, pid=8818}, "\n\200\200\376\1\0\0\0\24\0\1\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\24\0\6\0"...}, {{len=72, type=0x14 /* NLMSG_??? */, flags=NLM_F_MULTI, seq=1526219697, pid=8818}, "\n@\200\375\2\0\0\0\24\0\1\0\376\200\0\0\0\0\0\0@\1\n\377\376\200\0\2\24\0\6\0"...}, {{len=393236, type=0xffff /* NLMSG_??? */, flags=NLM_F_REQUEST|NLM_F_MULTI|NLM_F_ACK|NLM_F_ECHO|NLM_F_DUMP_INTR|NLM_F_DUMP_FILTERED|0xffc0, seq=4294967295, pid=282}, "\32\1\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"...}], iov_len=4096}], msg_iovlen=1, msg_controllen=0, msg_flags=0}, 0) = 144
[pid 8819] recvmsg(3, {msg_name={sa_family=AF_NETLINK, nl_pid=0, nl_groups=00000000}, msg_namelen=12, msg_iov=[{iov_base=[{{len=20, type=NLMSG_DONE, flags=NLM_F_MULTI, seq=1526219697, pid=8818}, "\0\0\0\0"}, {{len=1, type=0x14 /* NLMSG_??? */, flags=NLM_F_REQUEST, seq=0, pid=0}}], iov_len=4096}], msg_iovlen=1, msg_controllen=0, msg_flags=0}, 0) = 20
[pid 8819] close(3) 
```

跟踪的下一部分对我来说同样深奥。在这种情况下，所建立的套接字连接与 IPv6 地址通信。同样，完全不知道这里发生了什么。除了阅读`curl`的代码之外，我认为运行 Wireshark 并查看网络转储告诉我的信息可能会更清楚。无论如何，我对这里发生的事情并不感兴趣。看起来很清楚，`curl`通过几个步骤从本地和远程 DNS 服务解析域名。

```
[pid 8819] socket(AF_INET6, SOCK_DGRAM, IPPROTO_IP) = 3
[pid 8819] connect(3, {sa_family=AF_INET6, sin6_port=htons(443), inet_pton(AF_INET6, "2400:cb00:2048:1::681b:a05e", &sin6_addr), sin6_flowinfo=htonl(0), sin6_scope_id=0}, 28) = -1 ENETUNREACH (Network is unreachable)
[pid 8819] connect(3, {sa_family=AF_UNSPEC, sa_data="\0\0\0\0\0\0\0\0\0\0\0\0\0\0"}, 16) = 0
[pid 8819] connect(3, {sa_family=AF_INET6, sin6_port=htons(443), inet_pton(AF_INET6, "2400:cb00:2048:1::681b:a15e", &sin6_addr), sin6_flowinfo=htonl(0), sin6_scope_id=0}, 28) = -1 ENETUNREACH (Network is unreachable)
[pid 8819] connect(3, {sa_family=AF_UNSPEC, sa_data="\0\0\0\0\0\0\0\0\0\0\0\0\0\0"}, 16) = 0
[pid 8819] connect(3, {sa_family=AF_INET, sin_port=htons(443), sin_addr=inet_addr("104.27.160.94")}, 16) = 0
[pid 8819] getsockname(3, {sa_family=AF_INET6, sin6_port=htons(56904), inet_pton(AF_INET6, "::ffff:10.128.0.2", &sin6_addr), sin6_flowinfo=htonl(0), sin6_scope_id=0}, [28]) = 0
[pid 8819] connect(3, {sa_family=AF_UNSPEC, sa_data="\0\0\0\0\0\0\0\0\0\0\0\0\0\0"}, 16) = 0
[pid 8819] connect(3, {sa_family=AF_INET, sin_port=htons(443), sin_addr=inet_addr("104.27.161.94")}, 16) = 0
[pid 8819] getsockname(3, {sa_family=AF_INET6, sin6_port=htons(43230), inet_pton(AF_INET6, "::ffff:10.128.0.2", &sin6_addr), sin6_flowinfo=htonl(0), sin6_scope_id=0}, [28]) = 0
[pid 8819] close(3) = 0 
```

此时，PID 为 8819 的进程(线程)退出，我们返回到主线程。我将在下一篇博文中开始查看堆栈跟踪的这一部分。