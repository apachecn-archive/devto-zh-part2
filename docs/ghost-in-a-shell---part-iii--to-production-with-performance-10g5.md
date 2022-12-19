# 《空壳里的幽灵》——第三部分:生产与性能

> 原文：<https://dev.to/ebreton/ghost-in-a-shell---part-iii--to-production-with-performance-10g5>

[![Ghost in A shell - part III : to Production, with performance](../Images/16d7342c4dee2b0bb1c3121bcd25fae6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BrPhpqqH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://manu.breton.ch/blog/content/images/2018/06/Ghost-in-A-shell.png)

实际上，我对*我的*作品的[前一篇博客](https://dev.to/ebreton/ghost-in-a-shell---part-ii---https-is-the-norm-1jj4)的结果非常满意:它有一个代理，它服务于 HTTPs，sqlite3 毕竟还不错...而对于我的 7.5 用户来说，就是*太棒了*。

你对“伟大”的定义可能会略有不同...

> 将*的生产优势*定义为基于以下因素提供满足感的设置如何？
> 
> 1.  性能(包含在本博客中)
> 2.  易于维护(在下一篇博客中讨论)
> 3.  可靠性(不确定这是否适合本教程的范围)

Soooo...先说性能。

# 哲学

我**不会**深入表演主题，因为太多的东西需要涵盖。让我来建议这条路:专注于建立一个工具箱，让你

1.  轻松运行性能/负载测试(一个命令听起来不错，和往常一样；那将是`make gatling`
2.  轻松查看输出指标(就像在 web 浏览器中一样，谢谢您，加特林！)

背后的想法是将我们的决策建立在相对度量的基础上，这意味着我们将比较做出一些改变(理想的是:任何改变)之前和之后的测试结果。将根据结果的变化验证更改

但是说够了，让我们确保我们准备好了...

# 先决条件

## a)来自前几集...

我假设你已经浏览了我的前两篇博客:

1.  [第一部分:localhost](https://dev.to/ebreton/ghost-in-a-shell---part-i--localhost-5he9) ，让你熟悉 *Makefile* 的主要命令，并关注 localhost
2.  第二部分:HTTPs 是规范，它向您展示了如何在一台服务器上无畏地生成 ghost 实例

没有新的依赖，这意味着你可以使用`docker`、`make`和两个仓库[生产栈](https://github.com/ebreton/prod-stack)和[空壳子](https://github.com/ebreton/ghost-in-a-shell)。

## b)如果您希望发送电子邮件(概率很高)

Ghost 团队对发送电子邮件提出了一些建议，其中一个解决方案是[利用 MailGun](https://docs.ghost.org/docs/mail-config#section-mailgun) 。跟随链接，以及链接后面的步骤。

这将给你通过他们的平台发送电子邮件的凭证。这些证书将被放入*。/etc/prod.env* :

```
$ cp etc/prod.env.sample etc/prod.env
$ vi etc/prod.env
  ...
  # see https://docs.ghost.org/docs/mail-config#section-mailgun
  # you will find an updated screencast to understand exactly where to find these details
  MAILGUN_LOGIN=(Sandbox) default SMTP Login
  MAILGUN_PASSWORD=(Sandbox) default Password 
```

Enter fullscreen mode Exit fullscreen mode

这些变量将在“生产模式”下生成实例时使用，正如我们在 Makefile 中看到的:

```
prod:
    ...
    -e mail__transport=SMTP \   
    -e mail __options__ service=Mailgun \
    -e mail __options__ auth__user=${MAILGUN_LOGIN} \
    -e mail __options__ auth__pass=${MAILGUN_PASSWORD} \
    ... 
```

Enter fullscreen mode Exit fullscreen mode

因为我们现在处理的是“生产”环境，所以设置不像使用 SMTP 服务器时那么简单...因此途中出现了一些“惊喜”:

*   MailGun 会向你索要信用卡，即使你一直使用他们的免费计划
*   使用默认的登录名和密码，您必须声明您希望向其发送电子邮件的所有收件人(并且他们必须批准接收这些电子邮件)
*   要绕过这个限制，您必须在 MailGun 中设置您的域。(您需要为此修改您的 DNS 区域)

因此，如果您不喜欢使用 MailGun，您可以从`make prod`命令中删除上面的 4 行代码，或者让它适应另一个服务提供商(如果您这样做了，请告诉我结果如何！)

下一步是设置数据库...

# 切换 DB

## 一)目标

我们希望使用 MySQL 而不是 sqlite3，因为它是 Ghost 团队推荐的。很公平。

## b)设置

prod-stack 已经在一个容器中运行了一个 MariaDB，如 [docker-compose.yml](https://github.com/ebreton/prod-stack/blob/master/docker-compose.yml#L29) 文件:
中所定义的

```
 db-shared:
    image: mariadb:latest
    container_name: db-shared
    restart: always
    env_file:
      - $PWD/etc/db.env
    volumes:
      - db_data:/var/lib/mysql 
```

Enter fullscreen mode Exit fullscreen mode

只有一个定制部分，即保存 MYSQL_ROOT_PASSWORD 的 *db.env* 文件。当您通过运行`make`安装 prod-stack 时，已经为您生成了这个文件。这里提醒:

```
# from prod-stack root directory
$ make
cp etc/traefik.toml.sample etc/traefik.toml
cp etc/db.sample.env etc/db.env
sed -i s/password/auMr9jsMrPnX0Oatb9j4yX2AYBN2jTQV/g etc/db.env
Generated etc/db.env
... 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我猜你知道接下来会发生什么:从 *prod-stack/etc/db.env* 中复制值，并将其粘贴到你的*ghost-in-a-shell/etc/prod . env*文件中。它现在应该看起来像:

```
# password set for db-shared, as defined in prod-stack/etc/db.env
MYSQL_ROOT_PASSWORD=auMr9jsMrPnX0Oatb9j4yX2AYBN2jTQV

# see https://docs.ghost.org/docs/mail-config#section-mailgun
# you will find an updated screencast to understand exactly where to find these details
MAILGUN_LOGIN=postmaster@your.domain.com
MAILGUN_PASSWORD=your-key 
```

Enter fullscreen mode Exit fullscreen mode

完成了吗？由于 Makefile 中的以下代码行，您已经准备好使用 MariaDB 而不是 sqlite3。

```
prod:
    ...
    -e database__client=mysql \
    -e database __connection__ host=db-shared \
    -e database __connection__ user=root \
    -e database __connection__ password=${MYSQL_ROOT_PASSWORD} \
    -e database __connection__ database=${NAME} \
    ... 
```

Enter fullscreen mode Exit fullscreen mode

注意，您可以看到到 DB 服务器的连接是硬编码的:它是通过容器名`db-shared`建立的，如前所述，容器名是在*prod-stack/[docker-compose . yml](https://github.com/ebreton/prod-stack/blob/master/docker-compose.yml#L29)*中定义的。

# `make prod`

在上面的两个部分中，我们创建了一个包含三个环境变量的 *etc/prod.env* 文件。这允许我们调用`make prod`，这将创建一个 ghost 实例:

*   Nginx+Traefik 代理后面(同`make qa`)
*   可通过 HTTPs 访问(与`make qa`相同)
*   使用 MailGun 发送电子邮件(**新**)
*   使用 MariaDB 代替 sqlite3 ( **新**)

因为我们处于一种相对的心态(还记得哲学吗？)，我们将比较我们的新设置与旧设置的性能...

1.  为练习定义一个新的博客名称:`export NAME=fire-at-blog`
2.  用 sqlite3: `make qa`生成实例(为了简单起见，我们将使用默认内容)
3.  衡量绩效(并生成我们的第一份报告):`make gatling`

[![Ghost in A shell - part III : to Production, with performance](../Images/d1c259c8408ffc4cba76e526121a70a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--we1UbCMm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://manu.breton.ch/blog/content/images/2018/06/with.sqlite3.png)

1.  使用 MariaDB: `make stop prod`重新开始同一个博客(您也可以使用`make stop prod logs` target 跟踪日志并查看实例何时准备好)
2.  再次测量性能:`make gatling`

[![Ghost in A shell - part III : to Production, with performance](../Images/d565711f30656fc947c60c6f8dc345ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QXRrCeaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://manu.breton.ch/blog/content/images/2018/06/with-mariadb.png)

1.  比较写在文件夹*中的两份报告。/加特林-结果*

我们并不真正关心这两份报告中任何一份的绝对值。我们对这些差异感兴趣。不管期望是什么..我们现在有了一个命令的详细数字。我们可以在浏览器中直接访问这些报告。

为了便于说明，我只显示了请求和活动的高级数字...但是你还有更多(谢谢[加特林](https://gatling.io)！)

当然，这并不是唯一的决定。不要忘记，我们还希望易于维护(这里 sqlite3 可能更好)，以及可靠性(MariaDB 在这方面有几个优点)

> 最重要的是:我们对一种解决方案与另一种解决方案的性能有一个清晰的认识

# `make gatling`

上一节中的魔法来自于命令`make gatling`，其实很简单:

```
gatling:
    docker run -it --rm \
        -v $(shell pwd)/etc/gatling-conf.scala:/opt/gatling/user-files/simulations/ghost/GhostFrontend.scala \
        -v $(shell pwd)/gatling-results:/opt/gatling/results \
        -e JAVA_OPTS="-Dusers=${GATLING_USERS} -Dramp=${GATLING_RAMP} -DbaseUrl=${GATLING_BASE_URL}" \
        --network=proxy \
        denvazh/gatling -m -s ghost.GhostFrontend 
```

Enter fullscreen mode Exit fullscreen mode

我们正在使用一个 docker 容器，它封装了 gatling 所需的所有(Java)层。

*   `-v $(shell pwd)/etc/gatling-conf.scala ...`并挂载几个卷用于输入(场景文件)
*   `-v $(shell pwd)/gatling-results ...`为输出安装音量。
*   `-e JAVA_OPTS="-Dusers=${GATLING_USERS} ...`根据环境变量调整脚本(更多细节见下文)
*   `--network=proxy`允许容器直接对它的兄弟进行负载测试(比如博客实例)

场景文件，*。/etc/gatling-conf.scala* ，有 4 个序列(自显):

```
atOnceUsers(nbUsers),
constantUsersPerSec(5) during (myRamp seconds),
rampUsers(nbUsers) over (myRamp seconds),
heavisideUsers(10) over (myRamp seconds) 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到有两个变量(`nbUsers` & `myRamp`)可以在您期望的负载中为您提供一点灵活性。默认值在 Makefile 中提供，可以像往常一样被覆盖。

```
GATLING_BASE_URL?=${PROTOCOL}://${NAME}:2368/${URI}
GATLING_USERS?=3
GATLING_RAMP?=5 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，galing 容器定位于运行 blog 实例的容器。(因此 gatling 命令中有了`--network=proxy`参数)。这里有一个更现实的例子:

`GATLING_BASE_URL=https://your.qa.com/blog GATLING_USERS=20 make gatling`

您可能想知道什么是“完整的 URL”，因为我们使用了一个“..._BASE_URL "。您必须在这里更改配置文件(或使用另一个文件)。到目前为止，它们被编码为下面的数组，需要多少次就使用多少次(感谢最后一位`.circular`)。

```
 val uriFeeder = Array(
    Map("URIKey" -> s"welcome"),
    Map("URIKey" -> s"the-editor"),
    Map("URIKey" -> s"using-tags"),
    Map("URIKey" -> s"managing-users"),
    Map("URIKey" -> s"private-sites"),
    Map("URIKey" -> s"advanced-markdown"),
    Map("URIKey" -> s"themes")
  ).circular 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，我们可以选择除了`.circular`(例如`random`)之外的其他策略，但是我在这里选择了可重复+可预测的策略，目的是能够将结果与受控场景进行比较。

让我们应用这些知识来负载测试我们的代理

# 用 Nginx 玩一点

按照这种方法，我们可以更仔细地观察代理(Nginx + Traefik ),并将我们的配置与另一个具有 Nginx 缓存的配置进行比较...为此，我们将使用配置*prod-stack/etc/000-proxy-with-cache . conf . sample*:

```
proxy_cache_path /var/cache levels=1:2 keys_zone=STATIC:10m inactive=24h max_size=1g;

server {
  listen 80 default_server;
  listen [::]:80 default_server;
  server_name _;

  location / {
    proxy_pass http://traefik/;
    proxy_set_header Host $host;
    proxy_buffering on;
    proxy_cache STATIC;
    proxy_cache_valid 200 1d;
    proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

棘手的是

*   继续在当地玩耍，同时
*   让加特林通过 NGinx 访问博客，而不是直接从他的容器到另一个容器...

为此，我们将使用`GATLING_BASE_URL`变量和我们计算机的内部 IP。这将使 gatling 容器通过主机(这意味着通过 Nginx 和 Traefik)查询博客，而不是直接指向 ghost 容器。

> 注意:127.0.0.1 将不起作用(既不是本地主机)，因为在这种情况下容器将查询自身，而不是 ghost 实例。

假设我们的内部 IP 是 192.168.178.93...流程如下:

1.  运行第一个没有缓存的容器:`NAME=no-cache-blog make qa logs`
2.  一旦启动，运行一个加特林容器:`NAME=no-cache-blog GATLING_BASE_URL=http://192.168.178.93/no-cache-blog make gatling`
3.  激活产品堆栈中的 Nginx 缓存

```
$ cd /your/path/to/prod-stak/etc/conf.d
$ rm 000-default.conf
$ cp ../000-proxy-with-cache.conf.sample 000-proxy-with-cache.conf

# at this point you can stop and start the stack again, 
# or simply go inside the nginx container and reload the conf:
$ docker exec -it nginx-entrypoint bash
root@bef39f4ad198:/# nginx -s reload
... signal process started 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用缓存运行第二个容器:`NAME=with-cache-blog make qa logs`
2.  一旦启动，再次运行加特林:`NAME=with-cache-blog GATLING_BASE_URL=http://192.168.178.93/with-cache make-blog gatling`
3.  查看结果(左边没有缓存，右边有缓存)

[![Ghost in A shell - part III : to Production, with performance](../Images/004fa1417937e6e40ca884625cdebd0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e6pwu2TA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://manu.breton.ch/blog/content/images/2018/06/with-or-without.png)

缓存的性能更好(正如预期的那样)，但内容现在可能会“滞后”，这可能会对用户体验产生负面影响...另一方面，它也可以对维护的容易程度产生积极的影响，允许我们对博客本身进行小的停机(如果缓存得好的话)。

还是那句话，最终的选择还是要看你自己。至少，您将有指标来比较各种解决方案的性能。

# 谨记在心，避而远之...

比较绩效时避免改变多个标准。

例如，进行以下比较是一个坏主意:

| `make dev` | `make qa` | `make prod` |
| --- | --- | --- |
| 一些性能 | 其他性能。 | 又一次失败。 |

结果实际上是无用的，因为在不同的配置集之间有多个更改:您同时更改了 DB、代理和电子邮件设置(可能是一些缓存层)。因此，你将看不到它们各自的影响。

# 结论

现在，您可以针对开发、测试或生产运行三种模式的 ghost 实例:

| 命令 | 描述 | 评论 |
| --- | --- | --- |
| `make dev` | 如果您只是希望桥接主机上的一个容器端口(默认为 3001 ),则可以进行简单的(本地)安装 | 又称`make` |
| `make qa` | 如果您希望在标准端口(80 或 443)上提供服务，并且如果您希望任何人都能轻松访问您的博客，那么前者并不方便，该命令设置了一个 traefik 路由器 | [产品堆栈](https://github.com/ebreton/prod-stack)的推荐用法 |
| `make prod` | 同上，使用 MariaDB 代替 SQLite 和电子邮件提供程序(Mailgun) | 需要 Mailgun 的(免费)帐户 |

不过，这个博客(更重要的)收获是简单的命令`make gatling`，并使用它来验证(小而明确的)变更。

实际上，一个好的做法是将命令集成到持续集成工作流中，并在性能下降时发出警报

在下一篇博客中，我将介绍几个维护助手...为了在所有的实例中保持一点秩序，我们现在可以到处繁殖