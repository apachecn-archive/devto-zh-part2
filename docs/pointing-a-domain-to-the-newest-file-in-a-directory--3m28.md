# 将域指向目录中的最新文件

> 原文：<https://dev.to/jackharner/pointing-a-domain-to-the-newest-file-in-a-directory--3m28>

这可能不是最好的解决方案，但在这种情况下，它对我有效。与其说是一个实际的教程，不如说是对我思维过程的描述。

我为 Shoolu.com 编写了一个 python 脚本，使用 BigCommerce API 来获取最新添加的产品并生成一个 XML RSS 文件。BigCommerce 确实有自动生成这些 RSS 提要的功能，但他们兼职支持，并为新商家删除了它，所以它看起来不像是可以依赖的东西。

## 巨蟒前来救援

我找到了 BigCommerce Python API，并使用它来获取创建日期最多在 7 天前的所有产品。然后，我遍历所有这些产品，在 RSS 文件中创建必要的条目。

这工作得很好，但我基本上只是覆盖了一个文件，丢失了任何前几周的新产品。我知道这很简单，只需在文件名中写入一个不同的文件，这样每次都是新的。

## 天堂里的烦恼

计划是通过 MailChimp 和他们使用 RSS 订阅源的能力，使用这个 RSS 订阅源每周生成一封“学校有什么新消息”的电子邮件。当前确定文件日期的方法的问题是，我将 Mailchimp 指向一个直接文件，所以我必须每周更新它，这有点违背了所有这些的目的。

## 意想不到的解决办法

我试着寻找一种方法让根域([rss.shoolu.com](https://rss.shoolu.com))指向目录中最新的文件。有几个建议，包括使用 php 获取最新的文件，重定向和其他一些不太优雅的解决方案。

没有一个能像好的解决方案那样让我开心。

我最终决定更新 python 脚本，只输出`index.xml`和`latestProducts-[date].xml`。这样,`index.xml`将会被当前准确的信息覆盖，我们也将会记录过时的备份。

为了简单起见，我只更新了域的 Apache 虚拟主机文件:

```
<directory /var/www/rss/public/>
    ...
    DirectoryIndex index.xml
</directory> 
```

Enter fullscreen mode Exit fullscreen mode

所以根域会直接指向文件。可能不是最优雅的解决方案，但在这种特定情况下它是有效的，所以我想我应该记录下来。

*最初发布在我的博客上:[指向目录中最新文件的域](https://harnerdesigns.com/blog/pointing-a-domain-to-the-newest-file-in-a-directory/)T3】*