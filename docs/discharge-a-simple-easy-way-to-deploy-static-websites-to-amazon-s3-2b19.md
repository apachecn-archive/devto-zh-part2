# 放电，一个简单，容易的方式部署静态网站到亚马逊 S3

> 原文：<https://dev.to/brandonweiss/discharge-a-simple-easy-way-to-deploy-static-websites-to-amazon-s3-2b19>

[![](img/8c092da029f64286941478ad4444d16b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W7M3w81_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nma5ir3der6n1ujqw16s.png)

据我所知，将静态网站部署到亚马逊 S3 是最简单的方法。我花了几个小时仔细阅读 AWS 糟糕的文档，这样别人就不用看了。

亮点是:

*   几乎不需要了解 AWS
*   用于配置部署的交互式用户界面
*   正在发生的事情的逐步列表
*   支持在 URL 中没有尾随斜线
*   支持子域
*   使用 AWS 配置文件(命名凭据)向 AWS 进行身份验证
*   CDN (CloudFront)和 HTTPS/TLS 支持

这为我节省了大量的时间；希望对其他人有帮助。让我知道你的想法！