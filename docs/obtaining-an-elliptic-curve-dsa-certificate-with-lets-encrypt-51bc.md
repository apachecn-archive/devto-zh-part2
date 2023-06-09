# 从“让我们加密”中获取椭圆曲线证书

> 原文：<https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

编辑 2022 年 5 月 23 日:[见本文:“2022 年选择椭圆曲线签名算法指南”](https://soatok.blog/2022/05/19/guidance-for-choosing-an-elliptic-curve-signature-algorithm-in-2022/)

椭圆曲线(EC)证书，其中的公钥使用椭圆曲线加密，除了有一个很酷的名字外，还需要有[【现代】兼容性](https://wiki.mozilla.org/Security/Server_Side_TLS#Recommended_configurations)，由 [Mozilla 天文台](https://observatory.mozilla.org)测量。

在撰写本文时，Let's Encrypt [即将推出的功能](https://letsencrypt.org/upcoming-features/)页面表明，ECDSA ( [椭圆曲线数字签名算法](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)，而不是 ECDH、[椭圆曲线 Diffie-Hellman](https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman) )根证书和中间证书将于 2019 年在 Q1 推出，但目前还没有迹象表明 Let's Encrypt 何时将支持为最终用户生成 EC 证书。然而，幸运的是，让我们加密 will *签名*在证书签名请求(CSR)中传递给它的 EC 证书。

因此，与此同时，如果我们想要一个来自 Let's Encrypt 的 EC 证书，我们需要创建我们自己的证书，然后请求 Let's Encrypt 对它进行签名。

好在过程并不难。

在本例中，我们将使用 ECDSA 和 P-384 (secp384r1)曲线生成一个私钥，该曲线几乎支持 IE11 的通用浏览器(因此，它包含在 Mozilla 的“现代”兼容性要求中)。

* * *

# 生成私钥

首先，我们用 OpenSSL 生成私钥。我们将使用的 OpenSSL 命令是`ecparam` ( `man openssl`)，用于“EC 参数操作和生成”，并将配置参数传递给该命令(`openssl ecparam -help`)。

```
openssl ecparam -genkey -name secp384r1 -out privkey.pem 
```

Enter fullscreen mode Exit fullscreen mode

*   `-genkey`选项告诉 OpenSSL 生成一个 EC 密钥。
*   `-name`参数告诉 OpenSSL 使用哪条曲线。
*   `-out`参数告诉 OpenSSL 将输出写入文件。

请注意，默认情况下，OpenSSL 以 PEM 格式写入其输出。

我们可以检查 OpenSSL 用`ec`命令做了正确的事情，该命令处理 EC 密钥:

```
openssl ec -in privkey.pem -noout -text 
```

Enter fullscreen mode Exit fullscreen mode

*   `-in`是输入文件
*   `-noout`告诉 OpenSSL 不要输出密钥，这只会毫无意义地将`privkey.pem`输出到 stdout。
*   `-text`告诉 OpenSSL 以明文格式写出关于密钥的信息

 *如果一切顺利，密钥创建正确，OpenSSL 将显示如下内容:

```
read EC key
Private-Key: (384 bit)
priv:
    [redacted]
pub:
    [omitted]
ASN1 OID: secp384r1
NIST CURVE: P-384 
```

Enter fullscreen mode Exit fullscreen mode

这证实了钥匙是用 P-384 曲线制成的。

* * *

# 为证书创建 OpenSSL 配置

接下来，我们必须创建一个 OpenSSL 配置文件，其中包含特定于我们希望获得 TLS 证书的域的参数。在本例中，我们将在文件`openssl.cnf` :
中输入以下配置

```
[ req ]
prompt = no
encrypt_key = no
default_md = sha512
distinguished_name = dname
req_extensions = reqext

[ dname ]
CN = example.com
emailAddress = admin@example.com

[ reqext ]
subjectAltName = DNS:example.com, DNS:*.example.com 
```

Enter fullscreen mode Exit fullscreen mode

这些[配置选项](https://www.openssl.org/docs/manmaster/man1/req.html#CONFIGURATION_FILE_FORMAT)的简要说明:

在必填(`[ req ]`)部分:

*   告诉 OpenSSL 从配置文件中获取尽可能多的配置
*   `encrypt_key = no`告诉 OpenSSL 不要用密码加密私钥。([加密私钥是 Nginx](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_password_file) 支持的，但是我不用。)
*   `default_md = sha512`告知 OpenSSL 使用 SHA512 签署 CSR。(AFAIK，Let's Encrypt 只支持签名为 SHA256 的 RSA，但这并不意味着我们不能在 CSR 上使用更强的加密。)
*   `distinguished_name = dname`告诉 OpenSSL 寻找用于识别名配置选项的`[ dname ]`部分。
*   `req_extensions = reqext`告诉 OpenSSL 为请求的扩展配置选项寻找一个`[ reqext ]`部分，这是配置[主题别名](https://en.wikipedia.org/wiki/Subject_Alternative_Name)(San)的地方。

在可分辨名称(`[ dname ]`)部分:

*   `CN = example.com`指定证书的[通用名](https://www.ssl.com/faqs/common-name/)。
*   `emailAddress = admin@example.com`应该很明显。

在请求的扩展(`[ reqext ]`)部分中，`subjectAltName`提供了证书的 San 列表。(Chrome，从 v58 开始，[要求常用名包含在 San 列表中](https://support.google.com/chrome/a/answer/7391219?hl=en))。

让我们加密 v2 支持通配符域，因此在本例中，我为 apex 之外的主机请求了一个单级通配符(`*.example.com`)。

* * *

# 创建证书签名请求

最后一个客户端步骤是使用 OpenSSL 生成证书签名请求，然后我们将它传递给 Let's Encrypt to sign，并返回给我们已签名的证书。

生成 CSR 所需的 OpenSSL 命令是`req` ( `man openssl`和`openssl req -help`)。

```
openssl req -new -config openssl.cnf -key privkey.pem -out csr.pem 
```

Enter fullscreen mode Exit fullscreen mode

*   `-new`告诉 OpenSSL 我们正在创建一个 CSR(而不是检查现有的 CSR)
*   `-config openssl.cnf`指定我们在上面创建的配置文件
*   `-key privkey.pem`指定我们在上面生成的私钥
*   `-out csr.pem`告诉 OpenSSL 将 CSR 写入输出文件(而不是 stdout)

我们可以验证我们正确地生成了 CSR:

```
openssl req -in csr.pem -noout -text -verify 
```

Enter fullscreen mode Exit fullscreen mode

*   `-verify`请求 OpenSSL 验证 CSR 上的签名

这将在输出中产生预期的结果:

```
verify OK
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: CN = example.com, emailAddress = admin@example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (384 bit)
                pub:
                    [omitted]
                ASN1 OID: secp384r1
                NIST CURVE: P-384
        Attributes:
        Requested Extensions:
            X509v3 Subject Alternative Name:
                DNS:example.com, DNS:*.example.com
    Signature Algorithm: ecdsa-with-SHA512
         [omitted] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 让我们加密签名我们的证书

最后一步是将 CSR 传递给一个 ACME 客户端来进行加密，`certbot`是最常见的客户端。

传递给`certbot`客户端的命令行选项会根据我们的设置、我们的域是向谁注册的等等而有所不同。我们通常需要使用`certonly`命令，我们也可以使用 [certbot DNS 插件](https://certbot.eff.org/docs/using.html#dns-plugins)中的一个。

例如，如果`example.com`注册了 AWS Route 53，我们可以使用相应的插件来处理验证，这非常方便，在这个过程中不需要人工干预。(用 AWS 凭证配置 Route 53 DNS 插件超出了本文的范围。)

通常建议先做一个`--dry-run`来确保一切正常。

```
certbot certonly --dry-run --dns-route53 --domain "example.com" --domain "*.example.com" --csr csr.pem 
```

Enter fullscreen mode Exit fullscreen mode

*   注意，通配符两边需要引号，以防止 shell glob 扩展，通常这是个好主意。
*   `--csr csr.pem`告诉`certbot`我们已经有了一个证书，我们只需要加密来为我们签名。

`certbot`客户端将检查命令行上请求的域列表是否与证书中列出的域相匹配，它将使用 Route 53 DNS 插件来验证我们对该域的所有权，并让我们知道是否有任何错误。

如果没什么问题，它会这么告诉你:

```
IMPORTANT NOTES:
 - The dry run was successful. 
```

Enter fullscreen mode Exit fullscreen mode

真正的命令紧随其后:

```
certbot certonly --dns-route53 --domain "example.com" --domain "*.example.com" --csr csr.pem 
```

Enter fullscreen mode Exit fullscreen mode

(长时间)延迟后，客户端将产生以下输出:

1.  签名证书:`0000_cert.pem`
2.  根证书和中间证书:`0000_chain.pem`
3.  证书+中间体:`0001_chain.pem`

此时，可以删除 CSR `csr.pem`。

如果我们好奇，可以使用`x509`命令:
用 OpenSSL 检查客户端返回的证书

```
openssl x509 -in 0001_chain.pem -noout -text 
```

Enter fullscreen mode Exit fullscreen mode

唉，我们会发现，如上所述，Let's Encrypt 已经用 SHA256 签名签署了我们的证书。(除了更安全之外， [SHA512 在现代 64 位 CPU 上的性能优于 sha 256](https://crypto.stackexchange.com/questions/26336/sha512-faster-than-sha256)。)但是我们的公钥应该还是用 ECDSA。

这些文件毫无特色，所以我们应该以一种更具知识性的方式来移动和组织它们。

在 Debian Linux 上，我喜欢为我的域创建子目录，将私钥保存在`/etc/ssl/private/example.com/privkey.pem`中，证书:

*   `/etc/ssl/certs/example.com/cert.pem`
*   `/etc/ssl/certs/example.com/chain.pem`
*   `/etc/ssl/certs/example.com/fullchain.pem`

* * *

# **奖励回合:**配置网络服务器

在这一点上，我们有我们的证书在手。我们可以配置我们选择的 web 服务器来使用它们。举个简单的例子，一个 Nginx 配置片段，包括由 Mozilla Observatory 为其“现代”配置生成的修改规则[，如下:](https://mozilla.github.io/server-side-tls/ssl-config-generator/) 

```
ssl_certificate_key /etc/ssl/private/example.com/privkey.pem;
ssl_certificate /etc/ssl/certs/example.com/fullchain.pem;
ssl_trusted_certificate /etc/ssl/certs/example.com/chain.pem;

# Share 50 MB session cache
ssl_session_cache shared:SSL:50m;

# Disable session tickets
ssl_session_tickets off;

# Use only TLSv1.2+
ssl_protocols TLSv1.2 TLSv1.3;

# Safe ciphers
ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-\
RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256';
ssl_prefer_server_ciphers on;

# Safe curves
ssl_ecdh_curve secp521r1:secp384r1:prime256v1;

# OCSP Stapling
ssl_stapling on;
ssl_stapling_verify on;

# CloudFlare for DNS lookup
resolver 1.1.1.1;

add_header Content-Security-Policy "default-src 'none'; img-src 'self'; script-src 'self'; style-src 'self'; frame-ancestors 'none'";
add_header X-Content-Type-Options "nosniff";
add_header X-Frame-Options "sameorigin";
add_header X-XSS-Protection "1; mode=block";
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们做的一切都是正确的，当我们用 Chrome 这样的 web 浏览器检查证书时，它将确认这是一个 EC 证书:

[![Chrome certificate inspector](img/ed197a9407d48209a80d9727deff1df5.png "Chrome certificate inspector")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iR5pDI18--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6N2USsV.png)

另外， [Mozilla Observatory](https://observatory.mozilla.org/) 会给我们一个 A+的成绩！

[![Mozilla Observatory grade](img/897dfbc05f5f563b57a3b136ca4533d0.png "Mozilla Observatory grade")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ViRNNFbo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/aTi2Yxr.png)

* * *

# Postscript

我们还应该创建一个 cron job / systemd 计时器来更新我们的证书。这是留给读者的练习。*