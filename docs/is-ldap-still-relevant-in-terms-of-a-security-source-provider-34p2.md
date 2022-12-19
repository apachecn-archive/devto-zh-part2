# 作为一个安全源提供者，LDAP 仍然有意义吗？

> 原文：<https://dev.to/taitung/is-ldap-still-relevant-in-terms-of-a-security-source-provider-34p2>

我将从事一个与安全相关的新项目。目前，我正在研究在哪里存储用户凭证，如密码和令牌。我们现在所做的是将这些敏感信息与用户资料一起存储在 MongoDB 中。嗯，我不确定这是不是一个好的设计。但是，我想做一个改变，将凭证和配置文件数据分离到不同的存储，如 LDAP 和 MySQL，甚至 MongoDB。主要区别是在 LDAP 中存储用户凭证，在 MySQL 或 MongoDB 中存储配置文件数据。考虑 LDAP 可以提供的性能和安全级别，您认为 LDAP 在这种情况下仍然是一种相关的技术吗？或者说，你怎么用 LDAP？