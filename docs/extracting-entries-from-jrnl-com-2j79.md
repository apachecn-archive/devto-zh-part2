# 从 jrnl.com 提取条目

> 原文：<https://dev.to/seanlane/extracting-entries-from-jrnl-com-2j79>

几年前，我妻子开始在一个名为 LDSJournal.com(至少我相信那是这个名字)的在线服务上记录她的想法。大约两年前，这项服务被一家名为[jrnl.com](https://jrnl.com)的新网站收购。这似乎是一个相当简洁的服务，但我们关心的一件事是，如果帐户消失了，如何保存数据。

不幸的是，jrnl.com 唯一的出口选项似乎是下载 PDF 文件的能力，这是在你付费打印日志时创建的。根据同一消息来源，据称将有一个备份日志条目的选项，而不必购买物理副本，但目前已经过去了一年多，因为服务台文章承诺该功能将在 2017 年底前完成。除此之外，从他们制作的 PDF 文件中提取我妻子的作品可能会有很多潜在的问题，这取决于它是如何组合在一起的。考虑到这一点，我使用以下步骤来检索她的内容。

以与本文相似的方式:[Ian London:Web Scraping——发现隐藏的 API](https://ianlondon.github.io/blog/web-scraping-discovering-hidden-apis/),我使用来自 jrnl.com Web 应用程序的输出连接来识别它们的隐藏 API，并使用这些 API 来访问条目。登录到服务并导航到日记条目后，您可以查看浏览器发送到 jrnl.com 的请求标头，以检索条目和其他内容。API 键就是这些头中的一个，第一部分在下图中的`Authorization`下可见。

#### 获取 API 密钥

[![Getting the API Key](../Images/cfda684cf8802e4a4daa890862961f3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kW1eN6SE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sean.lane.sh/images/2018/08/jrnl1.png)

进一步的研究表明，API 的基本 url 是`https://jrnl.com/api/v1/`，条目的 API 端点是`https://jrnl.com/api/v1/entry`。使用名为[失眠症](https://dev.to/scottw/insomnia-rest-client-578d-temp-slug-9682618)的 REST API 工具，我们可以插入 API 键，并使用设置了限制选项的端点来允许返回更多条目:`https://jrnl.com/api/v1/entry?limit=250`。

然后，使用类似下面的 Python 脚本，您可以将帖子转换成可以在其他地方导入的格式。这个脚本是我用来准备导入到 [Ghost CMS 平台](https://ghost.org)的条目的，我是按照我上一篇文章中的说明建立的。将它放入 Ghost 还需要一点后期处理，如果您已经做到了这一步，那么跟随 Ghost 文档将帮助您完成剩下的部分。该脚本假设来自 jrnl.com 的条目被隔离并作为一个 JSON 数组保存在一个名为`posts.json`的文件中。

```
#! /usr/bin/env python3 
from datetime import datetime
import json, os

def millis(x):
  return int(datetime.fromisoformat(x).timestamp() * 1000)

with open('posts.json') as f:
  posts = json.load(f)

new_posts = []
for post in posts:
  temp = {
    'id': post['id'],
    'title': post['title'],
    'slug': post['title'],
    'html': post['content'],
    'image': None,
    'featured': 0,
    'page': 0,
    'status': 'published',
    'language': 'en_US',
    'meta_title': None,
    'meta_description': None,
    'author_id': 1,
    'created_at': millis(post['created']),
    'created_by': 1,
    'updated_at': millis(post['modified']),
    'updated_by': 1,
    'published_at': millis(post['entry_date']),
    'published_by': 1
  }
  new_posts.append(temp)

with open('new_posts.json', 'w') as f:
  json.dump(new_posts, f, indent=2) 
```

* * *

1.  [http://help desk . jrnl . com/kb/article/150-can-I-backup-my-jrnl/](http://helpdesk.jrnl.com/kb/article/150-can-i-backup-my-jrnl/)