# 分享您的 Python 代码

> 原文：<https://dev.to/lukaszkuczynski/sharing-your-python-code-58fd>

# 问题

前一篇文章是为了展示我如何帮助自己对保存在文件中的文本进行拼音化。但是如何分享呢？我可以告诉使用`requirements.txt`文件安装需求，然后使用`python script params`运行正确的脚本。但是..许多 Python 工具非常方便，因为它们是独立的工具，你可以直接在 Bash 中运行，例如`pip`、`pytest`等等。我决定将我的小模块具体化为一个安装包。后来，我希望把它放在 Python 模块的外部网站上，这样就可以让它进入`pip install ..`。

# 解

Python 附带了`setuptools`包，让你可以将运行整个机器所需的所有文件打包到整个文件夹中。您可以将测试和源文件存储在不同的文件夹中，并通过包进行绑定。这是示例性的包装定义。

```
from setuptools import setup

setup(
        name='pinmix',
        version='0.1',
        packages=['pinmix'],
        install_requires=['jieba','xpinyin'],
        entry_points={
            'console_scripts':['pinmix=pinmix.cmdline:main']
        }
) 
```

Enter fullscreen mode Exit fullscreen mode

我们有一些可用的选项:

*   `name`
*   `packages`:输出包中包含哪些文件夹
*   `install_requires`:安装软件包时，需要自动安装哪些软件包
*   什么样的脚本将被安装在你的 Python 路径上以使你的模块运行更容易？

定义了入口点之后，我们可以直接从命令行运行它。

还有一个选项来定义入口点，但是我在这里使用的这个似乎更容易，并且是完全可测试的。您可以导入`cmdline`模块，并像对任何其他模块一样测试它。项目由 Travis 构建和管理。我们将很快回到那个漂亮的 CD 工具。

# TL；速度三角形定位法(dead reckoning)

使用 setuptools，我能够提供可测试的小模块，您可以轻松地将其与您的系统集成，并使用短命令`pinmix`调用。
Github 的[这里有完整的回购](https://github.com/lukaszkuczynski/pinmix)