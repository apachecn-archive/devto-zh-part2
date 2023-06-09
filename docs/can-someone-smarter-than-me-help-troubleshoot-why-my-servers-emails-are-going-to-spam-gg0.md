# 有没有比我更聪明的人能帮我解决为什么我的服务器邮件会变成垃圾邮件的问题？

> 原文：<https://dev.to/jackharner/can-someone-smarter-than-me-help-troubleshoot-why-my-servers-emails-are-going-to-spam-gg0>

我有一台运行 Ubuntu 14.04 的 LEMP 服务器，使用邮箱作为邮件服务器。

我有一个运行 Ubuntu 16.04 的 LAMP 服务器，我最初设置 Postfix 作为一个 SMTP 中继，通过邮箱发送邮件。我以为这很有效，但现在看来我所有的电子邮件都变成了垃圾邮件。这真的只是为了一个网站，所以我卸载了 Postfix，只安装了一个 WordPress SMTP 插件，但这似乎没有什么不同。

据我所知，我的域名或 IP 地址都没有出现在黑名单上

我用[不是垃圾邮件](http://www.isnotspam.com)测试器测试了一封邮件，结果如下:

```
==========================================================
Summary of Results
==========================================================

SPF Check : pass
Sender-ID Check : pass
DKIM Check : pass
SpamAssassin Check : ham (non-spam)
==========================================================
Details:
==========================================================

HELO hostname: box.harnerdesigns.com
Source IP: <My Mail Server IP>
mail-from: hello@harnerdesigns.com
Anonymous To: <random email>@isnotspam.com
--------------------------------------------------------------
SPF check details:
---------------------------------------------------------------

Result: pass
ID(s) verified: smtp.mail=hello@harnerdesigns.com
DNS record(s):
harnerdesigns.com. 1799 IN TXT "v=spf1 include:servers.mcsv.net include:box.harnerdesigns.com include:server.harnerdesigns.com ?all"

---------------------------------------------------------------
Sender-ID check details:
---------------------------------------------------------------

Result: pass

ID(s) verified: smtp.mail=hello@harnerdesigns.com
DNS record(s):
harnerdesigns.com. 1799 IN TXT "v=spf1 include:servers.mcsv.net include:box.harnerdesigns.com include:server.harnerdesigns.com ?all"

---------------------------------------------------------------
DKIM check details:
---------------------------------------------------------------

Result: pass
ID(s) verified: header.From=hello@harnerdesigns.com
Selector=mail
domain=harnerdesigns.com
DomainKeys DNS Record=mail._domainkey.harnerdesigns.com

---------------------------------------------------------------
SpamAssassin check details:
---------------------------------------------------------------
SpamAssassin 3.4.1 (2015-04-28)

Result: ham (non-spam) (03.7points, 10.0 required)

pts rule name description
--------- ---------------------- -------------------------------

* 3.5 BAYES_99 BODY: Bayes spam probability is 99 to 100%
* [score: 1.0000]
* -0.0 SPF_HELO_PASS SPF: HELO matches SPF record
* -0.0 SPF_PASS SPF: sender matches SPF record
* -0.0 RP_MATCHES_RCVD Envelope sender domain matches handover relay domain
* 0.2 BAYES_999 BODY: Bayes spam probability is 99.9 to 100%
* [score: 1.0000]
* 0.1 HTML_MESSAGE BODY: HTML included in message
* -0.1 DKIM_VALID_AU Message has a valid DKIM or DK signature from author's
* domain
* 0.1 DKIM_SIGNED Message has a DKIM or DK signature, not necessarily
* valid
* -0.1 DKIM_VALID Message has at least one valid DKIM or DK signature
X-Spam-Status: Yes, hits=3.7 required=-20.0 tests=BAYES_99,BAYES_999,
DKIM_SIGNED,DKIM_VALID,DKIM_VALID_AU,HTML_MESSAGE,RP_MATCHES_RCVD,
SPF_HELO_PASS,SPF_PASS autolearn=no autolearn_force=no version=3.4.0
X-Spam-Score: 3.7

To learn more about the terms used in the SpamAssassin report, please search
here: http://wiki.apache.org/spamassassin/ 
```

Enter fullscreen mode Exit fullscreen mode

## 情侣提问

1.  我需要在 SPF 记录中包括`harnerdesigns.com`吗？`box.harnerdesigns.com`是邮件服务器，`server.harnerdesigns.com`是 LAMP 服务器，但是`harnerdesigns.com`是所有邮件将被发送到的域。
2.  看看`X-Spam-Status`，`required=-20.0`是不是垃圾邮件设置它来阻止一切的一部分？我所看到的一切都表明，你只需要将你的垃圾邮件分数保持在 5 分以下，就可以通过大多数过滤器。
3.  我不知道如何解决这个问题。对下一步该去哪里有什么建议吗？