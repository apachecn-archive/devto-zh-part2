# Masonite 存储库模式

> 原文：<https://dev.to/masonite/repository-pattern-with-masonite-4a0n>

# 简介

本文将介绍如何用一个 [Masonite](https://github.com/MasoniteFramework/masonite) 应用程序实现存储库模式。

“存储库模式”对我来说一直很奇怪，并且从来没有真正理解为什么它被称为存储库。也许是因为我相信“存储库”这个名字应该留给版本控制存储库，所以我很难将第二个定义与“存储库”这个词联系起来。

为了本文的目的以及您的生活向前发展，让我们将存储库想象成“您可以在业务逻辑层中使用的数据的集合”。

# 问题

为了进一步解释，任何应用都有 4 层:

*   业务逻辑层(控制器)
*   表现逻辑层(视图)
*   数据访问层(模型)
*   数据存储层(数据库/MySQL/Postgres/等)

通常最好不要将这些层混合在一起，因为它们通常将一个实现与另一个实现紧密耦合。在编写软件一段时间后，您可能已经意识到您一直在通过将模型导入到控制器逻辑中来实现这一点。

所以大多数情况下，我们在控制器中使用我们的模型，只是简单地将它们导入到文件中并使用它们。假设我们正在为我们当前的雇主建立一个商业线索维护程序:

```
from app import Leads

class SomeController:

    def show(self):
        leads = Leads.all() 
```

Enter fullscreen mode Exit fullscreen mode

很好，这样就可以从销售线索模型中获取所有的销售线索。但是如果我们有几个独立的领导模型呢？我们可能会有这样的东西:

```
from app import LocalLeads, ForeignLeads

class SomeController:

    def show(self):
        local_leads = LocalLeads.all()
        foreign_leads = ForeignLeads.where('sales_id', '>', 20).get() 
```

Enter fullscreen mode Exit fullscreen mode

这可能是我们在几个控制器方法中需要做的:

```
from app import LocalLeads, ForeignLeads, WallStreetLeads

class SomeController:

    def show(self):
        local_leads = LocalLeads.all()
        foreign_leads = ForeignLeads.where('sales_id', '>', 20).get()

        # More Logic Here
        ...

        return view('sales', 
            {'local': local_leads,
            'foreign': foreign_leads
            ...
            }) 

    def sales(self):
        local_leads = LocalLeads.all()
        foreign_leads = ForeignLeads.where('sales_id', '>', 20).get()

        # More Logic Here
        ...

        return view('sales', 
            {'local': local_leads,
            'foreign': foreign_leads
            }) 
```

Enter fullscreen mode Exit fullscreen mode

# 问题 2

这种方法的第二个问题是，这可能在小规模甚至单个控制器上工作，但如果我们需要在多个控制器甚至中间件和代码的其他部分中使用这种模型逻辑，我们将会遇到问题，因为这样我们可能会在整个应用程序中散布非常冗余的代码。

通过将所有的模型加入逻辑放在一个地方，我们可以在代码的任何部分共享，存储库有助于做到这一点。需要在存储库中进行的任何更改，而不是我们的控制器、中间件或代码的其他部分。

# 解决方案 1(有问题)

一种解决方案是将所有的逻辑放入构造函数中，如果它们在每个方法中总是相同的话:

```
from app import LocalLeads, ForeignLeads

class SomeController:

    def __init__(self):
        self.local_leads = LocalLeads.all()
        self.foreign_leads = ForeignLeads.where('sales_id', '>', 20).get()

    def show(self):

        # More Logic Here
        ...

        return view('sales', 
            {'local': self.local_leads,
            'foreign': self.foreign_leads
            ...
            }) 

    def sales(self):

        # More Logic Here
        ...
        return view('sales', 
            {'local': self.local_leads,
            'foreign': self.foreign_leads
            ...
            }) 
```

Enter fullscreen mode Exit fullscreen mode

### 真实世界场景

但问题是，如果他们需要基于他们所在的控制器方法的机会呢？在现实世界中，我们可能会遇到这样的情况，它们通常是不同的:

```
from app import LocalLeads, ForeignLeads
class SomeController:

    def show(self):
        local_leads = LocalLeads.all()
        foreign_leads = ForeignLeads.where('sales_id', '>', 20).get()

        # More Logic Here
        ...
        return view('sales', 
            {'local': local_leads,
            'foreign': foreign_leads
            }) 

    def sales(self):
        local_leads = LocalLeads.first()
        foreign_leads = ForeignLeads.where('order_id', 2).get()

        # More Logic Here
        ...

        return view('sales', 
            {'local': local_leads,
            'foreign': foreign_leads
            }) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们不能真正抽象出这些价值。另外，所有这些逻辑都是非常多余的。

这不仅是这个控制器的问题，而且如果我们需要在不同的控制器中使用完全相同的逻辑怎么办？现在，我们需要将所有逻辑复制并粘贴到第二个甚至第三个控制器中。

# 引入知识库

我们可以简单地创建一个存储库类。使用 Masonite，我们可以将它添加到一个`app/repositories`目录中:

```
app/
  http/
  repositories/
    LeadRepository.py 
```

Enter fullscreen mode Exit fullscreen mode

让我们打开这个资源库，创建一个基础类:

```
class LeadRepository:

    def get_sales():
        pass

    def get_domestic_sales():
        pass 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们现在有了一个存储库，我们希望获得所有销售和所有国内销售。让我们尝试将控制器中的内容抽象到这里。为了这个特定应用的目的，我们将把每一个都放入字典中，因为这可能是我们的需求:

```
from app import LocalLeads, ForeignLeads

class LeadRepository:

    def get_sales():
        return {
            'local': LocalLeads.all(),
            'foreign': ForeignLeads.where('sales_id', '>', 20).get()
        }

    def get_domestic_only():
        return {
            'local': LocalLeads.all(),
            'foreign': ForeignLeads.where('country', 'US').get()
        } 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在让我们将它导入到我们的控制器中，然后再试一次:

```
from app.repositories import LeadRepository

class SomeController:

    def __init__(self):
        self.leads = LeadsRepository()

    def show(self):
        sales = self.leads.get_sales()

        # More Logic Here
        ...

        return view('sales', 
            {'local': sales['local'],
            'foreign': sales['foreign']
            }) 

    def sales(self):
        sales = self.leads.get_domestic_only()

        # More Logic Here
        ...

        return view('sales', 
            {'local': sales['local'],
            'foreign': sales['foreign']
            }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

太好了，就这样！存储库可以用来抽象出我们可能在多个控制器甚至整个应用程序中使用的任何动态模型获取。现在，我们可以将该存储库添加到我们需要的任何控制器中，所有逻辑都保留在代码中的一个位置。

如果你喜欢这篇文章，一定要去 [GitHub Repo](https://github.com/MasoniteFramework/masonite) 给它一颗星或者加入 [Masonite Slack 频道](https://slack.masoniteproject.com)