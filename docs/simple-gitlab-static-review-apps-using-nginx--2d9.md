# 使用 nginx 的简单静态 gitlab 审查应用

> 原文：<https://dev.to/themasch/simple-gitlab-static-review-apps-using-nginx--2d9>

## 动机

在为客户进行前端项目时，我总是希望能够向他们展示当前的状态，而不必一直部署到测试环境中。我希望能够有多个版本，每个分支一个，这样多人可以做不同的事情，但已经部署了全球可访问的版本，以显示并与同事或客户讨论。这也将改进合并请求，因为人们不仅可以检查代码，还可以在浏览器中检查已部署的项目，而不必签出代码。

这正是 GitLab 所说的[“回顾应用”](https://docs.gitlab.com/ee/ci/review_apps/)。它基本上是使用他们的 CI [环境](https://docs.gitlab.com/ee/ci/environments.html)特性，并在合并请求中显示它们。

## 实现

推荐的方法似乎是使用 docker & kubernetes，但是为一个小的集群获取资源并不便宜，并且仅仅为了对一小部分代码进行更好的代码审查而设置它并不是我想要做的事情。所以我用了一个更简单的方法。我得到了一个小虚拟机，用通配符 vhost `$name.review.example.com`设置了 nginx，并将其配置为服务来自`/var/www/$name/public`的文件。它不能执行后端代码或代理到其他后端，它只提供静态文件。

nginx 的配置非常简单:

```
server {
  listen 443 ssl http2;
  listen [::]:443 ssl http2;
  server_name ~^(?<name>.+?).review.exampe.com$;

  root /var/www/$name/public;

  # insert your TLS config here. https://mozilla.github.io/server-side-tls/ssl-config-generator/ is a great tool!

  location / {
    try_files $uri $uri/ /index.html;
  }

  access_log /var/log/nginx/pages-access.log;
  error_log /var/log/nginx/pages-error.log debug;
} 
```

Enter fullscreen mode Exit fullscreen mode

配置 gzip，一般 TLS 的东西，发送文件等。是在`nginx.conf`中全局完成的。

部署是通过运行在同一台机器上的 gitlab shell runner 进行的，它只是将代码同步到目标目录。GitLab 配置项脚本如下所示:

```
build_app:                                                                                                                                                                                                                                  
  stage: build                                                                                                                                                                                                                              
  image: node                                                                                                                                                                                                                               
  script:                                                                                                                                                                                                                                   
  - npm install                                                                                                                                                                                                                             
  - npm run build-release                                                                                                                                                                                                                   
  only:                                                                                                                                                                                                                                     
  - branches                                                                                                                                                                                                                                
  artifacts:                                                                                                                                                                                                                                
    paths:                                                                                                                                                                                                                                  
    - public/                                                                                                                                                                                                                               

deploy_review:                                                                                                                                                                                                                              
  stage: review                                                                                                                                                                                                                             
  script:                                                                                                                                                                                                                                   
  - mkdir -p "/var/www/$REVIEW_TAG/public"
  - rsync -drv --delete public/ "/var/www/$REVIEW_TAG/public/"
  environment:
    name: "review/$CI_BUILD_REF_NAME"
    url: https://$CI_PROJECT_ID-$CI_BUILD_REF_SLUG.review.example.com
    on_stop: stop_review
  dependencies:
  - build_app
  only:
  - branches
  tags:
  - review

stop_review:
  stage: review
  script:
  - rm -rf "/var/www/$REVIEW_TAG"
  variables:
    GIT_STRATEGY: none
  when: manual
  environment:
    name: "review/$CI_BUILD_REF_NAME"
    action: stop
  only:
  - branches
  tags:
  - review 
```

Enter fullscreen mode Exit fullscreen mode

stop_review 任务可以手动触发，或者在 MR 关闭和分支删除时由 gitlab 使用。它只是删除了 docroot。标签`review`用于为这些作业选择正确的流道。

### Authenti-什么？

这很有效。它在我们内部的 docker ci runner 上构建代码，将结果放入归档文件中，将归档文件下载到 review 服务器上，并将内容同步到文档根目录。对于大多数应用程序，deploy_review 作业大约需要 3-4 秒。从那里开始，通过`https://<project-ID>-<branch name>.review.example.com`公开提供公共文件夹。没有认证。这很容易，在某些情况下可能不是问题，但是当您在那里部署为客户开发的项目，甚至可能包含真实世界的测试数据时，肯定感觉不好。

所以需要某种认证。因为易用性是它的核心特性之一，所以将它隐藏在复杂的 SSO 系统后面并不是一个选项(这里目前也没有使用这样的系统)。HTTP basic auth 是最合适的选择，但是如果用户数量变大并且只允许认证，那么维护`.htpasswd`文件是很痛苦的。但是，当我们有了身份验证时，为什么还要止步于此呢？我们可以在混合中添加一些授权，这样我们就可以只允许特定的一组用户访问任何给定的项目。

### nginx 子请求救援

谢天谢地，有一个 ~~app~~ nginx 模块可以做到这一点。 [ngx_http_auth_request 模块](http://nginx.org/en/docs/http/ngx_http_auth_request_module.html)允许您请求第三方对用户进行认证。在服务器端，nginx 为第三方创建一个子请求，并检查响应状态代码。如果是 200，它将继续为客户端请求提供服务，如果是 401 或 403，它将通过 WWW-Authenticate 头向客户端返回相同的错误，以获得 401 结果。每隔一个状态代码被认为是一个错误(客户端的 HTTP 500)。

nginx 配置的附加内容如下所示。

```
server {
  # ...
  location / {
    auth_request /_remote_auth_/;
    try_files $uri $uri/ /index.html;
  }

  location = /_remote_auth/ {
    proxy_pass https://auth.example.com/remote_auth;  
    proxy_pass_request_body off;  
    proxy_set_header Content-Length "";  
    internal;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们向主位置上下文添加了`auth_request`指令，并添加了另一个`internal`位置上下文来将子请求代理到我们的认证后端。在这里使用代理允许我们对请求有更多的控制，例如删除请求体(如果有的话)和取消设置它的内容长度。

### 过度工程是我最大的优势

因此，nginx 端解决后，我们需要一个后端来回答我们的认证请求。我决定推出一个小的 Symfony 应用程序，它只在一个单一的路由上回答 HTTP 请求，并需要 HTTP basic auth，使用来自数据库的用户。这种设置允许未来的扩展，如根据允许的产品列表检查请求的域名或添加不同的存储后端，如 LDAP。

使用 Symfony 作为框架在 PHP 中构建这样的东西实际上可以很快完成，它涉及 PHP 中的大约 200LoC、大多数可以生成的东西(引导代码、db 迁移、orm 实体)和几十行配置。为了方便起见，我们使用 [EasyAdminBundle](https://symfony.com/doc/master/bundles/EasyAdminBundle/index.html) 为管理员创建和修改用户提供一个简单的用户界面。

我选择在与 review 应用相同的服务器上运行认证后端，因为它是目前唯一使用它的服务。部署很容易，因为我们已经有了 gitlab runner。

```
deploy_to_prod:  
  stage: deploy  
  script:  
  - rsync -drv --exclude '.env' --exclude '.*' --exclude 'vendor' --exclude 'var/' --delete . "/var/www/authcenter/"  
  - cd /var/www/authcenter/  
  - curl -O "https://getcomposer.org/download/1.7.2/composer.phar"  
  - php composer.phar install --no-dev  
  - php bin/console doctrine:migrations:migrate --no-interaction  
  variables:  
    APP_ENV: prod
    DATABASE_URL: $PROD_DATABASE_URL  
  environment:  
    name: authcenter prod  
    url: https://auth.example.com
  only:  
  - master  
  tags:  
  - nginx 
```

Enter fullscreen mode Exit fullscreen mode

这方面的 nginx 配置一点也不复杂。我只是使用了 symfony 提供的[模板，并对其进行了调整以适应我的`PHP-fpm`配置。我还使用了`fastcgi_param`指令来设置 Symfony 应用程序所需的`APP_ENV`和`DATABASE_URL`环境变量。](https://symfony.com/doc/current/setup/web_server_configuration.html#nginx)

我可以使用[https://auth.example.com/admin](https://auth.example.com/admin)登录应用程序，并获得 EasyAdmin 界面来修改或创建用户，或者使用[https://auth.example.com/remote_auth](https://auth.example.com/remote_auth)并获得一个空的 HTTP 200 响应。

当浏览我的一个评论应用程序时，我被要求在第一次请求时输入我的基本身份验证凭据，随后的每个请求都会收到相同的凭据，因为浏览器就是这样做的。

### 慢度的发现

这很有效，但是我很快意识到它很慢。实际上，对于“只为静态文件服务”的东西来说，速度非常慢。每个文件的请求时间长达 1 秒，有时甚至更长。这个数字应该远低于 100 毫秒！我去试着调试/分析 PHP 应用程序。我首先怀疑 opcache 会丢失。实际上，那个模块并没有安装。但这并没有解决我的问题。它更快，但不是很快。当我意识到这个巨大的“警告:前面有性能杀手”标志时，我已经准备好在操作系统层面上分析这个非常小的应用程序了。当前的实现工作如下。

*   用户的浏览器向服务器发送请求，在授权头中包含凭证。
*   nginx 启动 subrequest，将凭证交给我的 PHP 应用程序。
*   PHP 在数据库中查询用户名
*   PHP 使用 **bcrypt** 验证密码。
*   如果密码匹配，它用 HTTP 200 响应，如果不匹配，用 401 响应。
*   nginx 要么继续提供文件，要么用 401 响应。

除了 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 之外，这其中的每一步都非常便宜(db 查询大约需要 1ms)。它甚至被特别设计得很慢。动作慢是很辛苦的。这是一个好的密码散列/密钥导出函数的一般思想:你不能走捷径，你必须做困难的数学工作。虽然在登录期间运行 bcrypt 一次并在之后设置会话 cookie 根本不是问题，但在无状态的基本 auth 会话上为每个文件运行 bcrypt 实际上是一个糟糕的想法。

### 表现不好？有缓存！

虽然可能有办法让 nginx 为认证请求设置一个 cookie，但我选择了一个不需要太多配置的解决方案，它实际上解决了大多数工作负载的性能问题。Nginx 可以缓存代理响应。我所要做的就是允许在我的 PHP 应用程序中缓存响应，并在内部位置块中添加一个代理缓存。
PHP 代码如下:

```
 public function index() {
        $response = new JsonResponse([]);
        $response->setCache(['max_age' => 300, 'public' => true]); // 5min
        return $response;
    } 
```

Enter fullscreen mode Exit fullscreen mode

新的 nginx 配置如下:

```
location = /_remote_auth/ {  
  proxy_pass https://auth.example.com/remote_auth;  
  proxy_pass_request_body off;  
  proxy_set_header Content-Length "";  
  proxy_cache auth_cache;  
  proxy_cache_key $name$remote_user;  
  proxy_cache_valid 5m;  
  internal;  
} 
```

Enter fullscreen mode Exit fullscreen mode

这需要在`nginx.conf` :
中的 HTTP 上下文中配置代理区域

```
http {
  # ...
  proxy_cache_path /var/run/nginx keys_zone=auth_cache:10m;
} 
```

Enter fullscreen mode Exit fullscreen mode

`proxy_cache_key`确保我们缓存每个用户名&应用的一个响应长达五分钟。这使我们只需向实际的 auth 后端发出一次请求，就可以为该应用程序提供所有资源，但我们仍会对每个应用程序和每个用户检查一次身份验证。

## 结论

我目前对这个设置很满意。性能不是很好，但可以接受，我们得到了基本的认证，用户管理并不可怕，可以很容易地扩展这个系统。nginx 中有一个在后台自动重新验证过时缓存的选项，这可能是有用的，尽管缓存可能不是这个性能问题的最佳解决方案。

欢迎提出如何改进此设置的想法或替代建议！

写完所有我发现的(或者我猜是 *refound* )之后，GitLab 有一些关于这种设置的官方文档:【https://gitlab.com/gitlab-examples/review-apps-nginx[。](https://gitlab.com/gitlab-examples/review-apps-nginx)