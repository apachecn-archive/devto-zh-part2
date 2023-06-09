# Masonite 编码技巧:抽象递归控制器代码

> 原文：<https://dev.to/masonite/masonite-coding-tip-abstracting-recurring-controller-code-1p35>

# 简介

如果你不熟悉 [Masonite](https://github.com/MasoniteFramework/masonite) ，那么你应该检查一下 [GitHub repo](https://github.com/MasoniteFramework/masonite) 。

当使用 Masonite 开发时，如果您没有刻意避免，您可能会注意到您的控制器逻辑中有一些重复。在本教程中，我们将介绍如何在你的控制器中抽象你的代码，使它变得非常干净。

# 控制器构造器

如果您还没有意识到这一点，可能是因为您跳过了文档中的这一部分，您可以将您需要的任何内容放入控制器构造函数中。例如，如果我们有这样的代码:

```
from app.League import League

class Controller:

    def show(self, Request):
        return Request.redirect_to('settings.plans')

    def store(self, Request):
        league = League.find(Request.param('id'))
        league.name = Request.input('name')
        league.save()

        return Request.redirect_to('settings.plans') 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将`Request`对象抽象出来，然后放入控制器构造函数，如下所示:

```
from app.League import League

class Controller:

    def __init__(self, Request):
        self.request = Request

    def show(self):
        return self.request.redirect_to('settings.plans')

    def store(self):
        league = League.find(self.request.param('id'))
        league.name = self.request.input('name')
        league.save()

        return self.request.redirect_to('settings.plans') 
```

Enter fullscreen mode Exit fullscreen mode

你可能会发现这是一种更干净的方法，也更枯燥，因为我们只担心我们的`Request`对象的一行。

# 重复模型查找

过一会儿你会意识到你有一些代码看起来像这样:

```
from app.League import League

class LeagueController:

    def __init__(self, Request, View):
        self.request = Request
        self.view = View

    def show(self):
        league = League.find(self.request.param('id'))

        return self.view('league/show', {'league': league})

    def store(self):
        league = League.find(self.request.param('id'))
        league.name = self.request.input('name')
        league.save() 
        return self.request \
            .redirect_to('league.show', {'league': league})

    def delete(self):
        league = League.find(self.request.param('id'))
        league.delete() 
        return self.request \
            .redirect_to('discover') 
```

Enter fullscreen mode Exit fullscreen mode

请注意，每个方法都有相同的代码来按 id 获取联盟，并以不同的方式使用它。我们可以简单地通过在构造函数中抛出来解决这个问题:

```
from app.League import League

class LeagueController:

    def __init__(self, Request, View):
        self.request = Request
        self.view = View
        self.league = League.find(self.request.param('id'))

    def show(self):
        return self.view('league/show', {'league': self.league})

    def store(self):
        self.league.name = self.request.input('name')
        self.league.save() 
        return self.request \
            .redirect_to('league.show', {'league': self.league})

    def delete(self):
        self.league.delete() 
        return self.request \
            .redirect_to('discover') 
```

Enter fullscreen mode Exit fullscreen mode

这实际上清理了相当多的代码，如果所有的逻辑都将与单个联盟一起工作，那么让构造器在控制器上设置它是有意义的。

* * *

如果你想更深入地探索 [Masonite](%5BGitHub%20-%20MasoniteFramework/masonite:%20The%20Modern%20And%20Developer%20Centric%20Python%20Web%20Framework.%20Be%20sure%20to%20read%20the%20documentation%20and%20join%20the%20Slack%20channel%20questions:%20http://slack.masoniteproject.com%5D(https://github.com/MasoniteFramework/masonite)) ，一定要点击那个链接并给出一颗星或者加入 [Slack](http://slack.masoniteproject.com) 频道。