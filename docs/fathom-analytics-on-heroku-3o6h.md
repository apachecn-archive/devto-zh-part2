# Heroku 的 Fathom 分析

> 原文：<https://dev.to/teej/fathom-analytics-on-heroku-3o6h>

这最初是[发布在我的博客](https://tj.ie/fathom-analytics/)上。

几个月来，我的网站一直没有任何分析，因为我不喜欢把所有这些信息都给第三方。另外，这是一个相当小的操作，所以这不是我的要求。

也就是说，我一直在关注 [Fathom Analytics](https://usefathom.com/) ，因为它看起来是一个理想的解决方案:

> 它跟踪网站上的用户(不收集个人数据),并给你一个非书呆子式的顶级内容和顶级推荐者的分类。它这样做是以用户为中心的权利和隐私，没有出售，分享或放弃你收集的数据。对于任何技术水平的网站所有者来说，这都是一个简单易用的工具。

[看来源](https://github.com/usefathom/fathom/blob/master/pkg/api/collect.go#L47)，也尊重用户的“不跟踪”偏好。

我一直没有尝试，因为我不知道如何安装它，直到 Github 上的一位好心人给了我一个如何在 Heroku 上运行的快速演示。我正在用一个每月花费 7 美元的爱好 dyno 测试它，所以如果一切顺利，我会很乐意支付这笔费用。

你需要一个 [Heroku](https://www.heroku.com/) 账号，以及 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) 来跟进。你将需要有你的帐单细节填写，以及它利用插件获得一个数据库。如果你只是想看看涉及到什么，你也不必使用爱好动态。

所以事不宜迟，命令如下:

```
# Get the code
$ git clone https://github.com/usefathom/fathom.git
$ cd fathom

# Create a new heroku app
$ heroku create

# Push the fathom container to heroku's registry
$ heroku container:push web

# Release the container
$ heroku container:release web

# Configure fathon to use msql and a secret
$ heroku config:set FATHOM_DATABASE_DRIVER=mysql FATHOM_SECRET=whateverGeneratedSecretYouWantToUse

# Create a new mysql database
$ heroku addons:create jawsdb:kitefin

# Get the mysql connection url
$ heroku config:get JAWSDB_URL //mysql://user:password@host:3306/database_name

# Set database variables from the above url
$ heroku config:set FATHOM_DATABASE_NAME=database_name FATHOM_DATABASE_USER="user" FATHOM_DATABASE_PASSWORD="password" FATHOM_DATABASE_HOST="host:3306"

# Register a new user
$ heroku run ./fathom register --email=something@email.com --password='yourpassword' 
```

Enter fullscreen mode Exit fullscreen mode

为了澄清运行`heroku config:get JAWSDB_URL ...`时的注释，它返回的是在以相同格式`//mysql://user:password@host:3306/database_name`设置 Fathom 环境变量时插入下一个命令的内容。我知道这已经解释了，但我错过了，因为我太急切了。

之后就差不多好了。最后一步是链接到跟踪脚本，如下所示:

```
<!-- Fathom - simple website analytics - https://github.com/usefathom/fathom -->
<script>
(function(f, a, t, h, o, m){
    a[h]=a[h]||function(){
        (a[h].q=a[h].q||[]).push(arguments)
    };
    o=f.createElement('script'),
    m=f.getElementsByTagName('script')[0];
    o.async=1; o.src=t; o.id='fathom-script';
    m.parentNode.insertBefore(o,m)
})(document, window, '//your-app-name.herokuapp.com/tracker.js', 'fathom');
fathom('trackPageview');
</script>
<!-- / Fathom --> 
```

Enter fullscreen mode Exit fullscreen mode

我得说它花了大约 15 分钟才启动并运行，这对我来说是相当不错的。我很期待 Fathom 的未来。