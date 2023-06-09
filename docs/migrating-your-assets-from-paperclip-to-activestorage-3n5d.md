# 将您的资产从回形针迁移到动态存储

> 原文：<https://dev.to/kylekeesling/migrating-your-assets-from-paperclip-to-activestorage-3n5d>

随着 Rails 5.2 的发布，现在有了一种称为 [ActiveStorage](http://edgeguides.rubyonrails.org/active_storage_overview.html) 的本机内置方式来处理资产上传和管理，这使得需要使用像[回形针](https://github.com/thoughtbot/paperclip/)、 [Carrierwave](https://github.com/carrierwaveuploader/carrierwave) 或 [Fog](https://github.com/fog/fog) 这样的宝石，回形针甚至可以编写[迁移指南](https://github.com/thoughtbot/paperclip/pull/2568)；暗示着这个世界可能不久了。

老实说，在我开始写这篇文章之前，我甚至不知道 Paperclip 已经编写了一个[迁移指南](https://github.com/thoughtbot/paperclip/blob/master/MIGRATING.md)，尽管它相当全面，但它并没有完成我认为最重要的任务之一——迁移远程主机上的文件。

考虑到这一点，我非常成功地使用了下面的 rake 任务来为我的许多模型移动资产。

这里有两种风格，迁移资产的同时改变它们的名字，或者只是简单的移动。

## 移动资产，同时更改其名称

在我的例子中，我有一个名为`headshot`的带有回形针附件的`User`类。我从来不喜欢我们用爆头这个词，所以这是一个绝佳的机会把它改成`avatar`。