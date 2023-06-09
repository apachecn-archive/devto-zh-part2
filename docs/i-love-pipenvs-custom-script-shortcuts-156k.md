# 我喜欢 pipenv 的自定义脚本快捷方式

> 原文：<https://dev.to/elanorigby/i-love-pipenvs-custom-script-shortcuts-156k>

更新:我觉得下面的文档有点无聊，所以我提交了一份做了一些修改的 PR，它们被接受了！🎉来看看-[https://pipenv . readthe docs . io/en/latest/advanced/# custom-script-shortcut s](https://pipenv.readthedocs.io/en/latest/advanced/#custom-script-shortcuts)T3】

从[文档](https://pipenv.readthedocs.io/en/latest/advanced/#custom-script-shortcuts):

### ☤自定义脚本快捷键

Pipenv 支持自定义`scripts`部分的快捷方式。`pipenv run`会自动加载并找到正确的命令替换。给定 Pipfile:

```
 [scripts]
    printfoo = "python -c \"print('foo')\"" 
```

Enter fullscreen mode Exit fullscreen mode

您可以在您的终端中键入运行:

```
 $ pipenv run printfoo
    foo 
```

Enter fullscreen mode Exit fullscreen mode

/ *结束文档*

而且真的就这么简单！无论您是否激活了 pipenv shell，它都会使用 pipenv 虚拟环境上下文运行您给它的脚本。我发现这对于运行测试、单个模块和定制命令非常有用。

例如，我使用 autopep8 库，这样我就可以保持我的空白漂亮，而不需要手动添加额外的行，所以我在 Pipfile 的`[scripts]`部分有这样一行:

```
pep8 = "autopep8 -riv --max-line-length 150" 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，我就能跑`pipenv run pep8 <file or directory name>`噗！每个文件末尾一行，函数之间两行，方法之间一行，等等。(注意:我并不认为 150 是一个好的最大行长度，我只是不喜欢工具自动换行，所以我宁愿手动选择。)

### 告诫

据我所知，在 pipenv shell 激活的情况下，您可以在终端中运行的任何东西都可以工作，但值得注意的是，您不能链接命令。我无法让`&&`、`||`或`;`开始工作。这并没有真正困扰我，因为如果你真的需要创建一个多命令脚本，你可以创建一个实际的脚本文件并从`[scripts]`运行它。

例如，我有一个有点复杂的脚本运行我的测试套件(主要是添加颜色),并且我在`scripts/`目录中有一个`test.sh`文件。这是我的 Pipfile 中的内容:

```
[scripts]
tests = "./scripts/test.sh" 
```

Enter fullscreen mode Exit fullscreen mode

我输入`pipenv run tests`然后它就出现了。

如果你还没有使用过 pipenv 的这个特性，那就试试吧！让我知道你最终用它做什么=)

如果您还没有转而使用 pipenv，那么可以把它看作是一种激励。我刚刚从`requirements.txt`和 virtualenv/pyenv 切换了这个项目，它一点也不差。在我们的持续集成/部署系统中寻找所有必要的变更需要一点坚持，但是变更本身并不困难。

欢呼吧🐍🙆🏻‍♀️