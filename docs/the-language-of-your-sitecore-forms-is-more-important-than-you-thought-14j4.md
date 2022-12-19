# 你的 Sitecore 表单的语言比你想象的更重要

> 原文：<https://dev.to/jermdavis/the-language-of-your-sitecore-forms-is-more-important-than-you-thought-14j4>

我时常会遇到一个问题，这个问题就在文档中，但不知何故我却忽略了。本周的话题是 Sitecore 新的 V9 表单实现，以及它与语言的关系…

_ **编辑补充:** _ Sitecore 似乎已经设法在我开始写这篇文章和发布它之间的时间推出了一个修复程序…如果你部署 [v9.0 更新 2](https://dev.sitecore.net/Downloads/Sitecore_Experience_Platform/90/Sitecore_Experience_Platform_90_Update2.aspx) ，根据发行说明，这个问题应该会得到解决。但是我还是会把这个贴出来，因为不是每个人都会马上升级…

如果您的默认内容语言不是“`en`”，并且您试图创建一个新的 Sitecore 表单，您可能会有点困惑。我在一个默认为“`en-GB`”的网站上创建表单:

[![](../Images/177ed029986e4537d5ef7d6a106e2e6a.png)T2】](https://jermdavis.files.wordpress.com/2018/06/defaultlanguage.png)

它是在配置中设置的:

[![](../Images/465c537af9ff4fc4452fa21516e55f78.png)T2】](https://jermdavis.files.wordpress.com/2018/06/sitelanguageconfig.png)

我很乐意通过闪亮的新表单编辑器创建一个新表单，并保存它:

[![](../Images/fb6fe3f23c195c4ead0de356747b8068.png)T2】](https://jermdavis.files.wordpress.com/2018/06/savenewform.png)

表单将出现在内容树中:

[![](../Images/7a925b5037490a1e11a07527304dba81.png)T2】](https://jermdavis.files.wordpress.com/2018/06/formiscreated.png)

但是当您回到设计器中的“选择要编辑的表单”视图时:

[![](../Images/0978d921793d09309537684e47e21d0e.png)T2】](https://jermdavis.files.wordpress.com/2018/06/formismissing.png)

它去哪儿了？暗示一些挠头…

现在有一些关于[的](https://sitecoresandbox.com/2018/03/09/there-are-no-forms-to-display-in-sitecore-9-forms-designer/)[博客帖子](https://citizensitecore.com/2017/11/19/sitecore-9-forms-form-doesnt-show-up-after-creation/)讨论了当你试图返回表单时表单不可见的问题。但在我的具体案例中，这些都不是问题。

因此，我注意到，即使站点的默认语言是“`en-GB`”，填充“选择表单”UI 的 ajax 请求也是:

```
http://site/-/item/v1/sitecore/shell?search=&searchConfig=%7B60F35FD9-88CB-4DF5-8E78-1E9BF5FE181C%7D&sc\_content=master&language=en&fields=\_\_Created%7C\_\_Updated%7C\_\_Thumbnail&pageIndex=0&pageSize=25

http://site/-/item/v1/sitecore/shell?search=&searchConfig=%7BA7C51A03-028A-4414-98DF-116DFAEAE5A6%7D&sc\_content=master&language=en&sorting=d\_\_smallcreateddate&fields=\_\_Created%7C\_\_Created%20by%7C\_\_Updated%7C\_\_Updated%20by%7CClassification%7C\_\_Thumbnail&pageIndex=0&pageSize=50 
```

和

```
http://site/-/item/v1/sitecore/shell?sc\_itemid=%7BAC27A304-1EED-487B-965E-C993C561C6A7%7D&sc\_database=core&language=en 
```

那些有什么特别的？它们都包含子句:"`language=en`"，并且我们知道(由于我们的默认语言)我们的表单项目没有该语言的版本:

[![](../Images/35dae5119bc5e0a5026f2be27238003a.png)T2】](https://jermdavis.files.wordpress.com/2018/06/actuallanguages.png)

那么，如果我们只是在“`en`”语言中添加一个版本会发生什么呢？很快，我们可以看到:

[![](../Images/d2db09f61f43ebf14d902172c5eafc5e.png)T2】](https://jermdavis.files.wordpress.com/2018/06/formappears.png)

成功！

现在，只有表单的根项需要更新，以使表单可见:

[![](../Images/a57de27d9cb83daec4d6aa31f1274f7c.png)T2】](https://jermdavis.files.wordpress.com/2018/06/rootformitem.png)

如果您实际上想要表单本身具有适当的语言变体，您可以使用表单编辑 UI 来决定您想要编辑哪种语言:

[![](../Images/e7c41a48d8e7f609ed08aeb2aca13446.png)T2】](https://jermdavis.files.wordpress.com/2018/06/formlanguagechoice.png)

您不需要通过内容编辑器手动创建任何其他版本。但是当然你首先需要表单在表单编辑器中是可见的，这样才能工作…

现在，在 Sitecore 的表单编辑器文档中提到了这个问题:

[![](../Images/006d6c9118e693e6a65f16a464d61dc2.png)T2】](https://jermdavis.files.wordpress.com/2018/06/formsdocmention.png)

但是，如果你的默认语言不是“`en`”，那么按照指示“*首先用默认语言*保存表单”就说起来容易做起来难了——因此，在我们得到一个更新来修复用户界面搜索查询的语言选择之前，你可能不得不继续手动添加表单项目的“`en`”版本…