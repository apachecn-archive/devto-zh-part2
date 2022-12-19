# 编写让你的队友生活更轻松的代码

> 原文：<https://dev.to/nbau21/writing-code-that-makes-your-teammates-lives-easier-2e8>

独自工作=/=在团队中工作。你现在的代码中可能有些东西对其他开发人员来说不容易理解，包括你未来的自己。这不是花一个小时自动完成一项无聊的任务。这些提示都是简单的小变化。简单到你不需要重新连接你的大脑或者改变你的工作流程👍

PS:这是我的[博客](https://blog.codingdoodles.com/writing-code-that-makes-your-teammates-lives-easier/)的交叉帖子。

TL；博士；医生

*   编写良好的提交消息
*   向 hacky 实现添加注释
*   编写自我记录的代码/变量/方法/类，而不是添加注释

附言:这些建议对我长期从事业余项目很有帮助。我过去常常花一两个小时来加快我的业余项目。现在只需要 15 分钟左右！

PPS:编写一份包含安装和使用指南的自述文件会有所帮助:)

### 写好提交消息

提交很好，它提供了项目的背景。在一个理想的世界里，人们可以通过历史来理解为什么会发生某些变化以及如何发生的。

不要

```
- Refactor
- Fix bug
- Fix bug
- Cleanup
- Remove test [0]
- Add feature 
```

这里有很多问题。程序员做了什么`refactor`？修复了哪些 bug？清理了什么？删除了什么测试？做了什么功能？

[0]当我仔细检查删除了什么测试时，发现它意味着一个“测试”字，不知何故混入了一些 css 文件中，而不是删除了一个实际的测试。

做:

```
- Refactor for loop to use .filter
- Handle edge cases
  - Don't empty cart when user logs out
  - Prevent crashes when more than 3 tabs open
- Remove redundant and unused code
- Implement feature X 
```

这种写作风格分享了该项目的生活日记。这使得任何人都可以很容易地通过查看日志找到相关的变化(例如:`git log`)。Chris Beams 写了一篇关于[如何写提交](https://chris.beams.io/posts/git-commit/)的文章。

### 给 hacky 实现添加注释

hacky 代码的不好之处在于，它通常没有文档记录，并邀请其他开发人员在它的基础上进行构建。它变成了没有人理解或知道的东西。团队中的新成员将尽可能避免接触这些代码，并试图绕过这些代码。

不要

```
my-class {
  ...
  flex-direction: column;
  ...
} 
```

我们小小的代码可能会变成更大更坏的东西。它的用途可能并不明显，也不清楚它在今天是否仍然相关。绕过它会耗尽宝贵的开发时间。

做:

```
my-class {
  ...
  flex-direction: column; // hack to center overflowing text in IE 10 https://stackoverflow.com/a/42302827
  ...
} 
```

几个月或几年后，你的项目可能不再支持 IE 10 了。我们现在可以删除这个黑客，现在我们不必担心这个黑客的其他副作用。

### 编写自我记录的代码/变量/方法/类，而不是添加注释

在不同的类中访问方法时更容易阅读。否则，API 的用户将不得不阅读方法/类来理解它做什么。这是不必要的，浪费时间！

其中:

```
void main() {
  play = true
  board = board()

  while (play) {
    input()
    play = win()
  }
} 
```

做:

```
void game() {
  isPlaying = true
  initBoard()

  while (isPlaying) {
    getUserInput()
    playing = !hasWon()
  }
} 
```

您可以很容易地看出，`game()`方法启动游戏，准备游戏板，获取用户输入，如果用户赢了，就结束游戏。

这同样适用于变量名。

其中:

```
// Updates the UI
void updateUI() { ..

// the data store
var store = .. 
```

这些评论没有增加我对这个变量的理解。`updateUI()`-应该-更新 UI 等。

做:

```
void updateUI() {
}

var store = Store() 
```

阅读这篇文章可以更全面地了解[命名变量](https://a-nickels-worth.blogspot.com/2016/04/a-guide-to-naming-variables.html)。