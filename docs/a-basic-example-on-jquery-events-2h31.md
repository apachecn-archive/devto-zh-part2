# jQuery 事件的一个基本例子

> 原文：<https://dev.to/tallmanlaw/a-basic-example-on-jquery-events-2h31>

jQuery 是一个 JavaScript 库，在对页面元素的事件驱动响应方面非常突出，允许用户通过文本输入与页面进行交互。下面是一个简单的例子。

首先，构建一个简单的 HTML 页面，允许对现有的姓名列表进行搜索、添加和删除功能。列表被硬编码为一个数组，然后允许数组方法与事件方法相结合。HTML 文件如下所示:

[![](../Images/ec95eea143a9efe0f773eb4c78b0995d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9zSVnYZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4tjglwu4jcmbs82gzla0.png)

接下来，编写一个简单的 CSS 文件。下面的代码是唯一必要的代码，稍后将与搜索按钮结合使用，以提供页面颜色变化。输入列表中的现有名称并单击搜索后，颜色将变为蓝色。

[![](../Images/3be1f97f4168e16c45fd1a152cecb5c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3C4-AaCe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xga8d65f5axhsi5nqgsg.png)

这个名为 student list 的列表编码在下面的 app.js 文件中。

[![](../Images/48728fea5ef9eb2030a7edbe524dc56a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EGIzddUQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o36t5o9kn83gdpbasl4p.png)

然后，创建一个呈现函数，允许列表呈现到输入字段下面的页面。

[![](../Images/b6ea70cff9d65a5f62c9279c4ca11fd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QAmNtWU9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bjel37rwe52briiyix82.png)

使用 jQuery 事件方法“on”将添加、搜索和删除功能添加到代码中，如下所示。

[![](../Images/719d41e728aeb483559d94766f340996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PXGsbShy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/si02mz17t7d5jhoblqqh.png)

就这么简单。现在，您可以向现有列表添加名称，这些名称将呈现在现有列表底部的页面上。您可以从现有列表中删除姓名，新列表将呈现在页面上，但不包含已删除的姓名。或者，您可以在现有列表中搜索姓名。在您从现有列表中输入一个名称并单击“搜索”按钮后，页面主体将变为蓝色。

在输入文本或单击任何按钮之前，学生列表的页面呈现如下所示。一些额外的代码被添加到这个简单样式的 CSS 文件中。

[![](../Images/a62ed2f035b00b6d9dfaf2d6c181cedd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qi6eRbGx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x3dmk7u5x1h7x8qy9wss.png)

在字段中输入现有列表名称并单击 search 按钮后，页面呈现如下。

[![](../Images/14e43ab34641125810eabae0eeda89ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oLh_6UpF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/af7z8l58dsey6al4czrd.png)