# 如何设置支持 HTTP/2 的 Nginx

> 原文：<https://dev.to/grigorkh/how-to-set-up-nginx-with-http2-support-2mgb>

NGINX 是一个快速可靠的开源 web 服务器。由于其低内存占用、高可伸缩性、易于配置以及对绝大多数不同协议的支持，它受到了广泛的欢迎。

支持的协议之一是相对较新的 HTTP/2，于 2015 年 5 月发布。HTTP/2 的主要优势是其对于内容丰富的网站的高传输速度。

#### 先决条件

开始之前，我们需要一些东西:

*   Ubuntu 18.04(或 16.04/14.04)
*   SSL 证书。生成自签名证书，从 Let's Encrypt 获得免费证书，或从其他提供商购买证书。
*   仅此而已。如果你有上面列出的所有东西，你就可以开始了。

#### 步骤 1 —安装最新版本的 Nginx

在`Nginx 1.9.5`中引入了对`HTTP/2`协议的支持。幸运的是，Ubuntu 16.04 中的默认库包含比这个更高的版本，所以我们不必添加第三方库。

首先，更新 apt 打包系统中的可用包列表:

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

然后，安装 Nginx:

```
sudo apt-get install nginx 
```

Enter fullscreen mode Exit fullscreen mode

安装过程完成后，您可以通过键入以下命令来检查 Nginx 的版本:

```
sudo nginx -v 
```

Enter fullscreen mode Exit fullscreen mode

输出应该类似于以下内容:

```
nginx version: nginx/1.14.0 (Ubuntu) 
```

Enter fullscreen mode Exit fullscreen mode

#### 步骤 2 —更改监听端口并启用 HTTP/2

我们要做的第一个改变是将监听端口从`80`改为`443`。

让我们打开配置文件:

```
sudo nano /etc/nginx/sites-available/default 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Nginx 被设置为监听端口`80`，这是标准的`HTTP`端口:

```
listen 80; 
listen [::]:80; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们有两个不同的监听变量。第一个用于所有的`IPv4`连接。第二个用于`IPv6`连接。我们将为两者启用加密。

将 HTTPS 协议使用的监听端口修改为`443`:

```
listen 443 ssl http2; 
listen [::]:443 ssl http2; 
```

Enter fullscreen mode Exit fullscreen mode

注意，除了 ssl 之外，我们还在该行中添加了 http2。这个变量告诉 Nginx 在支持的浏览器上使用 HTTP/2。

#### 步骤 3 —更改服务器名称

我们使用`server_name`条目来指定哪个域应该与配置文件相关联。在配置文件中找到`server_name`条目。

默认情况下，server_name 设置为`_`(下划线)，这意味着配置文件负责所有传入的请求。将`_`更改为您的实际域，如下所示:

```
server_name example.com; 
```

Enter fullscreen mode Exit fullscreen mode

保存配置文件并编辑文本编辑器。

无论何时对 Nginx 配置文件进行更改，都应该检查配置中的语法错误，如下所示:

```
sudo nginx -t 
```

Enter fullscreen mode Exit fullscreen mode

如果语法没有错误，您将看到以下输出:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok nginx: configuration file /etc/nginx/nginx.conf test is successful 
```

Enter fullscreen mode Exit fullscreen mode

#### 步骤 4 —添加 SSL 证书

接下来，您需要配置 Nginx 来使用您的`SSL`证书。如果你不知道什么是`SSL`证书或者目前没有任何证书，请跟随这个[教程](https://dev.to/grigorkh/how-to-setup-lets-encrypt-for-nginx-ipv6a-sll-rating-3j0i)。

在 Nginx 配置目录中创建一个目录来存储您的`SSL`证书:

```
sudo mkdir /etc/nginx/ssl 
```

Enter fullscreen mode Exit fullscreen mode

将您的证书和私钥复制到此位置。我们还将重命名文件，以显示它们与哪个域相关联。当您有多个域与该服务器相关联时，这在将来会很方便。用您的实际主机名替换 example.com:

```
sudo cp /path/to/your/certificate.crt /etc/nginx/ssl/example.com.crt sudo cp /path/to/your/private.key /etc/nginx/ssl/example.com.key 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们再次打开配置文件 1 并配置 SSL。

```
sudo nano /etc/nginx/sites-available/default 
```

Enter fullscreen mode Exit fullscreen mode

在服务器块内的新行上，定义证书的位置:

```
ssl_certificate /etc/nginx/ssl/example.com.crt;   
ssl_certificate_key /etc/nginx/ssl/example.com.key; 
```

Enter fullscreen mode Exit fullscreen mode

保存文件，并退出文本编辑器。

#### 第 5 步—避免旧的密码套件

有一个庞大的旧的和不安全的密码黑名单，所以我们必须避免他们。密码套件是一组密码算法，描述了应该如何加密传输的数据。

我们将使用真正流行的密码集，其安全性得到了 CloudFlare 等互联网巨头的认可。它不允许使用 MD5 加密(MD5 加密自 1996 年以来就被认为是不安全的，但尽管如此，直到今天它的使用仍然很广泛)。

打开以下配置文件:

```
sudo nano /etc/nginx/nginx.conf 
```

Enter fullscreen mode Exit fullscreen mode

在`ssl_prefer_server_ciphers on;`后加上这一行。

```
ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5; 
```

Enter fullscreen mode Exit fullscreen mode

保存文件，并退出文本编辑器。

再次检查配置中的语法错误:

```
sudo nginx -t 
```

Enter fullscreen mode Exit fullscreen mode

#### 步骤 6 —将所有 HTTP 请求重定向到 HTTPS

因为我们只对通过 HTTPS 提供内容感兴趣，所以我们应该告诉 Nginx，如果服务器收到 HTTP 请求，它应该做什么。

在我们文件的底部，我们将创建一个新的服务器块，用于将所有 HTTP 请求重定向到 HTTPS(确保用您的实际域名替换服务器名):

```
server {  
listen         80;  
listen    [::]:80;  
server_name    example.com;  
return         301 ;  
} 
```

Enter fullscreen mode Exit fullscreen mode

保存文件，并退出配置文件。

检查配置中的语法错误:

```
sudo nginx -t 
```

Enter fullscreen mode Exit fullscreen mode

#### 第 7 步—重新加载 Nginx

这就是所有 Nginx 配置的变化。因为我们检查了每个更改的语法错误，所以您应该准备好重启 Nginx 并测试您的更改。

总的来说，忽略注释掉的行，您的配置文件现在应该如下所示:

```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name example.com;
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;
    ssl_dhparam /etc/nginx/ssl/dhparam.pem;
}
server {
       listen         80;
       listen    [::]:80;
       server_name    example.com;
       return         301 https://$server_name$request_uri;
} 
```

Enter fullscreen mode Exit fullscreen mode

要应用更改，请重启 Nginx 服务器。

```
sudo systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

### 
 [T3】
结论](#conclusion) 

您的 Nginx 服务器现在提供 HTTP/2 页面服务。如果您想测试您的 SSL 连接的强度，请访问 [Qualys SSL](https://www.ssllabs.com/ssltest/) 实验室并对您的服务器进行测试。如果一切都配置正确，您应该会获得安全 A+标记。