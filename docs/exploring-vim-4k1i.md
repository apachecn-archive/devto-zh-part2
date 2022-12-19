# 探索 Vim

> 原文：<https://dev.to/vintharas/exploring-vim-4k1i>

*本文最初发布于[barbarianmeetscoding.com](https://www.barbarianmeetscoding.com/blog/2018/10/14/exploring-vim)。😊*

自从我成为一名程序员以来，我一直非常执着于少花钱多办事的想法。从如何过一个充实、有目的、有思想的生活，到如何以最有效的方式重构这一行代码。在编码领域，我一直痴迷于学习新的技能和工具，这些技能和工具可以解锁并释放无限的力量，使我能够通过每一次按键和我投入开发的每一个小时来实现更多。

[![Tidy desktop setup with and iMAC that has a wallpaper prompting you to do more](../Images/569567a6f378434adfd793c73d392046.png "Do More With Less")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yl1Ldoug--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/do-more.jpg)

*Unsplash 上卡尔·海尔达尔的照片*

这样的工具之一就是 [vim](https://www.vim.org/) 。Vim 是一个文本编辑器，其中心键盘设计、模态哲学和命令可组合性，向那些敢于面对陡峭的学习曲线并幸存下来讲述故事的人承诺了无限的生产力和熟练的文本编辑。

自从我决定咬紧牙关学习 vim 以来，已经有 5 年了。这很难，特别是因为我不是一个触摸打字员，但我设法学习和收集了一些权力承诺在另一边。然而，我从未完全跳到 vim。相反，我使用 vim 层呆在 Visual Studio、Atom 和最近的 Visual Studio 代码的更舒适的范围内(大多数编辑器都有 vim 模式，或多或少地支持 vim)。Vim 仍然是我的第二个编辑器，主要用于业余爱好项目或写作。所以我从来没有在 vim 得到真正好的**。不要误解我，学会熟悉基本的命令和动作帮助我更熟练地编写和编辑代码。更好的是，它使我的大脑和键盘之间的界面变得更薄，这让我可以更容易地将我的想法输入代码。**

 **尽管如此，我总是为没有走完全程而感到一丝悔恨，一种在另一个编辑器的范围内模拟 vim 不可能像真实的一样好的不安感觉，一种我肯定错过了某些东西的感觉。

所以我们来了！**今天，我发誓重新开始我对文本编辑启蒙的追求，踏上学习 vim 的艰险之路，到达编码牛逼的神圣殿堂**。想加入吗？

## Vim 是什么？

Vi 是一个古老的文本编辑器(与 [1976 年](https://en.wikipedia.org/wiki/Vi)一样古老)，它被设计成在终端上工作，并且具有以模态方式工作的非常不寻常的特征(例如，插入文本的模式、编辑文本的模式、选择文本的模式等等)。它的最新和最著名的化身是[vim](https://en.wikipedia.org/wiki/Vim_(text_editor))(**V**I**IM**proven ),它支持文本和图形界面，对 [vi](https://en.wikipedia.org/wiki/Vim_(text_editor)#Features_and_improvements_over_vi) 进行了大量改进，并且在人类已知的所有平台上都得到了支持。

<aside class="callout blue">

### 那尼奥维姆呢？

在过去的几年里，你可能听过人们谈论 Neovim，并想知道 *wat* ！？。 **Neovim** 是 vim 的一个现代分支，旨在重构 Vim，使其更易于维护、扩展，更容易被更广泛的社区所贡献。它开箱即用，具有更合理的默认设置和集成的终端。你可以在 [neovim.io](https://neovim.io/) 找到更多关于 Neovim 的信息。

</aside>

## 为什么是 Vim？

2018 为什么要关心学一个古代编辑？很棒的问题！Vim 提供了一种我所见过的与文本交互的不同方式，这种方式在编辑代码时给你完全不同的控制和流畅度。

使用 vim，您与代码交互的主要方式不是通过插入字符或使用鼠标四处移动。相反,**你将像一个代码外科医生,**随时随地以外科手术般的精度进行专业切割，以完全键盘驱动的工作流程的闪电般速度和准确性浏览你的代码和代码库。

Vim 旨在**为触摸打字员提供最高程度的生产力**，最常见的命令舒适地放置在主行及其相邻的键上。Vim 是一个模态编辑器，所谓的*正常*模态在最前面。一种随意读取、导航和转换代码的方式。事实上，vim 具有模式，允许在每个单独的模式中重复使用靠近主行的按键，最大限度地减少对缓慢和扭曲的按键组合的需求，并提高您的速度和手指及手腕的寿命。

Vim 是非常可定制的，你可以根据你的编码方式和做事方式来调整它。学习 vim 的美妙和复杂之处在于，你如何缓慢但肯定地让它以你工作的方式工作，在你的项目中，独自或与你的团队一起工作。

在任何情况下，即使您没有直接进入 vim 编辑器，您也可以通过将使用 vim 学习的所有命令和动作带到您最喜欢的编辑器中来获得回报。就是这样！Vim 是如此之好，以至于今天大多数其他编辑器都支持某种 vim 模式，这种模式将所有基本的命令和动作都带入到您所熟知的编辑器中。

那么**2018 年你为什么会想学 Vim 呢？**转述[德鲁内尔](https://twitter.com/nelstrom)在[实用 Vim](https://amzn.to/2CIzSpb) <sup id="fnref1">[1](#fn1)</sup> :

> Vim 是为那些想要提高游戏水平的程序员准备的。在专家的手中，Vim 以思维的速度撕碎文本。

谁不想这样呢，对吧？

## 一种 Vim 的味道

与任何其他传统编辑器不同，当使用 vim 时，你将在*普通*模式下花费大部分时间。在这种模式下，您不需要显式地编写代码，而是针对导航和精确的文本更改进行优化。你的右手紧紧握住 <sup id="fnref2">[2](#fn2)</sup> 核心**运动**(如在运动中)按键`hjkl`。这些键分别向左、下、上、右移动光标。您也可以使用`w`(转到下一个 **w** ord 的开头)或`e`(转到下一个单词的 **e** nd)逐词从左向右移动，或者使用`b` / `ge`进行相同的操作，但从右向左移动。

利用这些键，你可以精细地浏览一个文件，并以极大的报复和恶意进行攻击。你**d**elete*a*T7】word！`das`你去掉一个 **s** entence！`dap`而你擦掉了一个 **p** 的段落！厉害！

或者你可以少一些威胁，多一些培养和修理东西。使用`caw`、`cas`、`cap`到 **c** 改变一个 **w** ord、一个 **s** entence 或一个 **p** aragraph。但这并没有结束。你可以`ctx`到 **c** hange un **t** il 当前行的第一个`x`，或者`c$`到 **c** hange 一切直到行尾，或者最好`ci(`改变括号内的内容或者`ci"`对引号内的内容做同样的事情。

想象一个简单的字符串:

```
const msg = 'Hello vim' 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过键入`f'ci'WAT<ESC>`
来更改字符串

```
const msg = 'WAT' 
```

Enter fullscreen mode Exit fullscreen mode

也就是说*找到下一个`'`* 然后 **c** 把`'`里面的东西都换成`WAT`，然后`<ESC>`离开*插入*模式回到*正常*模式。

[![wat](../Images/15549969fc489a924de8c3c63a75e9fd.png "wat")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bwA1NbDb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/wat.jpg)

然后你可以 **y** 点击带有`yy`的行(这是 vim 的行话，表示复制)，然后 **p** 点击下面的`p`(也是 vim 的行话，表示粘贴):

<aside class="callout blue">

是的，你猜对了！*猛拉*是另一个操作符，类似于*删除*和*改变*。你可以用`y`就像`d`或者`c`猛拉一个单词`yaw`或者`yas`猛拉一个句子。此外，像 so `yy`这样的命令加倍会使命令在整行上运行。俏皮的`cc`换一行`dd`删一行。

</aside>

```
const msg = 'WAT'
const msg = 'WAT' 
```

Enter fullscreen mode Exit fullscreen mode

再次`f'ci'MAN<ESC>` :

```
const msg = 'WAT'
const msg = 'MAN' 
```

Enter fullscreen mode Exit fullscreen mode

然后用`kJ`疯狂插队(`k`上去`J`插队):

```
const msg = 'WAT' const msg = 'MAN' 
```

Enter fullscreen mode Exit fullscreen mode

用`c3w+<ESC>`冲洗，我们就有了自己:

```
const msg = 'WAT' + 'MAN' 
```

Enter fullscreen mode Exit fullscreen mode

[![All kudos to wat](../Images/411e226cda8775674430a9f9e65b4206.png "nanananana WATMAN!")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--do197CHO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/watman.jpg)

*来自超级棒的[窟](https://www.destroyallsoftware.com/talks/wat)T3】*

这是一个使用 vim 的完全无意义的练习，但是仍然向您展示了它的部分魔力。

对于较长的动作，您可以将**计数**与动作结合使用，例如，通过键入`5j`向下 5 行(如在`{count}{motion}`中)。同样，你可以把这些和你在上面看到的运算符(`d`、`c`等)和`d2w`一起使用，删除两个单词，或者`c2s`改变两个句子。还有更长的动作，你可以`H`移动到编辑器中可见区域的顶部，或者`gg`移动到文件的顶部，`L`和`G`实现相同的效果，但是向下。使用`{`向上移动整个段落，或使用`}`向下移动。而`%`帮助你找到匹配的括号。

你可以用`o`在下面开始新的一行(进入插入模式，这样你就可以开始输入)，或者用`O`在上面开始新的一行。您可以使用`/{pattern}`在文件中向前查找(文本的)模式，使用`n`(下一个)和`N`(上一个)在模式之间导航，或者使用`?{pattern}`向后导航。

您可以使用`.`命令重复之前的更改。只需输入`.`，vim 将重复您最后的更改。同样，你可以重复动作。输入`;`，你将重复一个以`t,f,T,F`开始的动作，或者输入`n`，重复一次搜索。您可以使用宏录制一组命令，并随意重放它们。还有更多...

如此强大的能力唾手可得，而我们几乎没有离开*正常的*模式或单个文件的限制。有拆分，有标签，有正则表达式，有外部工具的访问，有拼写检查，字数统计，有 [6 个基本模式和 6 个额外的变体模式](http://vimdoc.sourceforge.net/htmldoc/intro.html#vim-modes-intro)和无限的可扩展性和定制的可能性！(谁激动了！？)

## 维姆道

让我们后退一点，思考一下，试着从我们目前所看到的中汲取一些智慧。

**键盘居中是 vim** 的核心。重要和有用的命令位于您的指尖之下，它们的位置非常周到，有助于有效的使用和学习。例如:

*   右手下第一排的基本动作`hjkl`
*   一个在另一个下面的普通中继器`;`和`.`
*   另一个基本而有力的动作`f`是左手食指
*   基本操作人员左手可轻松操作:`s`、`x`、`d`、`c`、`r`
*   `s`与`cl`同义，`x`与`dl`同义，与`d`和`c`相差一指
*   `*`和`#`用于向前和向后查找当前单词的动作。它们由每只手的中指触发。

这些**操作符和动作有着难以置信的意义**，并且通过使用助记符很容易记住`c`表示 **c** hange，`cl`表示**c**hange**l**etter，`caw`表示**c**hange**a**T18】word，`ciw`表示**c**hange**I**NNE 这使得 vim 非常有助于学习，因为你不需要记忆神秘的命令:这些命令只是有意义的。

此外，所有这些运算符、计数和运动构成了一种(编程)语言。你可以把操作符想象成函数，把计数和运动想象成参数，或者用一个更简单的类比...你可以把操作符想象成动词，把计数想象成形容词，把动作想象成宾语。**[vim 的真正魔力在于构图](https://medium.com/@mkozlows/why-atom-cant-replace-vim-433852f4b4d1)** 。随着你逐步积累这些操作符和动作的词汇，你会发现你可以随心所欲地将它们结合起来。这样，一旦你从上一段中了解了所有的`c`、`cl`、`caw`、`ciw`、`ct.`，并且了解了`dl`是如何工作的，你不仅能够使用`dl`，你还会知道你可以将它与你已经掌握的所有动作以及`daw`、`diw`、`dt`等结合起来。

这很酷。当使用 Vim 时，你会感觉到你在文本编辑的元宇宙中导航，就像编程或控制编辑和编写文本的机制。如果您熟悉 git 以及使用 git 命令行处理源代码控制的感觉，您可以将 vim 视为文本编辑的 git。(撇开 vim 比 git 早了近 30 年这个事实不谈)。使用 vim，您将看到一段文本，您将不再仅仅看到单词或文本，您将看到无限数量的操作符和动作被应用的可能性。就像尼奥对母体的觉醒。

好的...谁让自己走得有点太远了？( **me 举手**

## Vim 为什么这么硬？

现在谈谈维姆的黑暗面...即。这个。

[![Classic image with representations of the learning curve of different text editors](../Images/6b5786d19cde9539ab1d9bba8e7f0d81.png "Vim Learning Curve")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cZUuzF9x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/vim-learning-curve.jpg)

*来源不明*

还有这个。

[![Classic joke about not being able to leave vim](../Images/a72e89543bf8e43df1c2edd59d186a64.png "Classic")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UCbi-2wC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/cant-leave-vim.jpg)

陡峭的学习曲线有些夸张，退出 vim 的笑话已经变得很老了。事实是，至少有三个因素导致了这种陡峭的学习曲线，并使 vim 对粗心的旅行者来说不太容易接近。

1.  触摸打字是必须的
2.  它是键盘驱动的，需要一些时间来适应默认的正常模式
3.  糟糕的第一印象

### 触摸打字是必须的

**触摸打字是 vim** 的先决条件。如果你不会打字，你将很难在 vim 中执行最基本的操作。作为一名自学成才的打字员[，这在一开始对我来说是一个巨大的障碍](https://www.barbarianmeetscoding.com/blog/2013/05/18/the-path-to-jedi-text-editing-two-weeks-into-vim-and-vsvim)，从每分钟 100 字到每分钟 10 字，让我打字极其缓慢，更不用说写代码了。仅仅是生产力的巨大下降就能让你在最初的几个小时里放弃对 vim 的追求。因此，如果你认为学习 vim 是一项有价值的追求，那就帮你自己一个忙，先学会接触打字。

以下是您可以用来学习如何触摸文字的一些资源:

*   [typing.com](http://www.typing.com)。我在这个网站上花了很多时间来改掉我所有的坏习惯，并学习正确的触摸打字方式。当我觉得我的触摸打字越来越生疏时，我今天仍然用它来练习打字。这个网站这些年来的进步令人惊讶。
*   [keyzen.io](http://wwwtyro.github.io/keyzen/) 。这是一个不错的触摸式打字训练器，主要帮助你提高在编程中常见的不常见字符的打字技巧，如`;`、`{`、`(`、`/`
*   zType 是一款打字游戏，你扮演一艘船，通过打字向一群外星人开火。练习触摸打字的一个非常有趣的方法。警告:非常上瘾，非常刺激。不要在睡觉前使用。

### 键盘和普通模式墙

在一个主要模式是*插入*文本的普通编辑器中度过了大半辈子之后，迁移到 vim 将是一个挑战。到目前为止，您已经习惯了切换编辑器，并且很快就能上手，因为它们都共享一种插入模式优先的方法，并且对于在 GUI 中使用鼠标进行探索非常友好。在任何现代编辑器中使用鼠标，你将能够探索菜单中可用的选项，并右键单击窗格、窗口、标签和文本。使用这种探索性的方法，你会慢慢明白事情的意义，并学会使用新的编辑器。

然而，Vim 完全是键盘驱动的，并且经常在终端内部使用(因此可能没有启用鼠标支持，也没有使用鼠标的选项)。尽管会提示您使用通过`:h`命令获得的帮助，但您很少会听从这个建议。只是感觉太诡异了，太陌生了。再加上一个非插入模式的编辑器，在那里你甚至不能输入文本(并且开始移动甚至离开编辑器)，你会感觉完全迷失了。

这里最好的建议是先学习最基础的知识，然后慢慢扩大你的词汇量。最基本的是:

*   获得关于`:help {whatever}`(或更短版本的`:h {whatever}`)的帮助。维姆的帮助非常好。
*   用`hjkl` ( `:h motion`或`:h movement`移动
*   用`i` ( `:h insert`)进入插入模式
*   用`<ESC>` ( `:h mode-switching`)返回正常模式
*   用`:w` ( `:h write`)保存文件
*   用`:q` ( `:h quit`)离开编辑
*   用`:e {filepath}`(如`:e src/main.js` ) ( `:h edit`)打开一个文件

<aside class="callout blue">

注意到这些命令前面的`:`了吗？冒号触发命令模式，也称为 ex 模式。当您键入冒号和命令时，该命令将显示在屏幕的左下角。

</aside>

为了生存，你只需要知道这些。请注意我是如何添加了`:help`命令的，您需要键入该命令来查找关于这些命令中每一个的信息，以及猜测它们有多容易。通过寻求帮助，你可以很容易地自己找出如何退出 vim。Vim `:help quit`请。这是一个非常容易实现的目标，第一天你可能会开始使用比这些更多的命令:`w`、`e`、`c`、`d`都是非常可学的，因为它们很有意义。

换编辑的时候能活下来不是目标。不会马上让你卷铺盖走人就足够了，但是你真正想要的是比你现在的编辑器更有生产力和效率。我认为，只需几个命令，你就会发现你可以比你目前的设置更有效率。到目前为止，实现这个目标最简单的方法是在当前编辑器上使用 vim 模式。这样，您就可以最大限度地减少精通 vim 所需的工作量，并(几乎)立即获得一些好处。

在任何情况下，这里最难的部分不是学习命令本身，而是舒服地执行它们。这就需要练习了，尤其是最基本的动作。一些小贴士可以帮助你:

*   **熟能生巧**。从小做起，持之以恒。每天至少学一个新招，练习。注意如何使用当前的编辑器，并尝试找到一些方法，通过使用新命令或已知命令的不同组合，可以更快地完成同样的事情。
*   **试试`vimtutor`** 。只需在您的终端(在安装了 vim 的机器上)键入`vimtutor`，您将获得一个 30 分钟的教程，它将帮助您开始使用 vim 并学习更多的基本命令。(在 neovim 中，您可以使用`:Tutor`命令从 vim 中启动 vimtutor)。你不需要一口气完成这个教程，但我真的建议你通读一遍。你会学到很多东西。
*   **耍个无赖学学`hjkl`** 。经典的 roguelikes 是基于文本的 RPG 游戏，你可以在一个用角色绘制的 2D 环境中玩(玩家通常被表示为一个`@`)。起源于终端的 Roguelikes 对 vi 非常友好，通常允许你用`hjkl`移动你的角色，并且只通过键盘控制游戏
*   **试试[维姆历险记](https://vim-adventures.com/)T3。这是一个很好的游戏，可以帮助你一次学习一个 vim 技能，并让你在用之前的技能证明自己的价值后解锁新的钥匙(和新的技能)。这是一个非常好的循序渐进学习 vim 的方法。**

### 糟糕的第一印象

vim 难学的最后一点是， **vim 给你的第一印象[恐怖](https://github.com/neovim/neovim/issues/276)** 。您输入`vim`(或者对于 Mac GUI 输入`mvim`，对于 Windows 和 UNIX GUI 输入`gvim`)欢迎使用 vim！

[![The white screen of paleness that greets you the first time you open vim](../Images/38cd2582a65f1236b7ebf05a48f75f61.png "Welcome To Vim!!")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tQNdGb-O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/welcome-to-vim.jpg)

这就是你必须要做的(如果你在终端上打开它，它可能是黑底白字)。打开一个文件(比如我的`.vimrc`),没有语法高亮显示:

[![The white screen of paleness that greets you the first time you open vim. This time showing a file with no syntax highlighting](../Images/0dcc003958e1daab417e4bbfb98b34db.png "Welcome To Vim part 2!!")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9b9PIhJ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.com/images/welcome-to-vim-ii.jpg)

这是一个非常糟糕的第一印象，因此我已经两次放弃学习 vim 了。(*我们来学 vim 吧！打开 vim！没时间了...*)

您可能想使用 vim 发行版——作为二进制或插件发行的 vim 预配置版本。这肯定会给你一个更好的第一印象，一个更符合你在现代编辑器中习惯的编辑器，但是它有一个巨大的缺点:你不会理解你的 vim 是如何设置的。它将只是一个默默无闻的黑匣子。这会让您错过 vim 的部分精髓和它的核心特性之一，即它的可扩展性和可定制性。相反，请遵循以下步骤，这些步骤旨在让你在前进的道路上取得小的成功，而不是让你不知所措:

<aside class="callout">

### Neovim 对初学者更好

考虑使用 [neovim](https://www.neovim.io/) 代替 vim 执行以下步骤。Neovim 有两点让它对新人来说更加平易近人。首先，Neovim 提供了比 vim 好得多的默认设置(比如语法高亮、自动缩进等)，这些都是 vim 中没有的基本功能。第二，它有一个更好的 *vimtutor* ，可以通过运行`:Tutor`从 neovim 内部启动，并在你完成练习时给你反馈。

</aside>

1.  从`vimtutor`(neo vim 上的`:Tutor`)开始学习 vim 的基础知识
2.  使用你选择的编辑器中的插件来应用你所学的东西。使用你当前的编辑器将会最小化你需要学习的东西，并且允许你用大量的微编辑 vim 技术来丰富你当前的编辑技能。根据您的编辑器的 vim 模式有多好，您会有或多或少的愉快体验。
3.  当您对基本的 vim 命令感到满意时，然后在`.vimrc`中设置您的 vim 配置(对于 neovim，则为`init.vim`)。看一看 [vim-sensible](https://github.com/tpope/vim-sensible/blob/master/plugin/sensible.vim) 或者我的 [vim wiki](https://dev.to/wiki/vim) 来逐步解释最小化和无插件配置。如果你正在使用 Neovim，你可以跳过这一步。
4.  使用 vim 插件管理器来丰富为您的工作流程量身定制的 vim 的功能(最成熟的有 [vundle](https://github.com/VundleVim/Vundle.vim) 、 [vim-plug](https://github.com/junegunn/vim-plug) 和[病原体](https://github.com/tpope/vim-pathogen)、[现代 vim](https://amzn.to/2C1lozu) 书籍推荐 [minpac](https://github.com/k-takata/minpac) )。这些插件管理器大部分时间的工作方式是，它们将克隆一个 git repo，其中包含一系列 vim 脚本，代表您需要的插件。Vim 将在启动时获取这些脚本，并在 vim 的上下文中运行它们。《现代 Vim》[这本书提供了让 Vim 作为现代 IDE 工作的很好的建议。](https://amzn.to/2C1lozu)
5.  查看 [vim awesome](https://vimawesome.com/) 中您感兴趣的插件以及您开发的应用类型
6.  随着时间的推移，练习和改进您的 vim 设置和配置

## 现在就这些

Wop！这比我预期的要长一些。希望您学到了一些东西，发现它很有趣，并且对 vim 感到有点好奇。祝你有美好的一天！

## 迫不及待想了解更多？

下面是更多的资源:

*   [维姆](https://www.barbarianmeetscoding.com/wiki/vim)和[尼奥维姆](https://www.barbarianmeetscoding.com/wiki/neovim)维基
*   vim 创始人 Bram Moolenar 的 7 个有效文本编辑习惯
*   [实用维姆](http://amzn.to/1koJVl9)和[现代维姆](https://amzn.to/2C1lozu)书籍
*   [vimcasts.org](http://vimcasts.org/episodes/archive/):德鲁·尼尔的 Vim 短片集，[实用 Vim](http://amzn.to/1koJVl9) 的作者

野蛮人遇到编码视频日志的[视频:](https://www.youtube.com/watch?v=_uJiaVcD_s4)

[https://www.youtube.com/embed/_uJiaVcD_s4](https://www.youtube.com/embed/_uJiaVcD_s4)

> ### 词是一种特殊的词
> 
> 在前面的例子中，我使用了`f'ci'`组合来改变引用文本的内容，但是`ci'`会达到同样的结果。
> 
> 不像其他文本对象(在前面的例子中我们称之为`aw`、`as`、`ap`、`i'`)，当你使用`i'`时，你甚至不需要将光标放在引用的文本上。在行首键入`ci'`，它将改变在该行中找到的第一个引用文本的内容。这太棒了，因为它省去了你输入`f'`的时间。我保留了`f'`,因为这种行为只适用于 quotes 文本对象，而不适用于其他对象。如果你想改变括号、括号、标签等的内容，你仍然需要将光标放在它们所界定的区域内。
> 
> 感谢你
> 
> [![bokwoon95 image](../Images/4cbea99c443ab4c64d4f434168ee8dad.png)](/bokwoon95)
> 
> ## [bokwoon95](/bokwoon95)
> 
> [/bokwoon95](/bokwoon95)
> 
> 在评论中分享这个！:D·何还推荐了 [targets](https://github.com/wellle/targets.vim) 插件，它将这一令人敬畏的行为扩展到了其余的文本对象。

<aside class="callout blue">

### 想了解更多关于 Vim 的知识？

然后阅读本系列的下一篇文章。尽情享受！

</aside>

* * *

1.  《实用 Vim》是一本关于 Vim 的非常棒的书。它遵循从初学者到更高级的提示格式。在每一个技巧中，它都提供了很好的背景信息和上下文，不仅可以帮助您理解如何使用新的命令和特性提高 vim 技能，还可以帮助您像 vim 大师一样思考更广泛的模式和思想。所有这些都有很好的例子和练习来帮助你练习肌肉记忆。原文是这样的:实用的 Vim 是为想要提高游戏水平的程序员准备的。在专家的手中，Vim 以思维的速度撕碎文本。阅读这本书是你迈向那个目标的下一步。 [↩](#fnref1)

2.  `h`键就在你的食指旁边，随手可得。 [↩](#fnref2)**