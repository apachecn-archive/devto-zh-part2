# 增强您的电子邮件工作流程的 3 个工具

> 原文：<https://dev.to/joshzaldana/3-tools-to-enhance-your-email-workflow-55l6>

[![Alt text of image](img/5e2c78cd08e61bcc77d3c71d9fe8fc43.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aTyKLhIS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/fN8tMK/programmer.jpg)

### **#1 使用任务运行器**

Gulp 是一个很棒的工具，它可以让你在每次创建活动时，自动完成你发现自己正在做的任何开发工作。例如，如果你发现自己从以前的电子邮件中复制并粘贴 HTML 代码到你的下一个活动中，点击⌘+F 用新版本替换所有以前的副本，哦，你想更新标题字体大小-这是另一个⌘+F.除了耗时，这个工作流程提供了大量人为错误的机会-谁没有关闭表格单元格？以这种方式构建电子邮件的另一个痛点是，代码错误往往会随着时间的推移而加剧；不像那些好的共同基金，这些类型的收益没有正回报。

那么，任务跑者有什么帮助呢？任务运行器的核心是...自动为您运行任务；就这么简单。花了几个小时(有些人花了几天)的事情只需要几分钟，这都要感谢一个任务跑者。当设置正确时，这些自动化的工作流还允许您快速排除任何错误，例如，如果您的代码缺少关闭的表格单元格标记，您会收到通知。

[了解如何设置 Gulp 任务运行器](https://github.com/gulpjs/gulp/blob/v3.9.1/docs/getting-started.md)

### **#2 杠杆模板**

所以你已经在用任务运行器了？太好了，我推荐的下一步是整合模板的使用。如果你真的想最大限度地利用任务运行器，我强烈推荐你寻找一种适合你开发需求的模板语言；我目前用的是[双截棍](https://mozilla.github.io/nunjucks/)。

这些模板语言并不难使用，而且很容易学习。这些模板语言的真正强大之处在于它们的定制。比起使用其他人的模板，你可以根据自己的需要构建模板，并轻松复制它们。下面是一个基本模板的示例:

```
{% extends "base.html" %}

{% block header %}
<h1>{{ title }}</h1>
{% endblock %}

{% block content %}
<ul>
  {% for name, item in items %}
  <li>{{ name }}: {{ item }}</li>
  {% endfor %}
</ul>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

你看到那个嵌入的循环了吗！？您的模板可以包含所有六行，或者您可以只使用一行代码来查找您的。json 文件引用并构建所需的行——这是处理多个项目时的关键。

[了解如何开始使用 Nunjucks 的更多信息](https://mozilla.github.io/nunjucks/getting-started.html)

### **#3 思考模块**

还记得玩乐高吗？电子邮件的构造很像那些乐高玩具；包括多个部件，每个部件相互配合。看一看你创建的最后一封邮件——你看到你的内容存放的主要区域了吗？包含以下内容的基本电子邮件模板可以分为三个部分——如果您想真正压缩您的模板，可以分为两个部分。

*   头条新闻
*   英雄形象
*   身体
*   CTA 按钮

下面是我如何分解它们的方法

```
- sectionBlock01 (contains headline)
- sectionBlock02 (contains hero image)
- sectionBlock03 (contains body and CTA button) 
```

Enter fullscreen mode Exit fullscreen mode

使用这三个`sectionBlock`,我可以在几秒钟内创建任何电子邮件——不管我的`sectionBlock03`包含多少行——循环会处理这些工作。

现在你有了 3 个工具来增强你的电子邮件工作流程，让你走上正确的道路，成为一名高效的开发人员。