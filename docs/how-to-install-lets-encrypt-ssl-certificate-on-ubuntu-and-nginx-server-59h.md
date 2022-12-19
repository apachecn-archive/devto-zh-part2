# 如何在 Ubuntu 和 Nginx 服务器上安装 Let's Encrypt SSL 证书

> 原文：<https://dev.to/murindwaz/how-to-install-lets-encrypt-ssl-certificate-on-ubuntu-and-nginx-server-59h>

## T2】

> 这篇文章出现在我的[博客](https://blog.hoo.gy/2018/03/13/how-to-install-lets-encrypt-ssl-certificate-on-ubuntu-and-nginx-server/)和[媒体](https://medium.com/hoopeez/how-to-install-lets-encrypt-ssl-certificate-on-ubuntu-and-nginx-server-ffa363b94723)上。与你的朋友分享；-)

相对于其他大多数 SSL 证书颁发者， *[让我们加密](https://letsencrypt.org/getting-started/)* 不仅可以免费使用，而且易于安装和更新。这篇文章重点介绍了我在 [Hoo.gy](https://app.hoo.gy) 和 [Hoo.gy 博客](https://blog.hoo.gy)上安装 mine 的步骤。

写这篇文章的动机有三个。首先，本文作为以后需要的参考，用 *[我们来加密](https://letsencrypt.org/getting-started/)* 。第二，与开发者社区分享经验是另一种学习形式。第三，作为对[团队民主化](https://letsencrypt.org/isrg/)安全基础设施这一核心部分的感谢。

本文分为 4 个主要部分:

*   安装必要的软件
*   生成密钥和证书
*   在 Nginx 上安装证书
*   强制 HTTS 重定向
*   证书自动续订

> 鸣谢:感谢我的朋友兼安全呆子 *[@jc_uwimpuhwe](https://twitter.com/jc_uwimpuhwe)* 的校对和想法充实。

**安装必要的软件**

**[Hoo.gy](https://app.hoo.gy)** 运行在位于纽约市[数字海洋](https://www.digitalocean.com/)数据中心的 **Ubuntu 14 LTS** Linux 盒子上。NodeJS web 服务器与 **Nginx** 耦合。从这个角度来看，我假设您的系统运行类似的堆栈。

证书的颁发是通过一个机器人来完成的，它覆盖了各种各样的操作系统和网络服务器。首先，第一步是更新和安装最新的软件包，同时确保 Ubuntu 包含一个新的软件包源。

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-nginx 
```

Enter fullscreen mode Exit fullscreen mode

第二步在盒子上安装 [certbot](https://certbot.eff.org/#ubuntutrusty-nginx) 。以下命令适用于 Nginx 服务器，但更多命令可以在 eff.org 的[找到。](https://certbot.eff.org/)

有两种可能的模式来生成 SSL 证书。这将是下一节的主题。

**生成密钥和证书**

默认模式是为普通 Linux 用户设计的。在您运行下一个命令之后，一切都搞定了。Certbot 生成正确的密钥+证书，并自动更新 Nginx 配置文件。

```
 $ sudo certbot --nginx 
```

Enter fullscreen mode Exit fullscreen mode

对于更高级的用户来说，他们更喜欢 key+certificate，并按照自己喜欢的方式安装证书，这个命令将帮助他们:

```
 $ sudo certbot --nginx certonly 
```

Enter fullscreen mode Exit fullscreen mode

生成私钥和证书后，另外，您还需要*安装证书*，*重新执行 HTTPS 重定向*，以及*自动更新证书*。

**在 Nginx 上安装证书**

因为您已经在生产中运行 Nginx，所以您可能不希望任何东西干扰您的定制配置。第二个命令就是这样给你的。有两种方法可以安装证书:第一种是保持你的配置不变，并 **symlink** 新证书。第二种方法显然是将配置链接更改到一个新的位置。我更喜欢后者。

```
# in /etc/nginx/sites-enabled/[your-config-file]
server{
  ...  
  listen 443 ssl;
  server_name example.com;
  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;  
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

因为一些原因，我习惯了用 **chained.crt** 做证书，用 **key.crt** 做密钥。不过，这种转变并没有那么难。Certbot 生成相同的文件，只是名称略有不同:密钥使用 **privatekey.pem** ，证书使用 **fullchain.pem** 。

**强制 HTTPS 重定向**

关于这个话题有很多讨论。软件开发人员是最不适合讨论的人。我们总是以部落主义告终，谁做得更好。对我来说有意义的一种方法是在现有的配置文件中创建一个新的服务器块，并将任何请求重定向到这个新的服务器块，这个服务器块只监听 HTTPS。这个新块看起来像下面这样:

```
server{
 listen 80;
 server_name example.com www.example.com;

 # redirect, and rewrite all links to https://example.com 
 return 301 https://example.com$request_uri;

 # Alternatively: if you want to forward without rewriting requested URL 
 return 301 https://$server_name$request_uri;
} 
```

Enter fullscreen mode Exit fullscreen mode

nginx 配置的定制本身可能是一个全新的话题，但是我不能在结束这篇文章之前不谈论两件事:自动更新(使用 cron 作业)和将所有流量重定向到安全通道。

**自动续费**

SSL 证书由 ***颁发让我们加密*** 持续 90 天。这本身并不是一件坏事。但是，如果您有 1000 多台服务器需要每 90 天更新一次...那将是一场噩梦。你需要某种自动化。如何做到这一点，是这篇博文的最后一个话题。

首先，从“让我们加密”更新证书需要一秒钟...如果一切按计划进行。

```
$ sudo certbot renew --dry-run
# or simply:
# $ sudo certbot renew
# restart the nginx server
$ sudo nginx restart 
```

Enter fullscreen mode Exit fullscreen mode

如果您幸运地拥有一个较小的配置并使用了自动更新策略，您可能会有一个类似如下的 cronjob】

```
# file: /etc/cron.d/certbot
0 */12 * * * root test -x /usr/bin/certbot -a \! -d /run/systemd/system && perl -e 'sleep int(rand(3600))' && certbot -q renew 
```

Enter fullscreen mode Exit fullscreen mode

*来自[Ishigoya ~ server fault](https://serverfault.com/a/879711)T3 的代码片段*

可以定制频率和重启命令。cronjob 为您提供了一种使用星形格式校准日期的方法:cron job[分钟，小时，一个月中的某一天，一个月中的某一天]。

*   这个 cron 每天大约在 5:30 运行服务器时间:_30 5 * * *..._
*   此服务每两个月(1 日和 15 日)在同一时间运行 *30 5 1，15 * *...*
*   最后一个运行时间相同，每两个月: *30 5 * */2 **

因此，如果您希望每天在服务器时间 5:30 左右更新证书，那么您的 cronjob 可能会如下所示。

```
30 5 * * * certbot renew --post-hook "service nginx restart"
# alternatively: using systemctrl 
# 30 5 * * * certbot renew --post-hook "systemctl reload nginx" 
```

Enter fullscreen mode Exit fullscreen mode

请考虑将此服务捐赠给在[的服务，让我们加密](https://letsencrypt.org/donate/)。尽管本文中讨论的想法看起来很简单(不说天真的安全性)，但在“让我们加密基础”上做了一些增强，这使得这个选择坚如磐石。如果你在容器环境中工作，特别是 Kubernetes，你应该检查 Github 上的[凯尔西·海托华](https://twitter.com/kelseyhightower) [Kube 证书管理器](https://github.com/kelseyhightower/kube-cert-manager)项目。网飞的 [Lemur](https://github.com/Netflix/lemur) 是管理证书的另一种选择，你可以在这里阅读[介绍文章](https://medium.com/netflix-techblog/introducing-lemur-ceae8830f621)。

**阅读清单**

*   [如何设置 SSL 让我们在 Heroku 上免费加密](https://medium.com/should-designers-code/how-to-set-up-ssl-with-lets-encrypt-on-heroku-for-free-266c185630db)
*   [使用 Node.js 和 Nginx 通过 SSL 的 WebSockets】](https://www.exratione.com/2013/06/websockets-over-ssl-with-nodejs-and-nginx/)
*   更多关于 Nginx HTTPS 重定向的讨论: [1](https://serverfault.com/a/258424) ， [2](https://bjornjohansen.no/redirect-to-https-with-nginx) ， [3](https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/#using-if)
*   [介绍狐猴](https://medium.com/netflix-techblog/introducing-lemur-ceae8830f621)
*   [椭圆曲线私钥结构](https://tools.ietf.org/html/rfc5915)