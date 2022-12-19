# 运行在 Docker Compose 上的 Symfony 4

> 原文：<https://dev.to/_mertsimsek/symfony-4-running-on-docker-compose-2fb3>

我正在开发一个应用程序，以提高词汇量。你将在这个应用程序中输入你所学的单词。然后，这个应用随机问你。为此，我需要建立工作区。Docker Compose 上的工作区将变成 Symfony 4。它依赖 Nginx，Php-Fpm 和 Mysql 容器。

首先我会调整 **docker-compose.yml** 文件。Mysql 简单纯粹。Nginx 和 Php-fpm 对他们的文件很感兴趣。项目结构是这样的。

[![alt text](img/db1b9f307598076ce0566da2402150d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--owaOngGH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mh3nw02inh5t7p5e8z39.png)

下面几行表示 **docker.compose.yml** 文件。

```
version: '2'
services:
    php:
        build: ./php-fpm
        volumes:
            - ./individual-vocabulary:/var/www/individual-vocabulary
            - ./php-fpm/php.ini:/usr/local/etc/php/php.ini
            - ./assets/vocabulary-gc-project.json:/var/www/vocabulary-gc-project.json
        environment:
            GOOGLE_APPLICATION_CREDENTIALS: /var/www/vocabulary-gc-project.json
        depends_on:
            - mysql
    web:
        image: nginx:latest
        ports:
            - "8888:80"
        volumes:
            - ./individual-vocabulary:/var/www/individual-vocabulary
            - ./nginx/individual-vocabulary.conf:/etc/nginx/conf.d/default.conf
        depends_on:
            - php
    mysql:
        image: mysql:5.6
        environment:
            MYSQL_ROOT_PASSWORD: symf0ny
        ports:
            - "3333:3306" 
```

Enter fullscreen mode Exit fullscreen mode

我需要一个 Nginx 配置文件。我定义如下。

```
server {
    listen 80;
    server_name web;
    root /var/www/individual-vocabulary/public;

    location / {
        # try to serve file directly, fallback to index.php
        try_files $uri /index.php$is_args$args;
    }

    location ~ ^/index\.php(/|$) {
        #fastcgi_pass unix:/var/run/php7.2-fpm.sock;
        fastcgi_pass php:9000;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 4 256k;
        fastcgi_busy_buffers_size 256k;
        internal;
    }

    location ~ \.php$ {
        return 404;
    }

    error_log /var/log/nginx/project_error.log;
    access_log /var/log/nginx/project_access.log;
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，现在我们必须为 Php-fpm 配置 **Dockerfile** 。

```
FROM php:7.2-fpm-alpine

RUN apk update \
    && apk add  --no-cache git mysql-client curl libmcrypt libmcrypt-dev openssh-client icu-dev \
    libxml2-dev freetype-dev libpng-dev libjpeg-turbo-dev g++ make autoconf \
    && docker-php-source extract \
    && pecl install xdebug redis \
    && docker-php-ext-enable xdebug redis \
    && docker-php-source delete \
    && docker-php-ext-install pdo_mysql soap intl zip \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_port=10000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    #&& curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && rm -rf /tmp/*

CMD ["php-fpm", "-F"]

WORKDIR /var/www/individual-vocabulary

EXPOSE 9000 
```

Enter fullscreen mode Exit fullscreen mode

之后，我需要一个 Php.ini 文件。对此，我这样定义。

```
[PHP]
engine = On
short_open_tag = On
precision = 14
output_buffering = Off
zlib.output_compression = Off
implicit_flush = Off
unserialize_callback_func =
serialize_precision = 100
disable_functions = dl
disable_classes =
zend.enable_gc = On
expose_php = Off
max_execution_time = 30
max_input_time = 60
memory_limit = 256M
error_reporting = E_ALL & ~E_NOTICE
display_errors = On
display_startup_errors = Off
log_errors = On
log_errors_max_len = 1024
ignore_repeated_errors = Off
ignore_repeated_source = Off
report_memleaks = On
track_errors = Off
html_errors = On
error_log = error_l
variables_order = "EGPCS"
request_order = "GP"
register_argc_argv = On
auto_globals_jit = On
post_max_size = 64M
auto_prepend_file =
auto_append_file =
default_mimetype = "text/html"
default_charset = "UTF-8"
include_path = ".:/opt/php72/lib/php"
doc_root =
user_dir =
enable_dl = Off
file_uploads = On
upload_max_filesize = 64M
max_file_uploads = 20
allow_url_fopen = On
allow_url_include = On
default_socket_timeout 
cli_server.color 
date.timezone = Asia/Riyadh
pdo_mysql.cache_size = 2000
pdo_mysql.default_soc
sendmail_path = /usr/sbin/sendmail -t 
mail.add_x_header 
sql.safe_mode =
odbc.allow_persistent = Off
odbc.check_persistent = Off
odbc.max_persistent = -1
odbc.max_links = -1
odbc.defaultlrl = 4096
odbc.defaultbinmode
ibase.allow_persistent = 1
ibase.max_persistent = -1
ibase.max_links = -1
ibase.timestampformat = "%Y-%m-%d %H:%M:%S"
ibase.dateformat = "%Y-%m-%d"
ibase.timeformat = "%H:%M"
mysqli.max_persistent = -1
mysqli.allow_persistent = Off
mysqli.max_links = -1
mysqli.cache_size = 2000
mysqli.default_port = 3306
mysqli.default_socket =
mysqli.default_host =
mysqli.default_user =
mysqli.default_pw =
mysqli.reconnect =
mysqlnd.collect_statistics = Off
mysqlnd.collect_memory_statistics =
pgsql.allow_persistent = Off
pgsql.auto_reset_persistent = Off
pgsql.max_persistent = -1
pgsql.max_links = -1
pgsql.ignore_notice = 0
pgsql.log_notice
bcmath.scale
session.save_handler = files
session.save_path = "/tmp"
session.use_strict_mode = 0
session.use_cookies = 1
session.use_only_cookies = 1
session.name = PHPSESSID
session.auto_start = 0
session.cookie_lifetime = 0
session.cookie_path = /
session.cookie_domain =
session.cookie_httponly =
session.serialize_handler = php
session.gc_probability = 1
session.gc_divisor = 100
session.gc_maxlifetime = 1440
session.referer_check =
session.cache_limiter = nocache
session.cache_expire = 180
session.use_trans_sid = 0
session.hash_function = 0
session.hash_bits_per_character = 5
url_rewriter.tags = "a=href,area=href,frame=src,input=src,form=,fields"
zend.assertions 
tidy.clean_output =
soap.wsdl_cache_enabled=1
soap.wsdl_cache_dir="/tmp"
soap.wsdl_cache_ttl=86400
soap.wsdl_cache_limit
ldap.max_links 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，我可以安装 Symfony 4 了。我将项目目录中的这个命令作为文档运行。

```
composer create-project symfony/website-skeleton individual-vocabulary 
```

Enter fullscreen mode Exit fullscreen mode

安装后，你会看到**。应用程序中的 env** 文件。我已经将`DATABASE_URL`环境变量更改如下。

```
APP_ENV=dev
APP_SECRET=9258a6c0e5c19d0d58a8c48bbc757491
DATABASE_URL=mysql://root:symf0ny@mysql:3306/individual-vocabulary 
```

Enter fullscreen mode Exit fullscreen mode

我为用户定义了`root`，为密码定义了`symf0ny`，为主机定义了`mysql`。这个主机名来自于 **docker-compose.yml** 文件。正如您所记得的，我们已经在这个文件中为 mysql 服务定义了`mysql`名称。

现在，我们可以运行这个命令。所以，所有的图像都变成了一个容器。

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，`:8888`端口正在监听 http 请求。你打开`http://localhost:8888`，它就回答你。

[![alt text](img/6f1b197622406ed052521db2cf74c0ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ld9Dqn21--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s9uavfz9dovlhj73s659.png)