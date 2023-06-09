# Debian 上鸽巢的 Argon2

> 原文：<https://dev.to/c33s/argon2-for-dovecot-on-debian-2ap7>

## TL；速度三角形定位法(dead reckoning)

```
echo "deb https://packages.sury.org/php/ stretch main" > /etc/apt/sources.list.d/sury.list
apt-get update
apt-get install libsodium-dev dovecot-dev build-essential libtool-bin
git clone https://github.com/LuckyFellow/dovecot-libsodium-plugin
cd dovecot-libsodium-plugin
./autogen.sh
./configure --with-dovecot=/usr/lib/dovecot --with-moduledir=/usr/lib/dovecot/modules
make
make install libtool --finish /usr/local/lib/dovecot/auth 
```

Enter fullscreen mode Exit fullscreen mode

## 详细信息

dovecot wiki 告诉我们，Argon2 是目前最安全的哈希算法。debian stretch 上的 dovecot 没有捆绑 argon2 算法。

```
root@mail:~# doveadm pw -l
MD5 MD5-CRYPT SHA SHA1 SHA256 SHA512 SMD5 SSHA SSHA256 SSHA512 PLAIN CLEAR CLEARTEXT PLAIN-TRUNC CRAM-MD5 SCRAM-SHA-1 HMAC-MD5 DIGEST-MD5 PLAIN-MD4 PLAIN-MD5 LDAP-MD5 LANMAN NTLM OTP SKEY RPA PBKDF2 CRYPT SHA256-CRYPT SHA512-CRYPT 
```

Enter fullscreen mode Exit fullscreen mode

为了能够使用`doveadm pw -s argon2`，你需要安装[dovecot-lib 钠插件](https://github.com/LuckyFellow/dovecot-libsodium-plugin)。意味着自己构建(但只是扩展)。

*   配置`packages.sury.org/php` als apt 存储库
*   安装所需的软件包
*   克隆[dovecot-lib 钠插件](https://github.com/LuckyFellow/dovecot-libsodium-plugin) repo
*   构建(您必须在 debian 上指定 dovecot 和模块路径)

不确定 sury repo，只注意到，在我安装了`php-sodium`扩展之后，`libsodium23`和`libsodium-dev`从那里安装

```
Get:1 https://packages.sury.org/php stretch/main amd64 libsodium23 amd64 1.0.16-2+0~20180103211631.7+stretch~1.gbpb56693 [153 kB]
Get:2 https://packages.sury.org/php stretch/main amd64 libsodium-dev amd64 1.0.16-2+0~20180103211631.7+stretch~1.gbpb56693 [171 kB] 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，您应该会看到:

```
root@mail:~/dovecot-libsodium-plugin# doveadm pw -l
MD5 MD5-CRYPT SHA SHA1 SHA256 SHA512 SMD5 SSHA SSHA256 SSHA512 PLAIN CLEAR CLEARTEXT PLAIN-TRUNC CRAM-MD5 SCRAM-SHA-1 HMAC-MD5 DIGEST-MD5 PLAIN-MD4 PLAIN-MD5 LDAP-MD5 LANMAN NTLM OTP SKEY RPA PBKDF2 CRYPT SHA256-CRYPT SHA512-CRYPT SCRYPT ARGON2 
```

Enter fullscreen mode Exit fullscreen mode

*   阅后编辑增强[https://github . com/lucky fellow/dovecot-lib 钠-plugin/issues/6 # issue comment-350849536](https://github.com/LuckyFellow/dovecot-libsodium-plugin/issues/6#issuecomment-350849536)
*   封面图片由 pix abay[https://www . pexels . com/photo/door-handle-key-keyhole-279810/](https://www.pexels.com/photo/door-handle-key-keyhole-279810/)