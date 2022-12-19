# 在 Let's Encrypt 中获取通配符证书

> 原文：<https://dev.to/nasa9084/lets-encrypt--4jg5>

[![Let's Encryptでワイルドカード証明書を取得する](../Images/92213423a78ad0ce9140ce4b153352f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VGhlmFxY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/content/images/2018/03/letsencrypt.png)

这是前几天在 twitter 上发表的受支持的 Let's Encrypt 通配符证书，今天凌晨，certbot 正式发表了支持通配符证书的消息[！](https://community.letsencrypt.org/t/acme-v2-and-wildcard-certificate-support-is-live/55579) [[1]](#fn1)

闲话不多说，实际上我会尝试获取通配符证书。

另外，这次的对象环境如下所示。

*   CentOS 7
*   nginx 作为反向代理
*   DNS

那么，我们继续工作。

在 CentOS 7 上使用 certbot 时，我想大多数都是在 yum 上安装 certbot 的，所以先更新一下。

```
$ sudo yum update 
```

省略输出，但 certbot 更新为 0.22.0。

0.22.0 是支持通配符的版本，所以没有问题！

官方的手册中写着使用[DNS-plugin](https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)，但是因为没有 Gehirn DNS 的插件，所以这次要手动进行。

执行以下命令。

```
# certbot certonly --manual --preferred-challenges dns -d *.web-apps.tech --server https://acme-v02.api.letsencrypt.org/directory
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer None
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): nasa.9084.bassclarinet@gmail.com
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org

-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: a

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: y
Starting new HTTPS connection (1): supporters.eff.org
Obtaining a new certificate
Performing the following challenges:
dns-01 challenge for web-apps.tech

-------------------------------------------------------------------------------
NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
-------------------------------------------------------------------------------
(Y)es/(N)o: y

-------------------------------------------------------------------------------
Please deploy a DNS TXT record under the name
_acme-challenge.web-apps.tech with the following value:

qiGA8Vep17l0nYJ1O1AdF68D9iT7bL5Mpoe3j7-Caag

Before continuing, verify the record is deployed.
-------------------------------------------------------------------------------
Press Enter to Continue 
```

如上所示，当系统提示添加 TXT 记录时，将指定的值添加到 DNS 中。

Gehirn 的形式如下。

[![Let's Encryptでワイルドカード証明書を取得する](../Images/4d2119c37b4baa24c8b7c97689dc8e3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cudgwvxD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/content/images/2018/03/Gehirn_txt_record.png)

添加后，请使用 dig 命令等待 DNS 记录已渗透()。

```
$ dig -t txt _acme-challenge.web-apps.tech

(前略)

;; ANSWER SECTION:
_acme-challenge.web-apps.tech. 30 IN    TXT "qiGA8Vep17l0nYJ1O1AdF68D9iT7bL5Mpoe3j7-Caag"

(以下略) 
```

返回运行 certbot 的终端，然后按 Enter 键继续。

```
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/web-apps.tech/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/web-apps.tech/privkey.pem
   Your cert will expire on 2018-06-12\. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
   Donating to EFF: https://eff.org/donate-le 
```

如果成功，将如上所示输出包含`Congratulations!`的信息。

fullchain 保存在`/etc/letsencrypt/live/<ドメイン名>/fullchain.pem`，密钥文件保存在`/etc/letsencrypt/live/<ドメイン名>/privkey.pem`。

自动更新的设定还在另一篇文章中。

* * *

1.  实际上，大概一周前发布了