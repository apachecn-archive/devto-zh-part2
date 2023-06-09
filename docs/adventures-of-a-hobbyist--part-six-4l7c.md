# 一个业余爱好者的冒险~第六部分

> 原文：<https://dev.to/link2twenty/adventures-of-a-hobbyist--part-six-4l7c>

# 缓慢的一周

## 这是什么？

我正在为一个开源项目学习 node，我决定记录我的旅程，以此来保持我的动力，并在时机成熟时获得有用的建议或帮助。

这里是这个系列以前的帖子，列表越来越长，所以我可能会开始只包括最后一个帖子，或者我可能会制作一个索引帖子。

*   [序言](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-one-2e0n)
*   [学习第一周](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-two-2g5a)
*   [思考`conf`文件](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-three-l1m)
*   [玩弄`MySQL`和`HTTP`T3】](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-four-2i9k)
*   [多想一点`conf`文件](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-five-532c)

## “缓慢的一周”是什么意思

正如你们中的一些人可能知道的那样，上周我在休假，这意味着我回到办公室后要赶很多进度，几乎没有时间做兼职项目，比如编程。

但是我仍然想做一些事情，即使它很小，所以我写了一个简单的节点脚本。

## 剧本是什么？

两周前，一个名为 [@markmatute](https://dev.to/markmatute) 的用户要求用节点练习来帮助他们学习，我想出了一个[简单的](https://dev.to/link2twenty/comment/4n3b)并给了他们。

简单的要点是一个 CLI 工具，它将用户名作为参数，并返回该用户最近发布的 10 篇 dev.to 帖子。另外，dev.to 有一个 RSS feed，谁知道呢。

我决定解决我自己的练习，我的意思是，这有多难。

## 代码

正如我之前说过的，我还在学习，所以如果你看到任何错误或只是代码马虎，请在评论中告诉我，这对学习体验真的很有帮助。

```
// https://www.npmjs.com/package/rss-parser
const RSS = require('rss-parser');
const rf = new RSS();

// third argument, which is the user input one
const arg = process.argv[2];

// function to take RSS data and log it to the screen
function print(result) {
  console.log(`Author: ${result.title}`);
  console.log(`Description: ${result.description}`);
  // only keep the first 10 items
  let items = result.items.length > 10 ? result.items.slice(0, 9) : result.items;
  //iterate through the items
  for (let item of items) {
    console.log('\n'+item.pubDate.substring(0, item.pubDate.length - 15) + ':');
    console.log(item.title);
    // only show this line if there is something on it
    item.content.trim() && console.log(item.content.trim().replace(/<[^>]*>/g, '').split('\n')[0].split(/\.|!|\?/)[0]);
  }
}

// start everything off by calling the function with 
// URL and argument
rf.parseURL(`https://dev.to/feed/${arg}`).then(print).catch(err => {throw err}); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这很简单，我使用`RSS-parser`读取 RSS 提要，然后循环遍历条目，并以我想要的格式打印出来。

## 输出

我用了 [@ben](https://dev.to/ben) 作为例子，他有很多帖子，所以很容易看出它是否有效。

```
node .\devtofeed.js ben 
```

Enter fullscreen mode Exit fullscreen mode

```
Author: Ben Halpern
Description: A Canadian living in New York, having a lot of fun cultivating this community! Creator and webmaster of dev.to.

Thu, 30 Aug 2018:
The Right Idea Becomes the Wrong Idea Over Time
2008: Don't build your server-side application with JavaScript

Tue, 28 Aug 2018:
Who's looking for open source contributors? (August 28 edition)
Please shamelessly promote your project

Sun, 26 Aug 2018:
Four Key Elements of a Healthy Framework Ecosystem
I wanted to share a great comment from this thread on Rails

Thu, 23 Aug 2018:
The Rails Ecosystem is Healthier than Ever
This is the first post in a new "flare tag" we're trying out: #healthydebate

Thu, 23 Aug 2018:
How to Host an "Ask Me Anything" on dev.to
I have been really happy to see more folks hosting AMAs

Mon, 20 Aug 2018:
Who's looking for open source contributors? (August 20 edition)
Please shamelessly promote your project

Sat, 18 Aug 2018:
What part of your first dev job were you least prepared for?

Fri, 17 Aug 2018:
Follow Friday! (v6)
Who have you been keeping up with on dev

Fri, 17 Aug 2018:
How many computers do you use?
Do you do everything on one laptop you carry around with you 
```

Enter fullscreen mode Exit fullscreen mode

## 注销

这真的是一个简短的帖子，下周，如果生活恢复正常，我计划看看节点路由器，然后可能是 js 路由器。感谢您的阅读。

狄米崔先生 56708300 次