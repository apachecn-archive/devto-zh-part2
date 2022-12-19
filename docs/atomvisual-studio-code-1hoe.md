# 从自动取款机换乘了 Visual Studio Code

> 原文：<https://dev.to/dala00/atomvisual-studio-code-1hoe>

最近，从长期使用的自动取款机换成了 Visual Studio Code。

理由只有一个，“太重了”

总觉得有机会换乘 Code。

### 首先是自动取款机的优点

自动取款机很好。
通过扩展功能，什么都能齐全，什么语言都可以处理，
只要启动一次，就能轻轻松松地移动，很舒服。

如果对使用的包装不满意的话，可以发送 plullic 进行改善，
大家一起制作的非常好的 APP。

那个时候我想，会一直使用到死吧。

### 自动取款机不好的地方

总之很重。

虽然每周 1 的常驻业务在 32bit 的 Windows7 上使用了 Atom，但是启动和结束太重了。
想要同时进行多个项目的话就太不像话了。

因为自己的 PC 是以前为游戏买的好东西，所以没怎么在意。
有时候 PC 本身会完全死机，所以觉得很奇怪，但是打开太多 Atom 的话就好像这样了。

另外，自动完成功能等软件包在背后开始分析，所以很快就会变得沉重。
键输入也开始产生延迟。 因为没办法，所以放弃使用那样的软件包。

因为最近增加了 IDE 功能，所以期待地试了一下，但是没有达到期待。
我想这一带完全冷却了对 Atom 的感情。

### 对 Visual Studio Code 的换乘的担忧

因为我经常尝试，所以我注意到了 Code 非常快、舒适。
换乘前等时候最大限度地感受到了 Atom 的重量，所以感觉到了天与地的差别。

那么为什么没有换乘呢，是对 Vim 封装的不满。

Atom 的 Vim 程序包，是日本人开发者将原本的 Vim 程序包改为其他程序包进行强化，从而夺取了主导权，非常舒适。

Code 的 Vim 不能进行下述操作，感觉日常使用起来很困难。

*   Insert mode 的时候也会夺走 Ctrl+F，检索、置换很麻烦
*   根据模式的不同，无法通过 Ctrl+PgUp、PgDn 进行标签切换
*   如果设定为使用系统的 Clipboard，则有一部分无法操作的不良情况(现在正在改善)

虽然只有这么一点，但是因为速度感丧失，压力很大，所以不能使用。

### Solution

因为解决了，所以马上换成了编码。

因为键绑定可以自由设定，所以自己随便设定就可以了。
现在使用的键绑定有以下感觉。

```
[  {  "key":  "ctrl+f",  "command":  "actions.find",  "when":  "editorTextFocus && vim.active && vim.use<C-f> && vim.mode == 'Insert' && !inDebugRepl"  },  {  "key":  "ctrl+pageup",  "command":  "workbench.action.terminal.focusPrevious",  "when":  "terminalFocus"  },  {  "key":  "ctrl+pagedown",  "command":  "workbench.action.terminal.focusNext",  "when":  "terminalFocus"  },  {  "key":  "ctrl+pageup",  "command":  "workbench.action.previousEditor",  "when":  "editorTextFocus && vim.active && vim.use<C-pageup> && !inDebugRepl"  },  {  "key":  "ctrl+pagedown",  "command":  "workbench.action.nextEditor",  "when":  "editorTextFocus && vim.active && vim.use<C-pagedown> && !inDebugRepl"  },  {  "key":  "tab",  "command":  "editor.action.indentLines",  "when":  "editorFocus && vim.active && !inDebugRepl && vim.mode != 'Insert'"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

### 换乘

非常舒适。 总之很快。
详细情况我不知道，也许是因为使用了语言服务器，自动完成等沉重的功能也特别快，没有压力。

因为 Git 的简单操作也能在 Code 上轻松完成，所以很方便。

以前使用 Visual Studio 的时候，总是想着很沉重，但是
我甚至想如果没有微软做的话，IDE 可能会更慢。

总之推荐 Visual Studio Code。