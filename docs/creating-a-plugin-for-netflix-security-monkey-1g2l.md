# 为网飞安全猴子创建插件

> 原文：<https://dev.to/alanmbarr/creating-a-plugin-for-netflix-security-monkey-1g2l>

[![Image of Security Monkey](../Images/6b1e1d1599713049252441f3f6a19b78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S_IQoQjS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/www.alanmbarr.com/blog/images/Security_Monkey.png)

## 自动增强安全性

对我来说，安全性是一个非常难以理解的概念。感觉就像记录了很多很多细节，似乎很容易被它淹没。进入[安全猴子](https://github.com/Netflix/security_monkey)我的同事保罗介绍给我的工具。向云迁移的一个很酷的事情是云提供商通过安全设置审查提供的各种产品。不幸的是，很难跟踪所有不同的事情。Security Monkey 通过监视不同端点的变化并使用监视程序显示这些变化的 json 差异来为您处理所有这些事情。审计员根据观察者和通知者的发现运行业务规则，以提醒那些需要知道的人。它提供了一个统一的地方来获取和检查云环境中的更改。

## 编写插件

编写文档是非常困难的，试图扩充别人的软件并不容易，尤其是当用任何老方法来做都更容易的时候。我在文档中找到了一些关于添加自定义插件的不同方法的参考。一个使用/custom 文件夹，两个使用 python 中的 entry_points 插件系统。我不希望我的改变成为更大系统的一部分。因此，使用 entry_points 效果最好。有很多尝试和错误，由于我目前的电脑情况，我不想麻烦使用 docker。我最终在 azure 中创建了一个 vm，以便同时运行 security monkey 和开发插件。幸运的是，提供的文档是惊人的。甚至编写插件也很简单。我最终把自己搞糊涂了，因为我以为通过放入我的插件文件夹，我会以某种方式使用 security.monkey 名称空间，但最终只使用了我自己的插件名称空间。我从来没有专业从事过 Python，只是在业余时间或其他项目中做自己的机器学习。我还了解到 **init** 。python 需要 py 来知道这将是要安装的包的一部分。

## 了解概念

我的虚拟机上的安装工作得很好，运行大多数软件也很容易。理解观察者、审计者和通知者的角色稍微复杂一些。关于这些部件如何工作的复杂性，没有太多的信息，我主要是抄袭了我从 GitHub 实现这些概念中学到的东西。我创建的第一个项目是一个新的客户经理。我想看看如何开始向 Azure 介绍 Security Monkey。帐户管理器是最容易实现的。

```
from security_monkey.account_manager import AccountManager, CustomFieldConfig

class AzureAccountManager(AccountManager):
    account_type = 'Azure'
    identifier_label = 'Organization Name'
    identifier_tool_tip = 'Enter the Azure AD Tenant ID'
    access_token_tool_tip = "Enter the path to the file that contains the Azure personal access token."
    custom_field_configs = [
        CustomFieldConfig('access_token_file', "Personal Access Token", True, access_token_tool_tip),
        ]

    def __init__(self):
        super(AccountManager, self).__init__() 
```

我主要是复制了 GitHub 版本的大部分内容，我想为安全 Monkey UI 定义如何添加这个新类型，并为它指出在哪里可以找到它的凭证。在这种情况下，我将假设它是存储在磁盘某处的 json。

为了创建自定义观察器，新的观察器需要从基础继承并覆盖它的一些功能。一个 slurp 函数和一个从 ChangeItem 继承的单独的类，还有一些对 SecurityMonkey 具有唯一值的属性，使事情保持独立。让我颇感困惑的一件事是，这么多的 SM 假设像 AWS 这样的事情，感觉非常专注于 AWS，我担心我可能会做错事情，但这似乎不是一个问题。文档指出使用一个特定的文件作为连接逻辑，但是基于 GitHub 版本，这似乎是放在一个 util 文件中。基本上，观察器是一个预定的作业，它将访问帐户的各个端点，获取数据列表，并将其保存在 SM 数据库中，以显示给安全用户或审计员来运行规则。

审计员和观察者保持对共享索引的引用，这样他们可以协同工作。所有插件都是动态加载的，所以这使得迭代功能变得非常容易，并且当事情以一种明显的方式发生时，调试起来不会太难。

```
from security_monkey import app
from security_monkey.auditor import Auditor
from vusmplugins.watchers.azure.security_center import AzureSecurityCenter
import jmespath

class AzureSecurityCenterAuditor(Auditor):
    index = AzureSecurityCenter.index
    i_am_singular = AzureSecurityCenter.i_am_singular
    i_am_plural = AzureSecurityCenter.i_am_plural

    def __init__(self, accounts=None, debug=False):
        super(AzureSecurityCenterAuditor, self).__init__(accounts=accounts, debug=debug)

    def check_for_provisioning_monitoring_agent(self, item):
        """
        Check that security agent is collecting data
        :param security_details:
        :return:
        """
        tag = "Account contains policy with security monitoring agent off."
        result = [match for match in jmespath.compile('value[*].properties.logCollection').search(item.config)]

        if "Off" in result:
            self.add_issue(10, tag, item, notes="Account contains unprovisioned monitoring agents") 
```

我最终得到了这个，我不能完全看到它在我的 VM 中工作，可能是因为我没有设置工人运行 redis 和芹菜，但模式保持不变。审计员持有针对安全问题的业务规则。这样做的一个问题是，你的很多规则最终都变成了代码，这可能需要更多的用户界面编辑？总的来说，这是一次有趣的经历。现在的主要挑战是与 Azure 集成，因为身份验证方法各不相同，尤其是在帐户上。对于我的个人测试，我使用 azure cli 来生成我的帐户令牌。对于企业解决方案来说，服务原则似乎是一条可行之路。剩下的时间，我把 SM 与我们的本地工具(如 rabbitmq)集成在一起，并让自动运行开始工作。做一点 linux 操作工作是一个不错的改变。

## 包装完毕

如果您与多家云供应商打交道，那么一套不断扩展的新 API、工具和系统将成为您最好的朋友。按计划运行并审计您的环境是一个梦想。即使您将来不使用 Security Monkey，使用自动化工具来加强质量或启用它也是更加主动地管理您的系统的一个重要步骤。