# 全面缓解

> 原文：<https://dev.to/danielw/efail-mitigations-5179>

您可能已经听说过，许多流行的电子邮件客户端存在一个缺陷，使得攻击者能够解密 PGP 或 S/MIME 加密的电子邮件内容。

这是一个网站，上面有所有关于攻击的信息: [EFAIL](https://efail.de)

*让我们收集专门针对受直接渗透攻击影响的客户端的缓解技术(苹果邮件、iOS 邮件和 Mozilla Thunderbird)*

* * *

到目前为止，我读到了一些即时缓解措施:

*   关闭邮件客户端的 OpenPGP 和 S/MIME 加密，使用外部工具(如 [Keybase](http://keybase.io) )通过复制粘贴内容进行加密/解密。
*   禁用 HTML 支持。仅显示明文
*   **(对于雷鸟)**在 Enigmail 设置中，保持 OpenPGP 加密启用，但关闭消息的自动解密/验证

[![A screenshot of thunderbird with the 'Enigmail' dropdown menu open and the selection on the menu item 'Automatically Decrypt/Verify Messages'](img/abf143e2346b0a2cb58e444cf9c95dfd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QDvwVKpE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tawh034iq7dhe7wvlmff.png)

*   **(针对雷鸟)**不允许消息中出现远程内容。

[![A screenshot of thunderbirds preferences with the 'Privacy' tab selected and the mouse cursor hovering over the checkbox 'Allow remote content in messages'](img/259b5cff51271dee7178751639129ba0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n-mA1b0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1ndg1zkk8owsun3sajk.png)

#### 注

最后两个只是保护你不被攻击。如果您接受该特定邮件的远程内容和/或手动解密邮件，您仍可能被利用。