# 6 个 Devops 面试问题

> 原文：<https://dev.to/hullsean/6-devops-interview-questions--3e7c>

[![Code Gif](img/fe7277e8ef82daff94518478d567e8ce.png "Code Gif")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m-n523CH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/srn1rae6d9e67y2b3tvi.gif)

如今 Devops 的需求量很大。在我参加的每一次 meetup 或科技活动中，我都会听到招聘人员或初创公司创始人谈论这个话题。似乎每个人都希望看到天才运营为他们的业务带来的好处。

在推特上找到肖恩·赫尔@赫尔肖恩。

也就是说技能集非常广泛，这解释了为什么没有更多的开发者选择 batton。

我觉得把面试问题列一个清单会有帮助。当然还有其他的，但这是我想到的。

## 1.解释 gitflow 发布流程

作为一名 devops 工程师，你应该有一个关于软件交付的良好基础。有了这些，你应该非常了解 git，尤其是标准的工作流程。

尽管有其他方法来管理代码，但是一个可靠的且被证实的方法是 gitflow 。总而言之你有两个主要分支，发展&高手。开发人员签出一个新的分支来添加一个特性，并将其推回到开发分支。您的 stage 服务器可以从这个分支自动构建。

你会希望定期发布软件的新版本。为此，您将开发合并到 master。然后，UAT 将从主分支自动生成。验收测试完成后，您可以将主服务器部署到生产环境中。于是有了[总运干线](http://www.paulhammond.org/2010/06/trunk/)的说法。

如果您知道修补程序是直接从主分支中完成的，并且是以这种方式直接推出的，那就更好了。

**相关: [8 问 AWS 专家](https://www.iheavy.com/2011/08/05/ec2-interview-aws-interview-cloud-interview-8-questions/)T3】**

## 2.您如何调配资源？

现在 devops 工具箱里有很多工具。Terraform 在[供应资源方面表现出色。有了它，你可以在声明性代码中指定你的应用程序需要在云中运行的一切。来自 IAM 用户、角色&组、dynamodb 表、rds 实例、VPC&子网、安全组、ec2 实例、ebs 卷、S3 存储桶等等。](https://www.iheavy.com/2018/02/12/easy-cloudformation-template-comparison-simple-terraform-beginner/)

当然，你也可以选择使用 CloudFormation，但是根据我的经验，terraform 更加完美。更重要的是，它支持多种云。想要在 GCP 或 Azure 中部署，只需移植您的模板&您马上就可以启动并运行了。

这需要一些时间来适应在 terraform 而不是在 AWS cli 或 dashboard 中构建东西的新工作流，但一旦你这样做了，你就会立即看到好处。您将获得我们在其他软件开发中看到的版本控制代码的所有优势。想回滚，没问题。想对您的基础设施进行单元测试吗？你也可以这样做！

一个 4 个字母的单词能划分 dev & ops 吗？T3】

## 3.你如何配置服务器？

如今配置管理的四大选择是 Ansible、Salt、Chef & Puppet。依我看，Ansible 有一些很好的优势。

首先，它不需要经纪人。只要你有 SSH 访问你的机器，你就可以用 Ansible 管理它。另外，您现有的 shell 脚本很容易移植到剧本中。Ansible 也不需要服务器来存放你的剧本。只需将它们保存在您的 git 存储库中，然后签出到您的桌面。然后在 yaml 文件上运行 ansible-playbook。瞧，服务器配置！

**相关:[如何雇佣一个不吸](http://www.iheavy.com/2011/11/25/how-to-hire-a-developer-that-doesnt-suck/)的开发者 T3】**

## 4.测试能实现什么？

单元测试和集成测试是持续集成的非常重要的部分。当你自动化你的测试时，你就形式化了你的站点和代码应该如何表现。这样，当您自动化部署时，您也可以自动化测试过程。让软件去做确保一个新特性不会破坏网站上任何东西的苦差事。

随着您自动化更多的测试，您加速了软件开发过程，因为您手动做的越来越少。这意味着更加敏捷，使业务更加灵活。

**相关:[对于小型开发团队来说，AWS 是否过于复杂？](http://www.iheavy.com/2016/04/26/is-aws-too-complex-for-small-dev-teams-startups/)T3】**

## 5.解释 Docker 的一个使用案例

Docker 是一种在本地机器或云中运行虚拟机的低开销方式。尽管它们不是严格意义上的独立机器，也不需要引导操作系统，但它们给了你许多好处。

Docker 可以封装遗留应用程序，允许您将它们部署到服务器上，否则使用旧版本的包和软件可能不容易安装。

Docker 可用于构建测试盒，在您的部署过程中促进持续的集成测试。

Docker 可以用来在云中提供设备，使用 swarm 你也可以编排集群。相当酷！

**相关:[微服务会不会就这么已经死掉了？](http://www.iheavy.com/2018/02/05/will-microservices-just-die-already/)T3】**

## 6.沟通与开发运维有什么关系

由于 devops 为组织带来了一个持续交付的新过程，它包含了一些风险。实际上，从长远来看，用老方法做事会有更大的风险，因为事情会发生变化。通过自动化，您可以更快地从故障中恢复。

但是这个新世界需要信仰的飞跃。它并不适合每一个组织或每一种情况，你可能会从 devops 圣书所说的和你的组织可以容忍的东西中找到一个平衡点。然而不可避免的是，当你提倡新的做事方式时，交流变得非常重要。

**相关:[如何将技能迁移到云端？](https://www.iheavy.com/2017/11/06/migrate-skills-cloud-unix-devops-database/)T3】**