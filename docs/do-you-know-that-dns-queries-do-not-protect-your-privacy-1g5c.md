# 你知道 DNS 查询不保护你的隐私吗？

> 原文：<https://dev.to/exadra37/do-you-know-that-dns-queries-do-not-protect-your-privacy-1g5c>

对于关心隐私的人来说，他们必须从 DNS 开始，因为 DNS 查询是以纯文本形式进行的，因此网络中的任何人都可以看到它们，比如您的 ISP，即使您的 ISP 不是为您解析 DNS 查询的人。

哦，但是我用的是像 OpenVpn 这样的虚拟专用网，所以我没问题，对不起，但是你不是...点击阅读更多相关信息[。](https://unix.stackexchange.com/questions/434916/how-to-fix-openvpn-dns-leak)

您需要使用 DNS over HTPPS 来解决隐私问题。在[这篇文章](https://scotthelme.co.uk/securing-dns-across-all-of-my-devices-with-pihole-dns-over-https-1-1-1-1/)中，我们可以看到如何用 Raspberry PI 和 Pi-Hole 软件设置一个家庭 DNS 服务器。