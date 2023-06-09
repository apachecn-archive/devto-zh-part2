# NGINX 反向代理

> 原文：<https://dev.to/remyg/nginx-reverse-proxy-54d7>

我的家庭服务器设置由几个 Raspberry Pi 组成，我在其中托管不同的 web 应用程序(这个博客、一个 RSS 阅读器、一些家庭 IOT 应用程序……)。我决定设置一个前端网关，将请求代理到正确的服务器:

[![Infrastructure](img/cdcb6e78aa4a6fc779ea4426fe97dd07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e5RXtRIx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://remyg.ovh/assets/img/reverse-proxy.png)

这些请求由 NGINX 反向代理代理，运行在网关上的 Docker 容器中。它根据主机重定向 HTTP 请求(例如，`remyg.ovh`在`rpi1`上运行，而`rss.remyg.ovh`在 rpi2 上运行)。

## NGINX 配置

主 NGINX 配置文件(`nginx.conf`)如下所示:

```
user nginx;
worker_processes 1;

error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    #tcp_nopush on;

    keepalive_timeout 65;

    #gzip on;

    include /etc/nginx/sites-enabled/*.*;
} 
```

与基本配置文件(来自默认 NGINX Docker 映像)的唯一区别是最后一行:

```
include /etc/nginx/conf.d/*.conf; 
```

被替换为

```
include /etc/nginx/sites-enabled/*.*; 
```

它忽略默认配置(`/etc/nginx/conf.d/default.conf`)并使用我定义的代理配置文件。

## 主机配置

每台主机都有自己的配置文件:

*   对于运行在 rpi1 上的 **remyg.ovh** (本地 IP 为 192.168.0.10，端口为 8080):

```
server {
    listen 80;
    server_name remyg.ovh;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    location / {
        proxy_pass http://192.168.0.10:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
} 
```

*   对于 **rss.remyg.ovh** ，运行在 rpi2 上(本地 IP 192.168.0.11，端口 8081):

```
server {
    listen 80;
    server_name rss.remyg.ovh;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    location / {
        proxy_pass http://192.168.0.11:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
} 
```

这些文件表明进入 rss.remyg.ovh:80 ( `server_name`和`listen`)的请求将被重定向到 192.168.0.11:8081 ( `proxy_pass`)。

这就是在 HTTP 上为网站提供服务所需的所有配置。

## 在容器中运行

要在 Docker 容器中运行反向代理，文件树如下所示:

```
nginx-reverse-proxy
  -> conf
    -> nginx.conf
  -> sites
    -> remyg.ovh
       rss.remyg.ovh 
```

使用这种结构，启动容器的命令将是:

```
docker run --name mynginx-proxy \
-v /home/pi/nginx-reverse-proxy/sites:/etc/nginx/sites-enabled:ro \
-v /home/pi/nginx-reverse-proxy/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
-p 80:80 -d nginx:alpine 
```

## HTTPS

为了在不同的网站上启用 HTTPS，我使用了 Let's Encrypt 和他们的实用程序 Certbot。

我从安装`certbot`包开始:

```
sudo apt install certbot 
```

当生成证书时，Certbot 需要验证它可以访问它生成的特定文件，指向 URL `http://your-host/.well-known/acme-challenge/{token}`。为此，首先在反向代理容器上创建并装载一个新卷:

```
docker run --name mynginx-proxy \
        -v /home/pi/nginx-reverse-proxy/sites:/etc/nginx/sites-enabled:ro \
        -v /home/pi/nginx-reverse-proxy/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
        -v /home/pi/letsencrypt_www:/var/www/letsencrypt \
        -p 80:80 -p 443:443 -d nginx:alpine 
```

然后在站点代理配置中指定在指向`/.well-known/acme-challenge/`时使用该卷:

```
server {
    listen 80;
    server_name remyg.ovh;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    location /.well-known/acme-challenge/ {
        root /var/www/letsencrypt;
    }

    location / {
        proxy_pass http://192.168.0.10:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
} 
```

并重新加载您的 NGINX 配置:

```
docker exec -it mynginx-proxy nginx -s reload 
```

现在您可以生成证书了:

```
sudo certbot certonly --authenticator webroot -w /home/pi/letsencrypt_www -d remyg.ovh -d rss.remyg.ovh 
```

这将在`/home/pi/letsencrypt_www`中生成 ACME 质询文件，并验证质询。它还将在`/etc/letsencrypt/certs/live/remyg.ovh/`和`/etc/letsencrypt/certs/live/rss.remyg.ovh/`中生成证书。

最后一步是使用新证书，并且只允许 HTTPS 请求。

首先装入一个包含证书的新卷:

```
docker run --name mynginx-proxy \
        -v /home/pi/nginx-reverse-proxy/sites:/etc/nginx/sites-enabled:ro \
        -v /home/pi/nginx-reverse-proxy/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
        -v /etc/letsencrypt:/etc/nginx/certs \
        -v /home/pi/letsencrypt_www:/var/www/letsencrypt \
        -p 80:80 -p 443:443 -d nginx:alpine 
```

然后更新您的代理配置:

```
server {
    listen 80;
    server_name remyg.ovh;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    location /.well-known/acme-challenge/ {
        root /var/www/letsencrypt;
    }

    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    server_name remyg.ovh;

    ssl_certificate certs/live/remyg.ovh/fullchain.pem;
    ssl_certificate_key certs/live/remyg.ovh/privkey.pem;

    location / {
        proxy_pass http://192.168.0.10:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
} 
```

重新加载 NGINX 配置，就万事俱备了！