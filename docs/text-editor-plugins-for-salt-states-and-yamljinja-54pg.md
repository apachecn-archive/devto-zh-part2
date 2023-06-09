# 盐州和 Salt 金贾的文本编辑器插件

> 原文：<https://dev.to/marnold/text-editor-plugins-for-salt-states-and-yamljinja-54pg>

<figcaption>*Image by [Justin Palmer](https://twitter.com/Caged/status/1039937162769096704)*</figcaption>

如果你曾经写过几个盐州，这些问题可能听起来很熟悉:

*   YAML 键后缺少冒号或冒号后缺少空格
*   数据无法编译:SLS“bar”中的状态“foo”不是列表形式
*   运行`state.highstate`时出现解析错误或扫描错误
*   嵌套字典，缩进两个空格而不是四个
*   在错误的缩进层次陈述需求
*   混合了制表符和空格的缩进，调试花费了惊人的长时间

即使你的州是有效的 YAML，他们仍然对盐没有感觉，因为你的必需品有一个错别字。到处撒上一些金贾标签，错误会变得更加古怪…

#### 有什么可以做的吗？

这些问题以前肯定出现过( [#802](https://github.com/saltstack/salt/issues/802) )。聪明的开发人员可能已经明白了这一点，对吗？

事实上，甚至在它们接触到您的基础设施之前，以半自动或全自动的方式验证您的状态是可能的。对于盐，您有以下选择:

1.  手动或使用提交挂钩使用 [`salt-call --local --retcode-passthrough state.show_sls YOUR_STATE`](https://docs.saltstack.com/en/latest/ref/modules/all/salt.modules.state.html#salt.modules.state.show_sls) 。这将触发状态呈现过程，并可以捕捉大量可能的错误。

2.  使用测试厨房和[厨房盐](https://github.com/saltstack/kitchen-salt)以及一些验证器来测试你的状态。关于这个话题的有用指南可以在[这里](https://salt-formulas.readthedocs.io/en/latest/develop/testing-formulas.html)找到

然而，这一切感觉有点沉重。您需要提交并推动运行您的测试，启动虚拟机或容器，深入 Ruby 代码，并为所有可能的支柱/颗粒排列编写测试。如此长的反馈周期很容易导致提交历史中的 [`git commit -m "Fuck YAML"`](https://github.com/search?q=fuck+yaml&type=Commits) ，以及随后的修正/重置/挤压之舞(你不能挤压发送给团队其他成员的淫秽提交通知！).

#### 还有更轻量级的吗？

嗯，你的开发环境可以帮到你。第一层防御可以在您输入时在文本编辑器中工作，提供即时反馈。它会在您的错别字被提交和部署之前捕捉到它们(并让 CI 系统处理更复杂的东西)。一个理想的编辑器插件可以:

*   帮助你处理 YAML 缩进层次和数据结构
*   了解并突出最常见的盐州关键词
*   突出显示 Jinja 括号和标签
*   自动完成关键字

下面我将介绍 9 个不同编辑器的插件，这样你就不必从你最喜欢的编辑器中切换出来。此外，我将给出一些安装/配置技巧，以节省你一些谷歌搜索。这些插件并不理想，但它们是开源的，如果你愿意，你可以贡献其中的任何一个(我[做过](https://github.com/glynnforrest/salt-mode/pull/21))。

*   [Atom](#atom)
*   [月食](#eclipse)
*   [Emacs](#emacs)
*   凯特
*   [午夜指挥官](#midnight-commander)
*   [崇高的文字](#sublime-text)
*   [PyCharm](#pycharm)
*   [Vim](#vim)
*   [Visual Studio 代码](#visual-studio-code)
*   [最终提示](#final-tips)

如果你最喜欢的编辑器不在这里，或者你想分享一个帮助你减少 YAML 错误的妙招，请在 Twitter 上 ping 我。

## Atom

[![SaltStack package for Atom](img/fda0757cd495f0dec578182c02858599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GAFhbfsx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-atom.png)

开箱即用，Atom 将`*.sls`文件理解为 YAML。另外，你可以安装 [atom-jinja2](https://atom.io/packages/atom-jinja2) ，并通过按`Ctrl+Shift+L`手动切换文件语法到`YAML (Jinja Templates)`。

为了获得更好的效果，您可以同时安装 [atom-salt](https://github.com/saltstack/atom-salt) 和 [atom-jinja2](https://github.com/danielchatfield/atom-jinja2) 。只需点击`Edit -> Preferences -> Install`，搜索`atom-salt`或`atom-jinja`，安装即可。

#### 特性

*   第一个`atom-salt`语言范围是`source.yaml.salt`，自动为`*.sls`文件启用，并提供语法高亮(YAML+金贾组合)
*   第二个作用域`source.python.salt`不与任何文件扩展名相关联，而是为以`#!py`开头的任何文件启用。这意味着您可以高亮显示`py`、`pydsl`和`pyobjects` Salt 渲染器的语法。
*   另外，它应该可以通过`salt-doc`命令打开 Salt docs，但是由于某种原因，它对我不起作用
*   `Atom-jinja2`提供`source.yaml.jinja`范围，为`*.jinja`、`*.j2`和一堆[其他扩展](https://github.com/danielchatfield/atom-jinja2#new-syntax-definintions)启用
*   支持语法突出显示和折叠
*   有几个与 Jinja 相关的[片段](https://github.com/danielchatfield/atom-jinja2/blob/master/snippets/atom-jinja2.cson)

您可以在您的`~/.atom/config.cson` :
中为任何扩展指定特定的语言范围

```
"*":
  core:
    customFileTypes:
      "source.python.salt": [
        "py.sls"
        "pysls"
      ] 
```

#### 其他事情要尝试

*   [语言-Salt](https://atom.io/packages/language-salt)-Salt 的替代 Atom 插件(支持`*.sls`文件，提供`source.salt`范围和一堆[片段](https://github.com/JonGretar/language-salt/blob/master/snippets/language-salt.cson)
*   [对齐器-salt](https://atom.io/packages/aligner-salt) ( [GitHub](https://github.com/adrianlee44/atom-aligner-salt) )可视化对齐 YAML 属性
*   Salt 插件应该默认启用缩进指南，但是您可以通过点击`Edit -> Preferences -> Editor -> Show Indent Guide`来强制执行

## 月食

[![SaltStack plugin for Eclipse](img/7d484948ccd5b3ba00b51eefc59bb092.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y61AZXsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-eclipse.png)

我无法为 Eclipse 找到一个足够好的解决方案，但是以下是您可以尝试探索的两个方向:

1.  进入`Help -> Eclipse Marketplace`，搜索[“yedit”](https://github.com/oyse/yedit)并安装。然后转到`Window -> Preferences -> General -> Editors -> File Associations`并将`*.sls`扩展与 YEdit 关联起来。这使得普通 YAML 文件的语法高亮显示(尽管 Jinja 没有这种运气)。

2.  另一个插件叫做 [LiClipseText](http://www.liclipse.com/text/supported_languages.html) 。它支持 YAML 和 Jinja 语法，但不能同时为一个文件启用它们。不过这应该是可能的，如果你想做贡献，这里有[一些提示](https://github.com/fabioz/LiClipseText/issues/13)。

缩进导轨可通过单独的[缩进导轨](https://marketplace.eclipse.org/content/indent-guide)插件获得。

## Emacs

[![SaltStack mode for Emacs](img/b6ca079db8d3732b3c1e1a68b8bbca11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_vj7hdL1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-emacs.png)

盐模式的是你最好的选择。它基于`yaml-mode`和`mmm-mode`，并自动为`*.sls`文件启用。最简单的安装方式是通过 MELPA。

#### 特性

*   `*.sls`文件的语法高亮显示(也可以启用`*.jinja`语法支持)
*   表达式和语句的缩进和对齐
*   用`flyspell`对注释进行拼写检查
*   使用`salt-mode-browse-doc`浏览状态模块的文档
*   在盐状态函数之间快速移动

#### 注意事项

*   还没有自动完成
*   它在引擎盖下使用 [yaml-mode](https://github.com/yoshiki/yaml-mode/) ，需要一个新的维护者。如果你愿意介入，有许多[问题](https://github.com/yoshiki/yaml-mode/issues)需要你去解决。

#### 其他事情要尝试

*   缩进工具和 [hydra](https://github.com/abo-abo/hydra) 对于像 YAML 和 Python 这样对空白敏感的语言来说，看起来是一个很好的组合。
*   [高亮-缩进-引导](https://github.com/DarthFennec/highlight-indent-guides)值得一看(虽然它增加了更多的视觉混乱)。还有一个[十字准线高亮](https://www.emacswiki.org/emacs/CrosshairHighlighting)模式。
*   如果使用 [Dash API 文档浏览器](https://kapeli.com/dash)，安装[Dash-in-point](https://github.com/stanaka/dash-at-point)
*   为了突出显示其他 Jinja 模板化的配置文件，建立使用`*.jinja`扩展名命名它们的约定，并在您的`init.el`中添加以下行(更多细节参见[自述文件](https://raw.githubusercontent.com/purcell/mmm-mode/master/README)):

```
(mmm-add-mode-ext-class nil "\\.jinja\\'" 'jinja2) 
```

## 凯特

[![SaltStack syntax highlighter for Kate](img/cf52aa1986b9135c0d6482e91155fb5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J8CkTrHY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-kate.png)

Kate-jinja2-highlighting 有一堆不同文件类型的语法荧光笔，最上面是 jinja。

#### 特性

*   使用 Jinja2 语法突出显示`*.jinja`、`*.jinja2`和`*.j2`
*   使用 YAML+Jinja2 语法高亮显示`*.yaml`、`*.yml`和`*.sls`文件(如果你应用了下面的补丁)
*   如果要启用缩进导轨，切换`Settings -> Configure Kate -> Editor Component -> Appearance -> Show indentation lines`

要安装它，将`*.xml`文件复制到`~/.local/share/org.kde.syntax-highlighting/syntax/`文件夹中。然后到`Settings -> Configure Kate -> Editor Component -> Open/Save -> Modes & Filetypes`，选择`Markup/Jinja2/YAML`，打开`Variables`下拉框，然后将`indent-width` `tab-width`都设置为`2`，将`Indent using`设置为`Spaces`。

您还需要将`*.sls`文件与语法 highlighter:
相关联

```
sed -i'' -e 's/\(\*\.yml\)"/\1;*.sls"/' yaml-jinja2.xml 
```

上面的`sed`命令相当于下面的补丁:

```
--- yaml-jinja2.xml.orig    2018-10-25 02:39:31.000000000 +0700
+++ yaml-jinja2.xml 2018-10-25 02:39:40.000000000 +0700
@@ -3,7 +3,7 @@
 <!-- Author: Dr Orlovsky MA <maxim@orlovsky.info> //-->
 <!-- Autogenerated from yaml.xml -->
 <language name="Jinja2/YAML" version="1.2" kateversion="2.3" section="Markup"
- extensions="*.yaml;*.yml" mimetype="text/yaml"
+ extensions="*.yaml;*.yml;*.sls" mimetype="text/yaml"
           author="Dr Orlovsky MA (dr.orlovsky@gmail.com)" license="LGPL">
   <highlighting>
     <contexts> 
```

## 午夜指挥官

[![SaltStack syntax highlighter for Midnight Commander](img/3e363dd64856f2442b71c76c685fd730.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vnrrwuyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-midnight-commander.png)

对于我们这些在 90 年代使用[正统文件管理器](https://en.wikipedia.org/wiki/File_manager#Orthodox_file_managers)的人来说,《午夜指挥官》是一个老掉牙的好东西，没有它就无法生活。就我个人而言，我使用它来快速导航文件系统树并查看文件，而不用在我的主编辑器中打开它们。根据[这张票](https://midnight-commander.org/ticket/3293)，开箱后，`mcedit`高亮显示`*.yml`和`*.yaml`文件并理解简单的 Jinja 变量(虽然不支持标签)。而且，它不理解`*.jinja`文件。

为了使`*.sls`高亮显示，你需要复制默认的`Syntax`文件并稍微调整一下:

```
mkdir -p ~/.config/mc/mcedit
sed -e 's/\(|YML)\)/|sls\1/' /usr/share/mc/syntax/Syntax > ~/.config/mc/mcedit/Syntax 
```

上面的`sed`命令相当于下面的补丁:

```
--- /home/user/.config/mc/mcedit/Syntax.orig 2017-08-05 04:03:29.000000000 +0700
+++ /home/user/.config/mc/mcedit/Syntax 2018-10-10 19:50:11.390542735 +0700
@@ -271,7 +271,7 @@
 file ..\*\\.cl$ OpenCL\sProgram
 include opencl.syntax

-file ..\*\\.(ya?ml|YML)$ YAML\sFile
+file ..\*\\.(ya?ml|sls|YML)$ YAML\sFile
 include yaml.syntax

 file .\*\\.osl$ OSL\sProgram 
```

## PyCharm

[![SaltStack support in PyCharm](img/9e28b8cf529f5408fb3722a777436a05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x5rzi00C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-pycharm.png)

嗯，看起来 PyCharm 没有特定于 Salt 的插件。Ansible 用户在这里更幸运——py charm 支持 [JSON 模式](http://schemastore.org/json/)，来自 RedHat 的某人已经创建了 [one](https://github.com/shaded-enmity/ansible-schema-generator) 。这意味着特定于 Ansible 的 YAML 数据结构的代码自动完成开箱即用！另外，还有一个独立的 [YAML/Ansible](https://plugins.jetbrains.com/plugin/7792-yaml-ansible-support) 插件( [GitHub](https://github.com/vermut/intellij-ansible/) )。

然而(除了使用 Ansible 插件之外)，您可以调整 PyCharm，使其适用于 Salt 状态。点击`File -> Settings -> Languages & Frameworks -> Python Template Languages`，在`Template language`下拉列表中选择`Jinja2`，点击“+”按钮，选择`YAML`。这将使 YAML+金贾组合的语法高亮显示。要将其与`*.sls`文件相关联，请转到`File -> Settings -> Editor -> File Types`，找到`YAML`并添加`*.sls`扩展名。

#### 其他特性

*   你可以用`Reformat Code`动作或者通过一个快捷方式重新格式化 YAML 代码:在 MacOS 上是`Cmd + Alt + L`;在 Windows 和 Linux 上是`Ctrl + Alt + L`
*   要自定义 YAML 代码风格设置，请访问`File -> Settings -> Editor -> Code Style -> YAML`
*   默认情况下，缩进参考线是启用的，但是您可以通过`File -> Settings -> Editor -> General -> Appearance -> Show indent guides`禁用它们

## 崇高的文字

[![SaltStack plugin for Sublime Text](img/a334800915180a533b9647b3e2e489f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DFwH9SQ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-sublime-text.png)

该扩展可在 [GitHub](https://github.com/saltstack/sublime-text) 上获得。要安装它，点击`Preferences -> Package Control -> Install Package`，搜索`saltstack`并点击安装。还建议为 Jinja 安装[独立扩展](https://github.com/kudago/jinja2-tmbundle)(进入`Package Control`搜索`jinja2`)。

#### 特性

*   `*.sls`文件的语法高亮显示
*   如果你安装了 Jinja 扩展，语法高亮显示`*.j2`文件
*   金贾的几个[片段](https://github.com/saltstack/sublime-text/tree/master/Snippets/Jinja)(叉自[崇高-金贾 2](https://github.com/jpvanhal/sublime-jinja2) )
*   一些常用的盐状态模块的[片段](https://github.com/saltstack/sublime-text/tree/master/Snippets/SaltStack)，比如`file`和`pkg`

默认情况下，缩进参考线是启用的，但是您也可以在设置中设置`"indent_guide_options": ["draw_normal", "draw_active"]`来高亮显示当前活动的参考线。

## Vim

[![SaltStack plugin for Vim](img/f0168ddf73cd444a6e1007cbd8921844.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xbxriINJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-vim.png)

你需要 [salt-vim](https://github.com/saltstack/salt-vim.git) 和 [vim-jinja2-syntax](https://github.com/Glench/Vim-Jinja2-Syntax.git) 。

#### 特性

*   `*.sls`文件和`Saltfile`的语法高亮显示
*   `*.jinja2`、`*.j2`和`*.jinja`文件的语法高亮显示
*   正确的缩进设置
*   `Space`按键将尝试折叠/展开一个区域
*   用`<`和`>`可以缩进或不缩进一个可视选择的块

如果您使用的是 Vim 8 或更高版本，您可以跳过病原体/病毒途径，直接安装插件:

```
mkdir -p ~/.vim/pack/plugins/start
cd !$
git clone https://github.com/saltstack/salt-vim.git salt.vim
git clone https://github.com/Glench/Vim-Jinja2-Syntax.git jinja2.vim 
```

然后将下面几行添加到您的`.vimrc` :

```
syntax on
set nocompatible
set modeline
filetype plugin indent on
let g:sls_use_jinja_syntax = 1 
```

你也可以考虑 [vim-indentguides](https://github.com/thaerkh/vim-indentguides) 、 [vim-indent-guides](https://github.com/nathanaelkane/vim-indent-guides) 或 [indentLine](https://github.com/Yggdroot/indentLine) 。有些人使用[十字准线突出显示](http://vim.wikia.com/wiki/Highlight_current_line)。

## Visual Studio 代码

[![SaltStack extension for Visual Studio Code](img/097801382d20f8041bedee550a19d82c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qsvVwRFe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://salt.tips/text-editor-plugins-for-salt-states-and-yaml-jinja/salt-vscode.png)

SaltStack 的 VSCode 扩展可在[市场](https://marketplace.visualstudio.com/items?itemName=korekontrol.saltstack)上获得。要安装它，点击`File -> Preferences -> Extensions`，然后搜索`saltstack`并点击安装。源代码可以在 [GitHub](https://github.com/korekontrol/vscode-saltstack) 上找到

#### 特性

*   对`*.sls`文件进行语法高亮显示(理解 YAML+金贾组合)
*   Jinja 括号的自动补全
*   YAML 缩进线的折叠
*   还支持`*.j2`和`*.jinja`扩展

如果您对自动补全功能添加的额外右括号`{%%}}`感到恼火，请将以下代码片段添加到您的`settings.json`文件(`File -> Preferences -> Settings`，然后按`{}`图标打开用户设置):

```
"[sls]":  {  "editor.autoClosingBrackets":  "never"  } 
```

## 最终提示

#### 为团队

如果你有一个工程师团队，他们定期为一个共同的盐湖城状态树做贡献，那么建立一些 Salt 金贾风格的约定是必要的。

您可以向每个状态文件添加以下样板文件，但是这很容易忘记，并且需要一些训练:

```
# -*- coding: utf-8; mode: salt; -*-
# vim: ft=sls 
```

或者，您可以要求工程师正确设置他们的编辑器。或者你可以采用[http://editorconfig.org/](http://editorconfig.org/)——只需在你的项目根中创建一个`.editorconfig`，任何兼容的编辑器都会自动选取你的编码约定。

此外，向 Salt 管理的所有配置模板添加`.jinja`或`.j2`后缀是有意义的，可以帮助大多数编辑器识别语法。

#### YAML 文档

*   [YAML 备忘单](https://kapeli.com/cheat_sheets/YAML.docset/Contents/Resources/Documents/index)(也可与[破折号](https://kapeli.com/dash)一起使用)
*   [YAML 格式](https://symfony.com/doc/current/components/yaml/yaml_format.html)(来自 Symfony 文档)
*   [YAML 多线](https://yaml-multiline.info/)(YAML 多线字符串的一个不错的备忘单)
*   [了解 YAML](https://docs.saltstack.com/en/latest/topics/yaml/) (来自盐务文献)
*   [YAML 的特质](https://docs.saltstack.com/en/latest/topics/troubleshooting/yaml_idiosyncrasies.html)(来自盐务文件)
*   [YAML 规范](http://yaml.org/spec/1.2/spec.html)(权威且全面，但不那么容易读懂)

#### 晋嘉文件

*   [Jinja 模板设计器文档](http://jinja.pocoo.org/docs/templates/)(请选择合适的 Jinja 版本以匹配您的 Salt 环境中安装的版本)
*   [了解金贾](https://docs.saltstack.com/en/latest/topics/jinja/index.html)(来自盐务文书)

#### 在线 YAML 解析器/解析器

如果你不想或者不能使用任何编辑器插件，这些可能会有帮助:

*   [https://github.com/Inveracity/jinjabread](https://github.com/Inveracity/jinjabread)(专为盐而造)
*   [http://yaml-online-parser.appspot.com/](http://yaml-online-parser.appspot.com/)
*   [https://www.json2yaml.com/](https://www.json2yaml.com/)
*   [http://www.yamllint.com/](http://www.yamllint.com/)

此外，YAML 是 JSON 的[超集，所以你可以在列表中使用方括号，在字典中使用圆括号:](http://yaml.org/spec/1.2/spec.html#id2759572) 

```
/etc/http/conf/http.conf:
  file.managed:
    - source: salt://apache/http.conf
    - mode: 644
    - template: jinja
    - context: { custom_var: "override" }
    - defaults: { custom_var: "default value", other_var: 123 } 
```

* * *

如果你想得到这样的提示，你可以订阅[邮件列表](https://tips.us8.list-manage.com/subscribe?u=79863cb6907503346da4ed704&id=9de0347807)。

另外，你可以在 Twitter 上关注我，我会定期发布这样的消息:

[![SaltTips tweet](img/392adbdf019b67d5a9ebfe47c4045ddb.png)T2】](https://twitter.com/SaltTips)