# 我的 PHP 崇高文本设置

> 原文：<https://dev.to/restoreddev/my-sublime-text-setup-for-php-4o3h>

我使用 Sublime Text 进行 PHP 编码已经有几个月了，在这段时间里，我积累了一些有用的插件和编辑器设置。我没有看到很多关于设置 Sublime Text 的文章，特别是关于 PHP 的，所以我想分享一下我的设置是如何工作的，以及对我高效地编写代码最有帮助的是什么。

### 首选项

Sublime Text 有一个首选项区域，您可以在这里使用 JSON 调整编辑器的布局。以下是我最喜欢的设置:

```
{  "font_face":  "Fira Code Retina",  "font_size":  14,  "line_padding_bottom":  2,  "line_padding_top":  2,  "highlight_line":  14  "bold_folder_labels":  true,  "rulers":  [80,  120],  "tab_size":  4,  "translate_tabs_to_spaces":  true,  "trim_trailing_white_space":  true,  "ensure_newline_at_eof_on_save":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

我遵循 PHP-FIG 提供的 [PSR-2](%5Bhttps://www.php-fig.org/psr/psr-2/) 风格指南，所以其中几个设置对保持这种风格非常有帮助。*标尺*设置在你的编辑器中显示一条垂直线作为线长的指引。PSR-2 将首选限制设置为 80 个字符，将软限制设置为 120 个字符，因此我在两个长度上都显示了一条垂直线，以便知道何时超过了限制。*标签*的设置确保我总是使用四个空格的标签来缩进(是的，比起标签我更喜欢空格)。我经常忘记在文件末尾添加一个空行，所以*确保 _newline_at_eof_on_save* 设置会自动为你做这件事。如果您很好奇，空白的最后一行是为了当您开始向文件中添加更多代码时，不会出现向上面一行添加返回字符的 git 差异。

我最喜欢的便利设置之一是 *highlight_line* 偏好。这将使 Sublime 突出显示光标所在的行，从而很容易找到您正在键入的内容。最后，我使用 [Fira 代码](https://github.com/tonsky/FiraCode)作为我的字体。这是一种有连字的非常好的字体。Sublime Text 现在支持字体连字，这使得你的代码看起来非常干净。

### 插件

崇高文本有如此多的插件，很难找到好的和有用的。排名不分先后，以下是我认为最有用的列表。

*   [全部自动完成](https://github.com/alienhard/SublimeAllAutocomplete)
*   [巴别塔](https://github.com/babel/babel-sublime)
*   [支架打火机](https://github.com/facelessuser/BracketHighlighter)
*   [DocBlockr](https://github.com/spadgos/sublime-jsdocs)
*   [去](https://github.com/kemayo/sublime-text-git)
*   [排水沟](https://github.com/jisaacks/GitGutter)
*   [PHP 伴侣](https://github.com/erichard/SublimePHPCompanion)
*   PHP 获取器和设置器
*   [葡萄酒](https://github.com/guillermooo/Vintageous)
*   [一个文件图标](https://github.com/ihodev/a-file-icon)
*   [升华棉绒](https://github.com/SublimeLinter/SublimeLinter)
*   [升华 Linter PHP](https://github.com/SublimeLinter/SublimeLinter-php)

*Sublime PHP Companion* 为 PHP 开发增加了几个有用的命令。特别是，我非常喜欢`Find Use`命令，它会自动在文件顶部为光标下的类名添加一个`use`语句。 *DocBlockr* 将为你的 PHP 方法自动生成 doc 块，使文档更加容易。 *Vintageous* 增加了 vim 按键绑定(我最喜欢的插件之一)。GitGutter 会在侧边栏中显示你添加和修改的代码行，让你很容易找到你写的新代码。Babel 插件增加了 ES6 和 JSX 语法高亮。 *All Autocomplete* 将为您打开的所有文件添加自动完成的条目，使得引用您打开的类的方法变得容易。

### 主题

在 Sublime Text 3 的普通版本中，该团队添加了一个名为 *Adaptive* 的新主题，它将自动调整侧栏和标签栏的外观，以匹配所选的配色方案。我最喜欢的两种配色方案是[德古拉](https://draculatheme.com/sublime/)和[日晒深色](http://ethanschoonover.com/solarized)(默认提供)。过去，我曾使用过[方方正正的主题](https://github.com/ihodev/sublime-boxy)，但它已经被弃用，取而代之的是一个更新的主题。

[![My setup](img/e90149aef17496090564c2361f2adb78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FhArZU7D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4eih36wn2x5rgl1rcla2.png) 
*这是我现在使用德古拉主题的设置。*

如果你有一些喜欢的崇高文本插件或设置，请在评论中告诉我。我一直在寻找更多的功能来尝试。

以下是一些帮助我创建这个设置的博客帖子的列表:

*   [PHP 开发人员的崇高文本(3)| MattStauffer.com](https://mattstauffer.com/blog/sublime-text-3-for-php-developers/)
*   [https://wesbos.com/sublime-text-5-visual-tweaks/](https://wesbos.com/sublime-text-5-visual-tweaks/)
*   [如何为 PSR 标准|开发简体版配置 Sublime Text】](http://joshfrankel.me/blog/How-to-configure-sublime-text-for-psr-standards/)

编码快乐！