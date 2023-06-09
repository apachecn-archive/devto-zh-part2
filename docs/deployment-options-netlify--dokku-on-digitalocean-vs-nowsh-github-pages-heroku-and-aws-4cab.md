# 部署选项:数字海洋上的 Netlify+Dokku vs now . sh、GitHub Pages、Heroku 和 AWS

> 原文：<https://dev.to/hugo__df/deployment-options-netlify--dokku-on-digitalocean-vs-nowsh-github-pages-heroku-and-aws-4cab>

> 确定在[数字海洋](https://m.do.co/c/4ee8b2d1adb4)部署栈上的 [Netlify](https://www.netlify.com/) + [Dokku](https://github.com/dokku/dokku) 用于 JavaScript 和节点。

在过去的几年里，我从来没有为我的应用程序和项目确定一个 goto 部署策略。从技术角度来说，无论是 API 服务器还是通过手柄处理渲染，它都倾向于前端的 Vue 或后端的 Node 静态站点生成器。

我最终选定的是[数字海洋](https://m.do.co/c/4ee8b2d1adb4)上的 [Netlify](https://www.netlify.com/) + [Dokku](https://github.com/dokku/dokku) 。对任何静态的东西都有效(对表单、CMS 和 lambda 更是如此)。Dokku，“您见过的最小的 PaaS 实现。”任何后端应用程序和数据库。这是我用来部署我的最新项目[发布频率](https://accountableblogging.com/post-frequency)和[负责任的博客](https://accountableblogging.com/post-frequency)的工具，用来量化和提高你的博客产出。

这是一长串服务的利弊列表:

*   👍→专业
*   👎→ con
*   🤔→未定
*   🚨→对我来说是决定性因素
*   🤓→有多好

我过去的部署经历

*   now.sh
*   github pages-github 页面
*   Heroku
*   自动警报系统
*   数字海洋
*   填妥了吗

堆栈向前移动

*   Netlify
*   数字海洋上的 Dokku

## 我过去的部署经历

出于这些目的，我倾向于以下部署选项:

### [现在. sh](https://now.sh)

*   👍静态托管
*   👍应用程序托管
*   👍可以建立您的静态网站/前端
*   👎没有数据库托管
*   🤔内置 DNS
*   🚨我没有充分利用它(只有 2 次部署，而不是限制的 5 次)

### [GitHub Pages](https://pages.github.com/)

*   👍自由的
*   👍烘焙到您的 GitHub repos 中(简单到推送至`gh-pages`分支)
*   👍静态托管
*   👎无法自行构建您的应用
*   👎在很长一段时间内，很难使用 SSL(必须将其与 Cloudflare 配对)
*   👎处理域名

### [英雄库](https://www.heroku.com/)

*   👍应用托管
*   👍数据库托管(有一个慷慨的免费层)
*   👍`git push heroku master`部署
*   👍附加系统
    *   👍只需点击一下鼠标，即可实现日志记录、聚合和监控等功能
*   👍构建包系统
*   👍简单的域管理
*   👎 🤑按应用/动态定价
*   🚨只是对于低流量的 app 来说不值得
*   🚨扩展节点应用程序涉及到增加进程的数量+节点进程不是非常资源密集，这不是一个很好的资源/成本使用(不是说我曾经需要扩展我的个人应用程序)

### AWS

*   👍你能想到的都有
*   👎仪表板 UI
*   🚨免费层到期(1 年)后，即使是最小的实例，定价也会很高

### [数字海洋](https://m.do.co/c/4ee8b2d1adb4)

*   👍仪表板 UI
*   👍竞争的价格形成
*   👎管理服务器
    *   ssh-ing 安装数据库和运行时需求(节点，pm2)
    *   通过 ssh 部署(或者至少 ssh 到`git pull`
*   👎设置 nginx😛
    *   *我私下很喜欢处理 nginx 配置，只是不以发布一些代码为代价*

**我不会在这里提到一键式部署，因为我在决定尝试 Dokku 之前没有使用过它们*

### [Vultr](https://www.vultr.com/)

*   👍我当时能找到的最便宜的 VPS(2.50 美元)
    *   警告:机器规格是你所支付的，这足以托管一个大学最后一年的项目 API + React 应用程序
*   👎管理服务器(参见数字海洋)

## 堆栈向前移动

### [Netlify](https://www.netlify.com/)

*   👍自由层(超越慷慨)
*   👍静态托管
*   👍可以建立您的静态网站/前端
*   🤔拉姆达·❤
    *   👍为某些东西拥有后端(那不是后端)是很酷的
    *   👎与后端代码捆绑器打交道
    *   👎调试缩小的后端代码
    *   🤔lambda 启动时间(如果您在前端有一个 SPA，可以在等待时提供一个很好的加载状态，这可能没问题)
*   👍形式
    *   集成起来很简单
    *   无需过早添加第二项服务，例如在登录页面收集电子邮件地址
*   👍Netlify CMS +身份
*   🤔CDN 烘焙于
*   👍域名服务器(Domain Name Server)
*   👍自动 SSL
*   🤓预览应用程序
*   👍不错的 GitHub 集成

### [多库](https://github.com/dokku/dokku)上[数字海洋](https://m.do.co/c/4ee8b2d1adb4)

*   👍需要多贵就多贵
    *   只要不耗尽你的 CPU/RAM，你可以拥有任意多的应用程序
    *   易于扩展
    *   起价 5 美元/月
*   👍[数字海洋](https://m.do.co/c/4ee8b2d1adb4)的[“Dokku 一键部署”](https://www.digitalocean.com/products/one-click-apps/dokku/)

Dokku 利弊(不考虑[数字海洋](https://m.do.co/c/4ee8b2d1adb4)

*   👍`git push dokku master`部署
*   👍从命令行进行管理
*   👎默认情况下没有用户界面，尽管 https://github.com/palfrey/wharf 可能很酷
*   👍简易 SSL
*   👍简单的域管理
*   👍应用程序托管
*   👍数据库托管
*   🤔一个实例上的多个应用
    *   如果 droplet 宕机，2-3 个应用就会宕机(我认为没有人会在意这一点)
    *   一个高流量的应用程序可能会消耗资源(当应用程序获得流量时，将它们转移到其他地方)
*   🤔还是自托管
    *   除了现在我需要能够管理 Docker/Dokku 的东西

## 设置 Dokku up

*   👍[在](http://dokku%20domains:add%20feed-accountable-blogging%20feed.accountableblogging.com)[数字海洋](https://m.do.co/c/4ee8b2d1adb4)上一键部署水滴
*   👎对于为 Dokku 实例的子域设置 DNS 记录，没有明确的指南
    *   以下是我的尝试:
    *   找出服务器的 IP 地址
    *   创造一个 A 记录
    *   对于`*.deploy.yourdomain.tld`和`deploy.yourdomain.tld`(随意用任何子域替换`deploy`)，对我来说是`deploy.codewithhugo.com`
    *   将记录指向 IP 地址
    *   (如果你用的是 CloudFlare 或者其他可以代理的东西):**不要使用 CDN/代理功能**
    *   为了检查它的工作情况，宋承宪`deploy.yourdomain.tld`
*   🤔没有本地 CLI(一切都通过 SSH 完成)
    *   将`alias dokku="ssh -t dokku@deploy.yourdomain.tld"`添加到您的`.bashrc` / `.zshrc`
    *   然后像在远程服务器上一样使用`dokku <command>`
*   👎应用程序的自定义域也是如此
    *   再说一遍，我的看法
    *   `dokku domains:add your-app-name subdomain.domain.tld`
    *   创造 CNAME 纪录
    *   名称:`subdomain`
    *   值:`your-app-name.deploy.yourdomain.tld`
*   👍易信加密([)https://github.com/dokku/dokku-letsencrypt](https://github.com/dokku/dokku-letsencrypt)
    *   ssh 进入服务器
    *   `sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git`
    *   将 letsencrypt 添加到应用程序
    *   `dokku letsencrypt app-name`
    *   添加 letsencrypt 证书自动续订 CRON 作业:
    *   `dokku letsencrypt:cron-job --add`

## 离别的思念

我从 now.sh 上迁移几个应用并不是为了从差变好，而是从好变得更好(为了我的需要)。

[无刷标志
【bill jelen】](https://unsplash.com/@billjelen?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)