# 如何在 OpenBSD 6.3 上使用 httpd 运行 web 服务器

> 原文：<https://dev.to/mnlwldr/how-to-run-a-webserver-with-httpd-on-openbsd-63-20c2>

填充`/etc/httpd.conf`并用你的域名
替换*example.com*

```
server "example.com" {
  listen on * port 80
  root "/htdocs/example.com"
} 
```

Enter fullscreen mode Exit fullscreen mode

> 默认情况下，httpd 守护进程被设置为/var/www，因此`root "/htdocs/example.com"`表示`/var/www/htdocs/exmaple.com`。

我们应该创建我们的文档根目录

```
mkdir -p /var/www/htdocs/example.com 
```

Enter fullscreen mode Exit fullscreen mode

将带有占位符的`index.html`文件添加到新目录

```
echo "Hello World from OpenBSD 6.3">/var/www/htdocs/example.com/index.html 
```

Enter fullscreen mode Exit fullscreen mode

检查 httpd 配置

```
httpd -n 
```

Enter fullscreen mode Exit fullscreen mode

当一切正常时，启用并启动 httpd

```
rcctl enable httpd
rcctl start httpd 
```

Enter fullscreen mode Exit fullscreen mode

现在你应该用你的 IP 地址到达你的网站。