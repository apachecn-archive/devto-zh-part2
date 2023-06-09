# 安装 phpDocumentor 问题已解决！

> 原文：<https://dev.to/codingfix1/installing-phpdocumentor-issues-solved-c8o>

安装 phpDocumentor 应该很容易。至少，我在网上到处看到，当然除了在官方网站上，你有很多选择来安装它，一个比以前的更容易！

所以我绝对有信心在几分钟内我就能运行 phpDocumentor 并为我的新 Php 类创建文档。

你知道吗？我错了。事情是这样的。

## phpDocumentor.phar

我尝试的第一件事是使用。phar 文件。我知道他们建议的第一种方法是用梨，但我有过使用梨的正面经验。一天前的 phar 文件:我想在 Atom 中安装并运行 php-cs-fixer，经过几次尝试后，我最终使用了 php-cs-fixer.phar 文件。因此，当 ti 开始安装 phpDocumentor 时，我告诉自己:“当我可以下载一个很棒的文件并放在我的 php 目录中时，我为什么要纠结于命令行、依赖问题和其他问题呢？”搞定了。一旦我安装了 phpDocumentor.phar，我就直接在我的类文件夹中打开命令提示符并键入

```
phpdoc -d "." -t "./docs" 
```

Enter fullscreen mode Exit fullscreen mode

呜呜呜！！！

无法打开输入文件:“phpDocumentor.phar”

## 用 Composer 安装 phpDocumentor

呜哇，这是什么？开始谷歌了一下，但没有成功，所以我很遗憾地放弃了。法尔文件和我继续作曲。比以前更糟！

```
phpdoc is not recognized as an internal or external command 
```

Enter fullscreen mode Exit fullscreen mode

去谷歌还是不去谷歌？不，我不想浪费时间去理解为什么一个东西被告知工作不工作(不说 Composer 本身，这是我在几个场合使用的很棒的工具，只说通过 Composer 安装 phpDocumentor)。归档尝试。

## 用 PEAR 安装 phpDocumentor

我一直有通过 PEAR 安装 php 扩展的良好经验，所以我认为它甚至可以安装 phpDocumentor。我输入了下面的命令，并急切地等待着响应:

```
pear channel-discover pear.phpdoc.org
pear install phpdoc/phpDocumentor 
```

Enter fullscreen mode Exit fullscreen mode

哇，一切似乎都很顺利！这个过程完成后，我再次尝试创建我的文档，输入通常的命令:

```
phpdoc -d "." -t "./docs" 
```

Enter fullscreen mode Exit fullscreen mode

啊啊啊啊！！！命令提示符窗口已经充满了许多与 Smarty 模板相关的警告和错误！

## 解

我就不告诉你细节了。我只是说三四十分钟的谷歌搜索给了我解决方案:直接从 Github 下载 2.9.0 版本。我去了[https://github . com/phpDocumentor/phpDocumentor 2/releases/tag/v 2 . 9 . 0](https://github.com/phpDocumentor/phpDocumentor2/releases/tag/v2.9.0)点击到名为 phpDocumentor-2.9.0.tgz 的压缩文件下载并...不要问我为什么，也许那天我非常不走运，但是下载非常慢，它停止了几次，一旦完成，档案被破坏了！

## 真解！

幸运的是，我找到了另一个允许下载该文件的网站:[https://fossies.org/linux/www/phpDocumentor-2.9.0.tgz/](https://fossies.org/linux/www/phpDocumentor-2.9.0.tgz/)。终于，我拿到文件了。我把它放在一个临时文件夹中，打开命令提示符，简单地输入:

```
pear install phpDocumentor-2.9.0.tgz 
```

Enter fullscreen mode Exit fullscreen mode

对文件 phpdoc.bat 的一个小调整仍然丢失。进入你的 php 文件夹(在我的 Windows 10 分区我安装了 xampp，所以我的 php 位于 C:\xampp\php 文件夹)用文本编辑器打开 phpdoc.bat。应该是这样的:

```
@echo off
if "%PHPBIN%" == "" set PHPBIN=php.exe
if not exist "%PHPBIN%" if "%PHP_PEAR_PHP_BIN%" neq "" goto USE_PEAR_PATH
GOTO RUN
:USE_PEAR_PATH
set PHPBIN=%PHP_PEAR_PHP_BIN%
:RUN
"%PHPBIN%" "%PHP_PEAR_BIN_DIR%\phpdoc" %* 
```

Enter fullscreen mode Exit fullscreen mode

只需更改最后一行，将“%PHP_PEAR_BIN_DIR%\phpdoc”替换为 phpdoc 文件的完整路径。在我的例子中，生成的 phpdoc.bat 文件如下所示:

```
@echo off
if "%PHPBIN%" == "" set PHPBIN=php.exe
if not exist "%PHPBIN%" if "%PHP_PEAR_PHP_BIN%" neq "" goto USE_PEAR_PATH
GOTO RUN
:USE_PEAR_PATH
set PHPBIN=%PHP_PEAR_PHP_BIN%
:RUN
"%PHPBIN%" "C:\xampp\php\phpdoc" %* 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已，现在成功了！我有我最喜欢的模板的完整文档:

```
phpdoc -d "." -t "./docs" --template="responsive-twig" 
```

Enter fullscreen mode Exit fullscreen mode

如果你觉得这篇文章有用，请通过你的社交网络分享。如果你想给我留言，我会很高兴收到你的来信！

安装 phpDocumentor 的问题解决了！最早出现在 [codingfix](https://codingfix.com) 上。