# 闪烁(1) Github 状态

> 原文：<https://dev.to/coreyja/blink1-github-status-eff>

**原帖[coreyja.com](https://coreyja.com/blink-1-github-status/)2018-04-29**

不久前，我得到了一个 [blink(1)](https://blink1.thingm.com/) ，这是一个很酷的小 USB 灯，完全可编程。虽然玩起来很有趣，但我还没有怎么用过它。最近我和一些同事聊天，意识到它的一个很好的用途是作为 Github 状态指示器，告诉我特定 Github 分支的状态。我会让它跟踪我们的总分行并报告 CI 状态。

现在它把灯变成红色表示失败，绿色表示成功，紫色表示失败。如果状态为“未决”,它将闪烁黄色，然后显示先前状态的颜色，并根据状态已处于未决状态的时间变暗。

在我们走得太远之前，这里有一个回购的链接:[https://github.com/coreyja/blink1-github-status](https://github.com/coreyja/blink1-github-status)

# Github 状态 API

第一个版本使用 v3 Github API 来检查状态端点。这工作得很好，但是不支持我想的用例。如果当前状态是 pending，我希望显示最后一个构建的状态，但是根据这个挂起的构建已经挂起了多长时间来使它变暗。越近越亮。这意味着浅绿色的灯将表明测试已经运行了一段时间，并且最后一次运行是成功的。

根据这一要求，我们还需要查看提交的历史及其状态。从这里开始，我决定使用 v4 GraphQL API。我当然可以从 v3 API 中得到我想要的东西，但是我想尝试一下 GraphQL。

# GraphQL

GraphQL 是一种编写 API 的新方法，它不是提供多个端点，而是提供一个端点，消费者可以通过正确的查询从 API 中获取所需的数据。因此对于这个例子，我需要编写一个 GraphQL 查询来获取数据，而不是找到正确的 API 来获取分支状态的历史。进行这项工作时最不可或缺的工具是 Github 的 [Explorer](https://developer.github.com/v4/explorer/) ，它是 [GraphiQL](https://github.com/graphql/graphiql) 的一种用法。这提供了一种实时方式来编写查询并查看 Github 将返回什么数据。在我把它集成到我的程序中之前，我是这样处理我的查询的，以使它正确。

最终，我找到了一个适合我正在寻找的信息的查询。它会为给定的回购查找指定的分支(例如:“主”)。从这里开始，它将查看最后 N 次提交，并包括对提交的任何状态检查。它包括提交的整体状态，以及单个状态`contexts`。以下是我正在使用的查询:

```
query($owner:  String!,  $name:  String!,  $branch:  String!,  $pageSize:  Int,  $cursor:  String){  repository(owner:  $owner,  name:  $name)  {  ref(qualifiedName:  $branch)  {  target  {  ...  on  Commit  {  history(first:  $pageSize,  after:  $cursor)  {  pageInfo  {  hasNextPage  endCursor  }  nodes  {  oid  status  {  state  contexts  {  state  context  createdAt  }  }  }  }  }  }  }  }  } 
```

我在查询中有一个还没有实现的基础就是分页。当前版本只获取一页结果，但是分页的基础已经内置在上面的查询中。

## 眨眼(1)

我有一个闪烁(1) m2，它有两个可以独立控制的发光二极管。与闪现(1)互动的红宝石是 [rb-blink1](http://ngs.github.io/rb-blink1/) 。不幸的是，它目前不支持独立设置两个 led。它是官方 C API 的包装器，也是 CLI 工具的动力。CLI 工具确实支持设置每个 LED，而且 gem 最近没有更新。因此，gem 可以更新以支持两种 led，这是我有兴趣在未来工作的项目！

## 代码和 Crontab

不出所料，我决定用 Ruby 实现它。它目前是一个 Ruby 脚本，提取了一些类，但是我想在将来使它成为一个可执行的宝石。显然还需要做更多的提取和建模工作，因为脚本目前完成了大部分繁重的工作。我不想将它实现为任何我必须保持活动的服务，所以它是一个简单的脚本，我目前正在通过 cron 运行它。将来我可能会考虑通过 launchd 来运行它，但是 cron 让我更快地运行起来。

cron 的一个奇怪之处是，它不会在 PATH 和其他 env 变量设置为您期望的交互式终端的情况下运行命令。因此，经常需要可执行文件的完整路径。因为这只是一个 ruby 脚本，而不是一个独立的可执行文件，所以我不得不跳过一个小圈来设置它。我需要从 cron 运行类似`bundle exec ./exe/run_light.rb coreyja/glassy-collections master`的东西。

我遇到的问题是`bundle`不在 cron 运行的有限路径中。我的解决方案可能有点矫枉过正，但它完成了工作，直到我做出一个正确的可执行文件。我使用`bash -c`创建一个子 shell。在这个目录中，我将 bash_profile，cd 放入项目目录，然后从那里我可以运行我的`bundle exec`命令。我还将`BLINK1_GITHUB_TOKEN`直接包含在 crontab 条目中，因为它不在 bash_profile 中。

这是我在 crontab 中的当前条目。我目前设置它每 5 分钟运行一次。我还将所有东西都通过管道传输到/dev/null，这样它就不会在每次运行时都向我发送终端邮件。理想情况下，它可能只输出一个错误，所以我不能捕获所有的输出，让邮件表明有一个问题。

```
*/5 * * * * bash -c "source ~/.bash_profile && cd ~/Projects/blink1-github-status && BLINK1_GITHUB_TOKEN=FAKE_TOKEN bundle exec ./exe/set_light.rb coreyja/glassy-collections master" &>/dev/null 
```

## 
  
未来

除了让这成为一个瑰宝，我还想研究创造一个家酿水龙头用于销售。有一些自制宝石项目，我很快发现，看起来很有趣。因为我使用 RBenv 来管理多个 ruby 版本，所以很难将 gem 作为全局可执行文件来安装。一些选项包括为你使用的每个 ruby 版本安装 gem，或者一些聪明的 bash 别名，这两个我都不太喜欢。使用自制软件安装用 Ruby 编写的全局可执行文件对我来说是一个更好的解决方案，所以我有兴趣尝试一下。

我想重复的另一件事是给 GraphQL 查询添加分页。我将构建块添加到我编写的查询中，现在我想利用它们来确保获取我需要的所有提交。现在，如果在 1 页提交中没有发现任何状态，灯将变成紫色，表示没有发现任何状态。在未来，我希望它能够继续要求更多的提交，以找到最近的一个状态。希望在这个用例中，您不会在 master 上有很多未测试的提交；-)但我还是想实现分页。