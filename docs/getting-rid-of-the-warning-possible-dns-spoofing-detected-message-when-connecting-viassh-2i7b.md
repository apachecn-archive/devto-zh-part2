# 摆脱“警告:检测到可能的 DNS 欺骗！”通过 SSH 连接时的消息

> 原文：<https://dev.to/segunadeleye/getting-rid-of-the-warning-possible-dns-spoofing-detected-message-when-connecting-viassh-2i7b>

[![Robot Cleaner](img/ba525340867ce4fb389c2d094622d266.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SwjqcS1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f59tx79i71ltx6ehqhm2.jpg)

我在迁移服务器时遇到了这个错误。我试图通过 SSH 连接到其中一个服务器，然后这个出现了。

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@       WARNING: POSSIBLE DNS SPOOFING DETECTED!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
The ECDSA host key for blog.dealdey.com has changed,
and the key for the corresponding IP address 176.31.35.20
is unchanged. This could either mean that
DNS SPOOFING is happening or the IP address for the host
and its host key have changed at the same time.
Offending key for IP in /Users/Macbook/.ssh/known_hosts:147
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:k+mijCohJm9g2pXglfmAgrvxWYqDtuzGO7do+Yt2Sd4.
Please contact your system administrator.
Add correct host key in /Users/Macbook/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/Macbook/.ssh/known_hosts:95
ECDSA host key for blog.dealdey.com has changed and you have requested strict checking.
Host key verification failed. 
```

出现这个警告是因为 blog.dealdey.com 现在有了一个新的 IP 地址。存储在我的本地计算机上的 known_hosts 文件中的现有 ECDSA(椭圆曲线数字签名算法)主机密钥对于连接到新的服务器不再有效，因此出现了一个黄色标志。

## 已知 _ 主机文件

当您第一次通过 SSH 连接到服务器时，服务器的公钥被发送到您的本地机器，并被添加到一个已知主机的列表中，该列表存储在 known_hosts 文件中。

```
> ssh user@blog.dealdey.com
The authenticity of host 'blog.dealdey.com (176.31.35.18)' can't be established.
ECDSA key fingerprint is SHA256:exV5JiKkxDDh90/Bne5lRYWbpbX1al2KlNw0aO0hlcM.
Are you sure you want to continue connecting (yes/no)? 
```

用`yes`响应会将主机密钥添加到已知主机列表中。

```
Warning: Permanently added 'blog.dealdey.com' (ECDSA) to the list of known hosts. 
```

对于服务器的所有后续连接，服务器都要经过身份验证。SSH 客户端检查 known_hosts 文件，以确保它即将连接到正确的服务器。如果 SSH 客户端检测到服务器主机密钥和 known_hosts 文件中存储的密钥之间存在任何差异，就会阻止连接并显示警告。

有两种方法可以修复警告。

### 1。删除旧的主机密钥

我们可以通过从 known_hosts 文件的指定行(第 95 行)中删除 blog.dealdey.com 的违规(即现有)ECDSA 密钥来解决此问题。在本地机器上找到并打开 known_hosts 文件。
删除第 95 行。
通过 SSH 连接到服务器。

如果警告再次出现，请删除警告中指定的行。删除域的所有违规密钥后，新服务器的新主机密钥将添加到 known_hosts 文件中，您将成功连接到服务器。

### 2。使用 ssh-keygen

另一种方法是使用 ssh-keygen 删除域的所有密钥。只需运行下面的命令。确保将路径/to/known_hosts 替换为本地计算机上的 known_hosts 文件的路径。

```
ssh-keygen -f "path/to/known_hosts" -R blog.dealdey.com 
```

通过 SSH 连接到服务器。新服务器的新主机密钥将添加到 known_hosts 文件中，您将成功连接到该服务器。

虽然这两种方法都解决了问题，但方法 2 肯定比方法 1 更有效、更快、更不容易出错。

你可以在这里阅读更多关于 DNS 欺骗的内容[https://www . how togeek . com/161808/htg-explains-what-is-DNS-cache-poisoning/](https://www.howtogeek.com/161808/htg-explains-what-is-dns-cache-poisoning/)以及关于 ECDSA 的内容在这里[https://en . Wikipedia . org/wiki/Elliptic _ Curve _ Digital _ Signature _ Algorithm](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)。