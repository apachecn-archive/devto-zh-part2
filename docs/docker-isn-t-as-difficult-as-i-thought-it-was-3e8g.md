# 码头工人没有我想象的那么难

> 原文：<https://dev.to/roelofjanelsinga/docker-isn-t-as-difficult-as-i-thought-it-was-3e8g>

[!["Containers"](img/3fb705639b4040c19acd22131c559c3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fooS6lne--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d46j2p840qg6ofi8zoxb.jpeg)

# 码头工人没有我想象的那么难

当我在我的项目中使用 Docker 时，我被“Dockerfile”吓到了。直到我意识到我已经在 Docker 之外做了一年类似的事情。

大约一年前，我写了一个完整的安装脚本，需要在任何新的服务器上运行，以便为特定的项目安装任何必要的软件。我认为这是世界上最好的事情，因为只需一个命令，我就可以安装整个应用程序及其所有的依赖项。

所以我一直觉得 Docker 很难理解，这让我很困惑。它与完整安装脚本的概念完全相同，但不是在主机操作系统上安装任何软件，而是将其安装在一个包含的环境中。所以当我明白这一点时，我为我的项目构建了一个单一的 docker 文件，包含我开始工作所需的一切。然后我心想:“Docker 是作为容器服务使用的，我为什么要使用单个大型容器呢？”。感觉好像我没有像预期的那样使用软件。这是我偶然发现 docker-compose 的时候。

Docker compose 通过一个 docker-compose.yml 文件为您管理多个不同的容器。现在我终于觉得我充分利用了不同的容器。

这里可以找到一个这样的例子:

```
version: "2"
services:
  nginx:
      build:
          context: ./nginx
      ports:
          - "8080:80"
      volumes:
          - ../:/var/app
  fpm:
      build:
          context: ./fpm
      volumes:
          - ../:/var/app
      expose:
          - "9000"
  redis:
      image: redis
      expose:
          - "6379"
  solr:
      image: solr:7.2.1-alpine
      expose:
          - "6379"
      volumes:
          - ./solr/search_core:/opt/solr/server/solr/search_core 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未使用过 Docker 或 docker-compose，这个文件看起来有点奇怪，但它实际上非常简单。版本只是简单地标记了你想使用哪个版本的 Docker。服务块是有趣的地方，因为这是定义不同容器的地方。如你所见，我有四个不同的容器。

第一项服务是 nginx，因为你需要某种网络服务器，相比 Apache，我更喜欢 Nginx。Nginx 要求您将任何内容重定向到 PHP，这与 Apache 不同。这就是为什么我还定义了一个 PHP 容器。这里的“上下文”参数仅仅意味着我想要做的任何配置都位于 docker 文件的给定位置。在这个 Dockerfile 文件中，我已经定义了容器应该运行什么软件。

这是 Nginx 服务的 docker 文件的一个例子:

```
FROM nginx
ADD ./default.conf /etc/nginx/conf.d/
RUN echo "daemon off;" >> /etc/nginx/nginx.conf
CMD service nginx start 
```

Enter fullscreen mode Exit fullscreen mode

所有这些 does 文件都定制了默认的 Nginx web 服务器配置。然后它告诉 Nginx 不要在守护模式下运行(因为 docker 镜像会马上停止工作)。CMD 指令只是启动 Nginx 服务。应用于 Nginx 容器的配置可以在这里找到:

```
server {
    listen 80 default_server;
    root /var/app/public;
    index index.php index.html;
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_disable "msie6";
    gzip_comp_level 6;
    gzip_buffers     4 4k;
    gzip_types text/css application/javascript text/javascript text/plain text/xml application/json application/x-font-opentype application/x-font-truetype application/x-font-ttf application/xml font/eot font/opentype font/otf image/svg+xml;
    gzip_min_length 1000;
    rewrite_log on;
    # serve static files directly
    location ~* \.(jpg|jpeg|gif|css|png|js|ico|html)$ {
        access_log off;
        expires max;
        log_not_found off;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location ~* \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass fpm:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    location ~ /\.ht {
        deny all;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个配置确保 web 服务器正确地为 PHP 文件提供服务，并为静态文件添加一些缓存头。所有请求都通过“fastcgi _ pass fpm:9000；”转发给 PHP 容器。显然，这个 Nginx 安装不是为 SSL 设置的，但是出于开发的目的，这还没有被认为是必要的。

第三个服务是 Redis，但是正如您所看到的，我在这里定义了一个“image”参数。这意味着我正在使用一个预构建的 Docker 映像，并且不希望做任何调整(至少在这种情况下)。我简单地公开了端口 6379，以便能够从我的主机操作系统监视服务。在生产环境中不建议这样做，因为现在外部将能够访问它。Docker 提供了指向这个端口的内部指针，所以您将能够在其他容器中使用它，而无需将其暴露给外部。

第四个服务也是一个预构建的 Docker 映像，但这次我通过“volumes”附加了一个主机卷。这意味着我允许 Docker 容器与我的主机操作系统上的一个或多个文件夹进行交互。通过这种方式，我可以使用容器中我自己硬盘上的信息。

Docker 和 docker-compose 使得与同事一起处理相同的代码变得非常简单，因为所有的代码都运行在完全相同的环境中。不管他们使用的是 Mac OSX、Windows 还是 Linux，应用环境都是一样的。

所以，如果你还没有尝试过 Docker，或者你被它吓倒了，那就试试吧，不要放弃。当你让它工作时，向你的应用程序添加功能将是一个非常简单的体验。如果你有任何关于我如何改进我的例子的建议，请告诉我！我喜欢从你身上学到更多！