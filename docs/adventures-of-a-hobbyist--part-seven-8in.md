# 一个业余爱好者的冒险~第七部分

> 原文：<https://dev.to/link2twenty/adventures-of-a-hobbyist--part-seven-8in>

# 玩弄路由

## 这是什么？

这是我学习经历的第七部分。我们的目标是学习 node，以便制作一个开源的 web 应用程序，供系统管理员用于日常任务。

如果你对阅读早期的帖子感兴趣，这里有[索引页](https://dev.to/link2twenty/adventures-of-a-hobbyist--index-4oj7)。

## 所以你的意思是`express`？

不，我知道`express.js`是一个东西，我知道人们喜欢它，但我想，至少从一开始，我会尝试制作一个定制路由器。我不打算让它做任何花哨的事情，只是一件简单的事情，以帮助我理解更大的概念。

### 起点

首先，我做了一个非常简单的概念证明。

```
const http = require('http');

function router(url) {
  let address = url.split('/')[1] || "index";
  return address;
}

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end(router(req.url));
}).listen(80); 
```

Enter fullscreen mode Exit fullscreen mode

它查看 URL 路径并将第一部分打印到屏幕上，如果没有任何内容，它将打印索引。超级简单，但证明我们可以使用一个函数来计算出什么需要打印。

### 加载文件

下一步是让它加载文件，而不仅仅是推送文本。

```
const http = require('http');
const fs = require("fs");

const pages = "pages";

function router(url) {
  let address = url.split('/')[1] || "index";
  let content;
  try {
    content = fs.readFileSync(`.\\${pages}\\${address}.html`, 'utf8');
  } catch (err) {
    content = fs.readFileSync(`.\\${pages}\\404.html`, 'utf8');
  }
  return content;
}

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html' });
  res.end(router(req.url));
}).listen(80); 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚使用了来自`fs`的`readFileSync`，它检查文件是否存在，如果存在，它将服务于文件，如果不存在，它将服务于 404 页面。

这种方法有几个问题。如果我尝试浏览到`http://localhost/home?beta=true`，我会得到一个 404，因为`.\pages\home?beta=true.html`打不开，但是锚确实工作。此外，也不支持像`http://localhost/api/userlist`这样的深层链接。

## 局限性

我已经提到过已经有了解决方案，所以我不打算尝试去克服这些限制。不过，如果你想告诉我一些解决问题的方法，我很乐意听听。

### 接下来是什么

下周我将看看`express.js`并尝试做一个快速的项目，我想也许是一个 MySQL 客户端来读取或写入数据。不过，如果你对我应该做的其他事情有什么建议，我会很乐意考虑的。

抱歉，我的帖子很短，希望我会很快回复到更长的帖子，当然，除非你更喜欢短的。
感谢阅读！🦄🦄🦄