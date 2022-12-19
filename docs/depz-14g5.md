# 德普兹

> 原文：<https://dev.to/ibalajisankar/depz-14g5>

<figure>[![Depz](../Images/6406fb3d99ce28f37676b78f4af39de2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N-iTztzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1q8ubrpn2t11gu2ilm1.png) 

<figcaption>Depz</figcaption>

</figure>

大家好🙌🏻

这篇文章是关于我的#DepZ[WIP]

## 什么是 Depz？

DepZ 是一个用于托管静态网站的开源平台。

## 我为什么要建 DepZ？

当产品处于开发阶段时，大多数组织会购买 3 到 4 个实例。他们通常维护开发、试运行、测试、预编程和生产。对于每个构建，他们需要一个特定的实例(通常是 EC2 t3.nano 实例)，每个实例大约花费 15 美元。

我们可以通过像 Netlify 这样的平台来实现这一点，但是大多数人都会有这样的问题“为什么我们需要共享我们的整个源代码？”因此，需要一种经济高效、安全且不共享源代码的部署方式。

## 连续部署

由于我们是高层冷漠的个体，我们从不喜欢构建我们的网站并将构建文件复制到一个特定的实例中。所以需要持续集成。

## 如何才能实现这一切？

静态站点的部署方式有很多种:
1。现在
2。S3 斗
3。天蓝色
4。谷歌云
5。Heroku
6。汹涌

对于连续部署:
。1.位桶管道
2。亚马逊管道

## DepZ 如何工作？🎩

DepZ 将 Bitbucket 与亚马逊的 S3 整合在一起。

1.  登录 Bitbucket 账号

    <figure>[![Depz](../Images/6406fb3d99ce28f37676b78f4af39de2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N-iTztzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1q8ubrpn2t11gu2ilm1.png)

    <figcaption>Bitbucket 登录</figcaption>

    </figure>

2.  一旦你登录，它将显示该 bitbucket 帐户中的所有回购。

    <figure>[![Depz](../Images/3ea7736309dfca44878943dc880ffea1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ma2aamz7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yzyb3jl6rj6wje321ygq.png)

    <figcaption>bit bucket 回购清单</figcaption>

    </figure>

3.  选择要部署的回购

    <figure>[![Depz](../Images/f142f803f3b03d542917773a9001200d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OnMAwYoc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0j9gv708c8l7jo4rxms0.png) 

    <figcaption>部署设置</figcaption>

    </figure>

4.  部署完成后，将会生成一个 URL。

    <figure>[![Depz](../Images/de5ae35217fdc4dd4255b9ac64d381d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gUXhylgb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u1lqe1e1zkhi99n5h0zb.png) 

    <figcaption>直播网址</figcaption>

    </figure>

5.  一旦网站上线，我们需要从 BitBucket repo 中提取代码，因为连续部署是通过管道处理的。

/*AWS 区域选择，管道为#WIP。/

## 设置 DepZ

```
 1\. Setup the Bitbucket OAuth.

    2\. Update the secret key in the source code.

    3\. Store the AWS key and AWS secret in the BitBucket env variable.

    4\. Deploy the site 😈 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
未来目标

合并

1.  Git 提供者
    *   开源代码库
    *   GitLab
2.  部署
    *   现在
    *   github pages-github 页面
    *   蔚蓝的
    *   谷歌云
    *   Heroku
    *   汹涌
3.  亚马逊代码管道
4.  自定义域。
5.  HTTPS
    1.  亚马逊证书管理器(使用谷歌木偶师)
    2.  LetsEncrypt

你可以在这里查看:[https://depz.balajisankar.tech/](https://depz.balajisankar.tech/)
如果你想玩的话，这里是代码:[https://bitbucket.org/ibalajisankar/depz/](https://bitbucket.org/ibalajisankar/depz/)
你喜欢你所读的吗

我们也可以在推特上联系