# 让我们加密 Certbot/acme.sh 和“300 多项选择”错误

> 原文：<https://dev.to/c33s/lets-encrypt-certbotacmesh-and-the-300-multiple-choices-error-3e4i>

如果你想使用 letsencrypt，但即使你有这样的绝对最低配置也不能让它工作:

```
server {
  listen *:80;

  server_name           example.com;
  root /var/www/example.com;
} 
```

Enter fullscreen mode Exit fullscreen mode

有可能你的 DNS `A-Record`指向了与`AAAA-Record`不同的位置。如果您不能直接访问 DNS 接口，并且只更改了`A-Record`，这很容易发生。

对于 debian9(拉伸),从背面端口安装 certbot。shiped 版本相当过时，可以导致 [`Client with the currently selected authenticator does not support any combination of challenges that will satisfy the CA.`](https://community.letsencrypt.org/t/solution-client-with-the-currently-selected-authenticator-does-not-support-any-combination-of-challenges-that-will-satisfy-the-ca/49983)

[`cerbot`](https://certbot.eff.org/) 想到了，对我来说，没那么有用的错误信息 [`300 Multiple Choices`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/300) 和 [`acme.sh`](https://github.com/Neilpang/acme.sh) 日志文件也只包含了一个`code='400'`

`certbot`错误:

```
 - The following errors were reported by the server:

   Domain: example.com
   Type:   unauthorized
   Detail: Invalid response from
   http://example.com/.well-known/acme-challenge/TOKEN_REMOVED:
   "<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
   <html><head>
   300 Multiple Choices
   </head><body>
   <h1>Multiple C" 
```

Enter fullscreen mode Exit fullscreen mode

`acme.sh`错误:

```
[Mon May 28 17:37:26 CEST 2018] url='https://acme-staging.api.letsencrypt.org/acme/challenge/TOKEN_REMOVED/ID_REMOVED'
[Mon May 28 17:37:26 CEST 2018] payload='{"resource": "challenge", "keyAuthorization": "KEY_AUTH_REMOVED"}'
[Mon May 28 17:37:26 CEST 2018] POST
[Mon May 28 17:37:26 CEST 2018] _post_url='https://acme-staging.api.letsencrypt.org/acme/challenge/TOKEN_REMOVED/ID_REMOVED'
[Mon May 28 17:37:26 CEST 2018] _CURL='curl -L --silent --dump-header /root/.acme.sh/http.header  -g '
[Mon May 28 17:37:27 CEST 2018] _ret='0'
[Mon May 28 17:37:27 CEST 2018] code='400' 
```

Enter fullscreen mode Exit fullscreen mode

*   照片由 Mikes Photos[https://www . pexels . com/photo/arrow-close-up-direction-environment-445012/](https://www.pexels.com/photo/arrow-close-up-direction-environment-445012/)