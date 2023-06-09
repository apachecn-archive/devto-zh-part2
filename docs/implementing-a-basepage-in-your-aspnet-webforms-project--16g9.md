# 在 ASP.NET web forms 项目中实现基页

> 原文：<https://dev.to/hardkoded/implementing-a-basepage-in-your-aspnet-webforms-project--16g9>

我开始准备两篇帖子，一篇关于页面会话，另一篇关于减少 QueryString、ViewState 和会话样板。但是我意识到这两种解决方案都是在一个基础页面上实现的。所以我觉得我首先需要写一下在一个项目中实现一个 BasePage 的重要性。

## 什么是 BasePage？

我打赌你已经注意到你所有的页面都继承自[页面](https://msdn.microsoft.com/en-us/library/system.web.ui.page%28v=vs.110%29.aspx)。该实现有三个目标:

*   ASP.NET 运行时知道如何实例化一个页面类并与之交互。
*   Page 类为我们做了所有艰苦的工作，不亚于将我们的页面转换成 HTML。
*   它为我们提供了让我们更有效率的工具。例如，它提供了一种在视图状态中持久化数据的简单方法，或者帮助我们确定我们是否在回发中。

> 如果我们制作自己的页面类，这样我们就可以在工具箱中添加更多的工具，会怎么样呢？

## 怎么才能做到？

实现很简单。我们只需要一个从 Page 继承的类。

```
public class BasePage : Page
{

} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要一页一页地去做(好的，你可以使用全部替换)，改变你所有的页面，所以它们继承自 Basepage 而不是 Page。

所以我们把这个:

```
public class default: Page 
```

Enter fullscreen mode Exit fullscreen mode

变成这样

```
public class default: BasePage 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可以给自己的工具箱增加什么？

在这个主页上，您可以做很多事情。正如我之前提到的，我将写一些关于一些解决方案的帖子，如 PageSessions 和 reducing QueryString 样板文件。但是，现在，让我给你一些想法。

### 使用户数据全面可用

你需要用户在每一页上的全名吗？将样板文件移到你的首页。

```
public class default: BasePage
{
    public string UserFullName
    {
        get
        {
            return Session[userfullname].ToString();
        }
        set
        {
            Session[userfullname] = value;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 改变线程文化

如果你想为全球用户服务，你需要考虑他们的文化。根据[这篇维基百科文章](https://en.wikipedia.org/wiki/Date_format_by_country)，如果你输入 2018 年 1 月 2 日，大约 6 亿人会读到“2018 年 1 月 2 日”，而 35.65 亿人会读到“2018 年 2 月 1 日”。
如果你知道用户的文化，你可以在当前线程中设置它。然后，所有的`ToString`方法将使用该文化来正确地呈现日期和数字。

```
Thread.CurrentThread.CurrentCulture = Thread.CurrentThread.CurrentUICulture = new CultureInfo(userCulture); 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览器间的全线通讯

假设您有一个购物车应用程序，需要更改样式。这可能是因为购物车已满，用户已登录，或者用户来自其他国家。您可以在 form 元素中设置类，然后在 CSS 样式表中使用它们。

```
Form.Attributes["class"] = 
    mainElement.Attributes["class"] + " " +
    (HasItemsInCart() ? "items-in-cart" : string.Empty); 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的 CSS 中你可以这样做

```
.items-in-cart .some-cart-div
{
    display: block;
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以声明所有你需要的 javascript 变量。假设你了解用户文化，你想利用它；不仅在服务器端，而且在客户端站点。您可以在所有页面上声明一个 javascript 变量。

```
ScriptManager.RegisterClientScriptBlock(
    this, 
    GetType(), 
    "currentCulture", 
    $"var currentCulture = '{userCulture}';", 
    true); 
```

Enter fullscreen mode Exit fullscreen mode

### 通用方法

我打赌你已经写了一个叫做`FindControlInAllControls`的方法，它试图使用递归循环在一个控件数组中找到一个控件。BasePage 是实现这一点的好地方。

### 压缩你的视图状态

你可以在你的基本页面中实现这个视图状态压缩。这也是斯科特·汉瑟曼推荐的[。](https://www.hanselman.com/blog/ZippingCompressingViewStateInASPNET.aspx)

### 页面会话

PageSessions？那是什么？我将写一篇关于页面会话的文章，你可以在你的 BasePage 上实现它。

### 减少查询字符串/会话/视图状态样板文件

我讨厌用于从 QueryString、session 和 ViewState 同步和初始化变量的样板文件。我将张贴一些想法，以消除这些样板使用....是的，你猜对了！，你的主页。

## 最后的话

依赖注入和扩展方法在现代开发中可能已经变得流行，但是正如我们在这个 Basepage 练习中所看到的，继承仍然有很多可以提供的。

最初发布于[harkoded.com](http://www.hardkoded.com/blogs/implementing-base-page-aspnet-project)