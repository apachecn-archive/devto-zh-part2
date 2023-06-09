# 在具有自定义域的 bitnami prestashop 实例上启用 https

> 原文：<https://dev.to/jurgen/enabling-https-on-a-bitnami-prestashop-instance-with-a-custom-domain-4plk>

本教程是在浪费了太多时间试图弄清楚如何在 prestashop bitnami 实例上启用 https 之后编写的。

这些步骤适用于:
PrestaShop 1 . 7 . 4 . 3-0
Debian Linux 9(HVM)
AWS EC2

**要在 Prestashop bitnami 实例上启用 SSL 并自动重定向到 https，请执行以下步骤:**

1.  配置您的自定义域
    按照本 bitnami 教程的每个步骤来设置您的自定义域:
    [https://docs . bitnami . com/AWS/FAQ/configuration/configure-custom-domain/](https://docs.bitnami.com/aws/faq/configuration/configure-custom-domain/)

2.  配置您的 prestashop 以接受 SSL
    这一步听起来可能很明显，但是如果您像我一样是一个 prestashop 新手，就我所知，它从未在任何教程或已解决的问题中被提及。因此，如果跳过这一步，您会遇到一些重定向错误。
    要启用修复此问题，您需要**配置您的 prestashop 以接受 ssl**
    为此，进入您的 prestashop 管理员(yourprestashopsite.com/administration)>配置>商店参数>常规
    在常规页面中，确保“启用 ssl”和“在所有页面上启用 SSL”切换为“是”。

3.  配置您的 apache 以启用 https 支持
    按照这个交互式 bitnami 教程在 bitnami 上启用 https 支持:
    【https://www.katacoda.com/bitnami/scenarios/configure-https】T2
    我鼓励您使用让我们加密 SSL 证书:它是免费的，易于安装。Bitnami 提供了一个脚本来生成它，还提供了一个 cron 作业来在证书过期时自动更新您的证书。

瞧，你现在应该自动重定向到你的域名**，启用 https】。**

希望这有所帮助！