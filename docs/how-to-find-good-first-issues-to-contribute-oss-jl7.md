# 如何找到贡献 OSS 的好的第一个问题

> 原文：<https://dev.to/ohbarye/how-to-find-good-first-issues-to-contribute-oss-jl7>

*这篇文章基于[我题为“如何找到好的首发”](https://speakerdeck.com/ohbarye/how-to-find-good-first-issues)的演讲。*

## 目标读者

如果你能做到以下几点，这篇文章会对你有所帮助:

*   想贡献 OSS 吗
*   正在努力寻找一个存储库或一个问题来贡献
*   (你更喜欢主流/流行的操作系统吗😇)

老实说，目标只有我。我是一个 OSS 新手，想知道我能为 OSS 做些什么。

## 开好第一期单子

在这篇文章中，我将介绍一个技巧，通过一个简单的脚本来找到*好的第一个问题*，并制作一个问题列表，如下所示。

[![list](img/2c5d745aa66ba98dede0b2d88e75acb7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2rpIc53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/41986183-557969d4-7a70-11e8-9e27-55e3fb45f471.png)

这个列表有大量的问题等待初学者的贡献。你可以在这里以电子表格[的形式查看完整列表](https://docs.google.com/spreadsheets/d/1-2jhSCFZUWyFsubCnGiX-xui9nA76M783Z2SOfVrEU4/edit#gid=440574128)

### BTW，什么是「好的第一期」？

它是 GitHub 默认提供给每个存储库的一种标签名称。

[![labels](img/a443c24eaa293c8cd77bc6c35aeae167.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YdzzfSio--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/41986182-554eba68-7a70-11e8-8eb3-209fb60aceb2.png)

根据[官方](https://help.github.com/articles/helping-new-contributors-find-your-project-with-labels/)的说法，这个标签表示如下。

> 将`help wanted`和`good first issue`标签应用到存储库中的问题上，以突出人们为您的项目做出贡献的机会。

## 如何列出好的第一期

为了列出这些问题，我写了一个名为 *goofi* 的简单脚本，并发布在 GitHub 上:【https://github.com/ohbarye/goofi】T2

它只做以下三件事。

1.  调用 GitHub GraphQL API 获取问题
2.  格式化其响应
3.  创建 CSV

容易吗？

### GraphQL 查询

尽管我可以用 GitHub API v3 REST 版本做同样的事情，但最好使用 GraphQL API，这样我可以避免 N+1 查询问题。

以下是脚本运行的查询。它提取问题:

*   其知识库拥有超过 500 颗恒星。
*   其存储库中有 1 个以上标记为`good first issue`的问题。
*   谁的存储库的语言是`javascript`。
*   标记为`good first issue`。

```
{  search(first:  100,  query:  "language:javascript good-first-issues:>1 stars:>500",  type:  REPOSITORY)  {  repositoryCount  pageInfo  {  startCursor  endCursor  hasNextPage  }  nodes  {  ...  on  Repository  {  owner  {  login  }  name  url  issues(first:  100,  labels:  ["good first issue"],  states:  OPEN,  orderBy:  {field:  UPDATED_AT,  direction:  DESC})  {  totalCount  nodes  {  title  url  }  }  stargazers  {  totalCount  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这里让我感到惊讶的是，GitHub API 可以处理相当特定的过滤器。

*   "良好的首要问题:> 1 "
*   "星级:> 500 "

如果你对 API 规范感兴趣，请看[https://help.github.com/articles/searching-repositories/](https://help.github.com/articles/searching-repositories/)。

### 响应

这是响应的一部分，上面的查询结果如下。

```
{  "data":  {  "search":  {  "repositoryCount":  196,  "pageInfo":  {  "startCursor":  "Y3Vyc29yOjE=",  "endCursor":  "Y3Vyc29yOjEwMA==",  "hasNextPage":  true  },  "nodes":  [  {  "owner":  {  "login":  "vuejs"  },  "name":  "vue",  "url":  "https://github.com/vuejs/vue",  "issues":  {  "totalCount":  4,  "nodes":  [  {  "title":  "warn if $set is used on a property that already exist",  "url":  "https://github.com/vuejs/vue/issues/8129"  }  ]  },  "stargazers":  {  "totalCount":  105267  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 格式化数据~写入 CSV

一旦我们可以通过 GitHub API 轻松获取数据，格式化数据就不是一件难事了。

```
writeIssues(repository) {
  const owner = repository.owner.login;
  const name = repository.name;
  const stars = repository.stargazers.totalCount;

  repository.issues.nodes.forEach((issue) => {
    const title = issue.title;
    const url = issue.url;
    this.writer.write({owner, name, stars, title, url});
  });
}

const nodes = response.data.data.search.nodes;
nodes.forEach(this.writeIssues); 
```

Enter fullscreen mode Exit fullscreen mode

## 然后呢？

现在我们有了候选人名单，你所要做的就是一个一个地检查问题，找到你可以做出贡献的点。

* * *

## 另辟蹊径

我最初使用的是非常酷的 GitHub 问题阅读器 Jasper T1，通过创建一个收集问题的流来找到这些问题。

[![jasper](img/0f35ec3615df29525a20ec11decefd27.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e3YaF51d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/41986181-5526dd72-7a70-11e8-9bca-d513e7e36d15.png)

[![jasper_stream](img/12b4891365ea0e46c66f978990b8f63d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HnVfmLii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/41986180-54fbcdee-7a70-11e8-96f3-dab0c9dbda51.png)

但是...

*   它不能根据恒星数量对储存库进行分类
*   它不能拒绝“良好第一发行”的错误用法
*   每当有问题更新时，它都会通知我

简而言之，这种方式不适合我，因为对我来说太吵了。

* * *

## 他们真的是很好的方式吗？🤔

至少，我可以贡献一些流行的库，即使我最初并不熟悉它们。

例如 Node.js

[![contribution](img/462a9887b0097c1540df124af23937e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_fDE4K3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/41986179-54cdb3be-7a70-11e8-9b31-fb30d739e9e1.png)

## 下一次尝试

我愿意尝试建立脚本的 GUI 部分，以便每个人都可以在任何时候找到好的第一期，当他们想作出贡献。一旦我完成了，我会写另一篇文章！

* * *

让我们找到你的“好的首要问题”,为让这个世界变得更好做出贡献！💪