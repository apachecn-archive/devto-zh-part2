# 我的 Visual Studio 代码安装程序

> 原文：<https://dev.to/aspittel/my-visual-studio-code-setup-1emn>

我喜欢我已经用了一年半的文本编辑器，所以我想展示一下我的设置！Visual Studio 代码已经远远超过了我使用 Atom、Spyder、Sublime Text、Emacs 和 Idle(内置 Python 编辑器)的经验，我在它之前的几年里一直使用这些代码。我想我应该写一篇关于我的设置和我喜欢的扩展的文章！

[https://www.youtube.com/embed/OzP_3KUS17E](https://www.youtube.com/embed/OzP_3KUS17E)

## 主题

因为我教人们如何专业地编码，并且做了很多关于编程的演讲，所以有一个可读的主题对我来说是非常重要的。我也喜欢有一个我在审美上喜欢的主题，所以通常是带有很多少女色彩的主题。

这些标准被设定，我最喜欢的两个主题是[仙女线](https://marketplace.visualstudio.com/items?itemName=nopjmp.fairyfloss)和[吸血鬼](https://draculatheme.com/visual-studio-code/)。

#### 仙女牙线

[![Fairy Floss](../Images/0aa2ad0fc0bfbbe7b827cf1b6151bc57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---wkQHx2k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/erlp85m0h5tnsk3n74fd.png)

#### 德拉库拉

[![Dracula](../Images/025756652cee01f86f03e4efa83f4bb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mov3n2aA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1mbrka2lqn6j26rxtlcc.png)

## 字体

我是 FiraCode 的长期忠实拥护者(约会前 VS 代码！);我喜欢让我的代码一目了然的连字。设置起来也很容易，这真是太好了！

[![The Fira Code Font](../Images/1d27c7c939da2f79e28930dd52d1e6ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zXxo3LMu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27ra6e92kvyqy3emies7.png)

## 保存配置

我的设置的另一个关键部分是我的保存配置。我最喜欢的是 autosave，你可以通过`file`下拉菜单打开它。我对它做了进一步的配置，这样每当我在写代码的时候暂停，我的代码就会自动保存，而不需要我做任何事情。我无法解释这对现场编码和初学者有多大帮助。

```
// in settings.json
"files.autoSave": "afterDelay",
"files.autoSaveDelay": 100, 
```

Enter fullscreen mode Exit fullscreen mode

同样，我设置了更漂亮的 T2 来格式化我在 T0 上的代码。理想情况下，这也会自动运行，但目前已经足够接近了。我用 [StandardJS](https://github.com/standard/vscode-standardjs) 做了一段时间同样的事情，然后转向一个支持分号的客户端。

```
// in settings.json
"editor.formatOnSave": true, 
```

Enter fullscreen mode Exit fullscreen mode

对于大多数语言缩进，我使用两个空格，唯一的例外是 Python，默认情况下我使用四个空格。

```
// in settings.json
"[python]": {
    "editor.insertSpaces": true,
    "editor.tabSize": 4
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 埃米特

我非常依赖 [Emmet](https://emmet.io/) ，所以我喜欢它内置于 VS 代码中。我还把它设置成不仅仅用于我的 html 文件:

```
// in settings.json
"emmet.includeLanguages": {
  "html": "html",
  "erb": "erb",
  "javascript": "javascriptreact",
  "vue": "html",
  "ejs": "html"
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 综合终端

我在 Mac 上使用带有 Zsh 的 [iTerm，因此我设置了 VS Code 的集成终端来使用该设置。](https://zen-of-programming.com/terminal-setup/) 

```
// in settings.json
"terminal.external.osxExec": "iTerm.app",
"terminal.integrated.shell.osx": "zsh", 
```

Enter fullscreen mode Exit fullscreen mode

## 分机

我内置了许多语言和特定于框架的扩展，这些扩展很枯燥，并且非常依赖于开发人员，所以我将跳过这些，以便讨论一些我最喜欢的通用扩展。

1.  这个扩展简直太神奇了，我真的相信它会在不久的将来改变代码教育和结对编程。它允许开发者在不同的计算机上对同一个文件进行谷歌文档式的协作。您甚至可以跨机器开放对本地主机端口的访问，并共享终端会话。有一些 bug，特别是在 Windows 机器上，但是太酷了，甚至没有关系！

2.  代码拼写检查器 -我直接在我的文本编辑器中写了很多 Markdown 和页面内容，我肯定有一些拼写问题。我喜欢这个扩展，它可以强调我的错误并提供建议，甚至在我离线的时候。

3.  [Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost) -这是一个“很好”而不是必需的东西，但是它显示了我在我的项目中导入的 npm 包的大小。

4.  我直接在我的编辑器中使用它进行实时 HTML 验证。不再需要每次修改都运行 W3C 验证器！

5.  GitLens 显示文档中的 Git 责备注释和作者高亮。这对从事多作者项目非常有帮助！

6.  这个扩展允许你按下播放按钮直接在 VSCode 中运行你的代码，不需要终端交互！对新程序员特别有帮助。

7.  [表情符号](https://marketplace.visualstudio.com/items?itemName=bierner.emojisense) -我喜欢 Slack 的表情符号-自动填充帖子`:`功能，这个扩展允许你在 VSCode 中做同样的事情！

## 结论

我对我的设置非常满意——我没有任何抱怨！如果你对我的设置有任何疑问，欢迎发 [tweet](https://twitter.com/aspittel) 给我，也可以把你最喜欢的技巧和窍门发给我！

如果你喜欢设置帖子，我也[写了我的 Zsh + iTerm2 设置](https://zen-of-programming.com/terminal-setup/)。

> 如果你喜欢这篇文章，看看我的完整的[博客](http://zen-of-programming.com/)或者[订阅](https://mailchi.mp/b4216331e284/zen-of-programming)我的周五每周总结！