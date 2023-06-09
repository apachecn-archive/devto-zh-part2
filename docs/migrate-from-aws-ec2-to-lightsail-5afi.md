# 从 AWS EC2 迁移到 Lightsail

> 原文：<https://dev.to/david_j_eddy/migrate-from-aws-ec2-to-lightsail-5afi>

克罗斯从我的个人博客[https://blog.davidjeddy.com](https://blog.davidjeddy.com)上发布的。

作为所有云工程师的良好实践，我一直在寻找最大限度降低运营成本和简化运营的方法。对于这个博客来说，它目前的价格大约是每月 25 美元。成本来自 EC2 实例、RDS ( + 14 天备份)、EFS 卷、53 号路由域和出站流量。环顾 Lightsail 是一个更简单、更便宜的解决方案；所以我开始迁移。

## 简而言之，AWS Lightsail

从表面上看，Lightsail 是一个简化的用户界面，用于简单的 VPS 管理。可以通过 UI 提供服务器、SSH 密钥、端口访问和磁盘快照；简化的用户界面。

## 该过程

#### SSH 进入机器

这是标准的公平 Linux 访问。Lightsail 用户界面提供了*的下载链接。pem 文件。下载下来，移到~/。ssh，将权限改为 0400，正常连接。UI 还提供了一个基于 web 的 SSH 界面。我怀疑这在后台利用了 AWS 系统管理器。

#### 更新的操作系统

再次，相当标准的票价，不过我做了升级到 Ubuntu 18.04。称我勇敢，但这是一个个人博客网站，而不是一个公司支付系统。

#### 安装服务

[LEMP](https://www.digitalocean.com/community/tags/lemp?type=tutorialshttps://www.digitalocean.com/community/tags/lemp?type=tutorialshttps://www.digitalocean.com/community/tags/lemp?type=tutorials) 栈 [FTW](https://knowyourmeme.com/memes/win-epic-win-for-the-win)

*   Nginx 配置总是无处不在。到处配置 JSON 文件。Ugg。
*   PHP/PHP-FPM 像往常一样简单。我选择了 Unix sock 风格的配置。
*   MySQL，[不要忘记运行强化脚本](https://www.techrepublic.com/article/how-to-harden-mysql-security-with-a-single-command/https://www.techrepublic.com/article/how-to-harden-mysql-security-with-a-single-command/)

#### 迁移 Wordpress

随着核心服务的安装，我进入了迁移的未知部分。如何迁移 Wordpress？幸运的是，Wordpress 通过**设置菜单**项包含了基本的导入/导出功能。

<figure>[![](img/a45b1b1def53480f4c1800eb499fc5b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8bOWi4_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/wp_export.png) 

<figcaption>赢了！</figcaption>

</figure>

获取输出并将其导入到新实例中。除了链接目录内容，一切都很好。这太糟糕了，我不得不找到一个替代方案，手动迁移内容。:(嘘。

#### 更新域

随着内容的移动，我转到了 Route 53 并设置了一个子域(https://blog.davidjeddy.com ),并将其指向通过 Lightsail UI 提供的新静态 IP。然后我等待繁殖。曾经我确信博客。*正确指向我编辑的 [TLD](https://en.wikipedia.org/wiki/Top-level_domain) 重定向到博客。*因此不会丢失任何有机流量或弄乱任何人的书签。

#### 备份

这是网络，崩溃时有发生。这是生活的现实。对于 LightSail backup，我的应用程序只需在用户界面中点击“创建快照”即可完成，并且会对磁盘进行镜像。我甚至可以通过 CLI API 做到这一点。因为该实例现在包含了我的整个应用程序和数据库，所以它是一个全包备份。[一定要检验你的恢复计划！](https://www.cloudcomputing-news.net/news/2016/nov/02/testing-your-disaster-recovery-plans-best-practice-guide/)

#### 淘汰旧的基础设施

一旦我确定一切正常，我就对旧位置进行了“软关闭”。特别是在网站顶部添加了一个横幅，表明迁移正在进行。在周末，我检查了流量模式，以确保旧实例不再接收流量。然后，关灯。

## 结论

迁移前的平均成本在 25 美元/月的范围内。迁移后的预计成本约为每月 10 美元。虽然每月不到几千个，但这表明 AWS 是认真的，要让其他基本的 VPS 提供商为钱而战。低成本+ AWS 基础设施=自下而上的市场竞争。