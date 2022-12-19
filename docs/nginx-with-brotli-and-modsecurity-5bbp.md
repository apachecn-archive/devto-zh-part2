# Nginx 与 Brotli 和 ModSecurity

> 原文：<https://dev.to/krish512/nginx-with-brotli-and-modsecurity-5bbp>

您的下一次部署是否使用 Docker？

web 服务器怎么样？Nginx？不错的选择！

但是如何管理安全性呢？

码头已经安全了吗？神话！醒醒

是的，你需要处理好自己的安全问题，Docker 不会做任何事情来帮助你保护你的终端的公共访问。

嗯，我最近偶然发现了 Nginx [ModSecurity](https://www.modsecurity.org/) 模块，觉得很神奇。

它处理大量的漏洞，并使你的应用程序免受主要的 web 和 SQL 漏洞的攻击，否则 nginx default 不会处理这些漏洞。

另外， [Brotli](https://github.com/google/brotli) 是 Google 提供的一个更好的压缩工具，与 GZip 相比，它提供了 20%的额外压缩。

哇！不是吗？那么，如何在您的部署中使用它呢？

这需要大量的构建、编译和配置工作。保证至少 15 分钟的构建时间！

别担心，我会支持你，用所有这些为你建立一个码头工人的形象。

try[https://github . com/krish 512/docker _ engine _ modsecurity](https://github.com/krish512/docker_nginx_modsecurity)，

具有 Modsecurity v3 和 Brotli 压缩级别 11 的最新 Nginx

PS:不是真的推广我的回购，只是用我的贡献来帮助社区，并期待 PRs 对回购的提升，使其功能更加丰富。