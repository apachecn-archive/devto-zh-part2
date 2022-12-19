# 如何通过 acme 客户端启用 HTTPS

> 原文：<https://dev.to/mnlwldr/how-to-enable-https-with-acme-client-5a0c>

填充`/etc/acme-client.conf`并用你的域名
替换*example.com*

```
authority letsencrypt {
  api url "https://acme-v01.api.letsencrypt.org/directory"
  account key "/etc/acme/letsencrypt-privkey.pem"
}
authority letsencrypt-staging {
  api url "https://acme-staging.api.letsencrypt.org/directory"
  account key "/etc/acme/letsencrypt-staging-privkey.pem"
}
domain example.com {
  alternative names { example.com }
  domain key "/etc/ssl/private/example.com.key"
  domain certificate "/etc/ssl/example.com.crt"
  domain full chain certificate "/etc/ssl/example.com.pem"
  sign with letsencrypt
} 
```

Enter fullscreen mode Exit fullscreen mode

创建目录

```
mkdir -p -m 700 /etc/acme
mkdir -p -m 700 /etc/ssl/acme/private
mkdir -p -m 755 /var/www/acme 
```

Enter fullscreen mode Exit fullscreen mode

填充`/etc/httpd.conf`

```
server "example.com" {
  listen on * port 80
  root "/htdocs/example.com"
  location "/.well-known/acme-challenge/*" {
    root { "/acme", strip 2 }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

检查配置并重启 httpd

```
httpd -n 
```

Enter fullscreen mode Exit fullscreen mode

当一切正常时，重启 httpd

```
rcctl restart httpd 
```

Enter fullscreen mode Exit fullscreen mode

运行 acme 客户端

```
acme-client -vAD example.com 
```

Enter fullscreen mode Exit fullscreen mode

现在启用 HTTPS 并重启 httpd

填充`/etc/httpd.conf`并为 HTTPS 添加一个新的服务器部分

```
server "example.com" {
  listen on * tls port 443
  root "/htdocs/example.com"
  tls {
    certificate "/etc/ssl/example.com.pem"
    key "/etc/ssl/private/example.com.key"
  }
  location "/.well-known/acme-challenge/*" {
    root { "/acme", strip 2 }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

检查配置并重启 httpd

```
httpd -n 
```

Enter fullscreen mode Exit fullscreen mode

和

```
rcctl restart httpd 
```

Enter fullscreen mode Exit fullscreen mode

现在你应该通过 HTTPS 到达你的网站