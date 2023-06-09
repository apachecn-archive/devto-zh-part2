# 用 Gatsby.js 处理表单 V2 和 Netlify

> 原文：<https://dev.to/changoman/form-handling-with-gatsbyjs-v2-and-netlify-4ddd>

*本帖最初发表于[CodeBushi.com](https://codebushi.com/form-handling-gatsby-netlify/)T3】*

[![](img/fc535bfd8fd3eb67dbbb6b1995b28a94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rMlF4jf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4krle8lzsrmenr44scxc.jpg)

拥有一个有效的联系方式是许多网站的基本要求，但是建立一个静态网站可能会很棘手。如果你用 [Netlify](https://www.netlify.com/) 托管你的网站(你应该这样)，你可以免费使用他们令人敬畏的[表单处理功能](https://www.netlify.com/docs/form-handling/)。设置起来超级快速和简单，你永远不会想再跳圈或嵌入一个丑陋的形式。

对于这个例子，我将使用一个用 [Gatsby.js](https://www.gatsbyjs.org/) 创建的静态站点。你可以用几乎任何静态站点生成器来做这件事，只要确保你是用 Netlify 托管的。

#### [盖茨比现场带表单](https://gatsby-forms.netlify.com/) <small>( [查看来源](https://github.com/codebushi/gatsby-forms) )</small>

假设您的机器上安装了 Gatsby.js，让我们从克隆我之前制作的 [Gatsby starter](https://codebushi.com/gatsby-starters/) 开始。

```
# Create a new Gatsby site with the Forty starter
gatsby new gatsby-forms https://github.com/codebushi/gatsby-starter-forty

# Go into the new directory
cd gatsby-forms/

# Start the dev site, browse to http://localhost:8000/
gatsby develop 
```

Enter fullscreen mode Exit fullscreen mode

你的新 Gatsby 网站应该已经启动并运行了，向下滚动到页面底部查看联系表单。由于 Gatsby 是使用 React.js 构建的，所以这个表单区域只是一个简单的 React 组件。在您选择的文本编辑器中打开文件，`src/components/Contact.js`

下面是表单的初始 JSX:

```
<form method="post" action="#">
    <div className="field half first">
        <label htmlFor="name">Name</label>
        <input type="text" name="name" id="name" />
    </div>
    <div className="field half">
        <label htmlFor="email">Email</label>
        <input type="text" name="email" id="email" />
    </div>
    <div className="field">
        <label htmlFor="message">Message</label>
        <textarea name="message" id="message" rows="6"></textarea>
    </div>
    <ul className="actions">
        <li><input type="submit" value="Send Message" className="special" /></li>
        <li><input type="reset" value="Clear" /></li>
    </ul>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的就是给`<form>`标签添加一些新的属性:

```
<form name="contact" method="post" data-netlify="true" data-netlify-honeypot="bot-field">
  <input type="hidden" name="bot-field" />
  <input type="hidden" name="form-name" value="contact" />
    ...
</form> 
```

Enter fullscreen mode Exit fullscreen mode

第一个隐藏区域`bot-field`是 Netlify 的机器人可以选择的。第二个`form-name`隐藏字段是特定于 Gatsby 的，因为 Gatsby 删除了不包含在 JSX 表单中的输入字段。

就是这样！这是让表单提交工作的最简单的方法。Netlify 的机器人会在您部署时自动检测属性`data-netlify="true"`，并为您处理表单。`data-netlify-honeypot="bot-field"`是可选的，但它是用于反垃圾邮件的，很容易包含进来。如果垃圾邮件机器人填写了隐藏的`bot-field`输入，那么表单不会被提交。

现在只需用这些文件创建一个新的 GitHub 存储库并[部署到 Netlify](https://www.netlify.com/docs/continuous-deployment/) 。

转到您的新站点并提交表单。您应该会被引导到一个通用的成功页面，我们可以稍后再考虑对此进行更改。Netlify 的后端会有一个表单区，你可以在那里看到提交的内容。您还可以进入`Settings > Forms > Form notifications`，在新提交的内容出现时随时收到电子邮件提醒。相当牛逼！

#### [成品](https://gatsby-forms.netlify.com/) <small>( [查看来源](https://github.com/codebushi/gatsby-forms) )</small>

你也可以添加一个自定义的`Success`页面，查看[原帖](https://codebushi.com/form-handling-gatsby-netlify/)了解更多详情！