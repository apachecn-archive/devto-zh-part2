# 如何用 OpenSMTPD 收发邮件

> 原文：<https://dev.to/mnlwldr/how-to-send-and-receive-emails-with-opensmtpd-jnf>

填充`/etc/mail/aliases`

```
echo "root: YOUR_LOCAL_USER" >> /etc/mail/aliases 
```

Enter fullscreen mode Exit fullscreen mode

这意味着发送到 root 用户的所有电子邮件都将发送到您的 _LOCAL_USER。

如果你愿意，你可以添加一些其他别名，如“联系人”或“嗨”。

```
echo "contact: YOUR_LOCAL_USER" >> /etc/mail/aliases
echo "hi: YOUR_LOCAL_USER" >> /etc/mail/aliases 
```

Enter fullscreen mode Exit fullscreen mode

添加完所有别名后，有必要运行`newaliases`。

```
newaliases
/etc/mail/aliases: 70 aliases 
```

Enter fullscreen mode Exit fullscreen mode

现在你应该创建一个 SSL 证书，比如这里的[或者使用一个现有的证书。](https://wildauer.io/acme-client-openbsd/)

现在我们可以填充`/etc/mail/smtpd.conf`并用您的域名替换*example.com*。我们想要的一切是接收本地用户的其他电子邮件，并将所有电子邮件发送给用户 mbox =>/var/mail/YOUR _ LOCAL _ USER

```
table aliases file:/etc/mail/aliases

pki example.com key "/path/to/your/example.com.key"
pki example.com certificate "/path/to/your/exmaple.com.crt"

listen on egress tls pki exmaple.com
listen on egress port 587 hostname example.com tls-require pki example.com auth mask-source

accept from any for domain "example.com" alias <aliases> deliver to mbox
accept from any for local alias <aliases> deliver to mbox
accept from local for any relay 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。OpenSMTPD 正在侦听端口 25 和 587，并接受加密连接。密钥和证书位置是用 pki 关键字配置的。并将消息传递给用户的系统 mbox(/var/mail/YOUR _ LOCAL _ USER)。

用您的域名填充`/etc/mail/mailname`。

```
echo "example.com">/etc/mail/mailname 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以检查你的配置`smtpd -n`，当一切正常时，你可以重启 OpenSMTPD `rcctl restart smtpd`

使用 telnet 检查您的 OpenSMTPD 服务器

```
telnet exmaple.com

EHLO exmaple.com
MAIL FROM: <from@somehwere.tld>
RCPT TO: <to@example.com>
DATA
Subject: This is just a test
<- blank line ->
This is a test :)
<- blank line ->
.
QUIT 
```

Enter fullscreen mode Exit fullscreen mode

如果它说的是类似于`250 2.0.0: 574eff74 Message accepted for delivery`的话，那么恭喜 [OpenSMTPD](https://www.opensmtpd.org/) 成功了。