# Python 脚本工具箱:第 2 部分-字符串模板和“argparse”

> 原文：<https://dev.to/rpalo/python-scripting-toolbox-part-2---string-templates-and-argparse-4lbh>

*菲利普·斯温伯恩在 Unsplash 上拍摄的封面照片*

这是 Python 脚本工具箱系列的第 2 部分。这是一个由三部分组成的关于 Python 脚本工具的调查。我通过创建三个展示标准库不同部分的脚本来展示这个功能。

1.  在[第一部分](https://assertnotmagic.com/2018/06/05/python-toolbox-shout/)中，我们构建了`shout.py`:一个脚本，它喊出你传递给它的一切。
2.  在第 2 部分中，我们将创建`make_script.py`:一个从模板生成启动脚本的脚本，用于像[项目欧拉](https://projecteuler.net/)或[罗莎琳德](http://rosalind.info/problems/locations/)这样的事情。
3.  下一次，第 3 部分将以`project_setup.py`为特色:创建基本项目框架的脚本

现在，让我们开始吧。

## 剧本二:`make_script.py`

在进行编码挑战时，您应该在本地机器上编写代码，一旦找到答案就提交，您会发现自己一遍又一遍地重写许多相同的模板，以便读入命令行参数、解析内容并输出结果，而您工作的真正内容是脚本的主要功能。处理输入和输出只是一项次要任务。如果我们可以把这些繁重的工作用脚本写出来，然后尽快开始解决编码问题，这不是很好吗？这就是我们今天要做的。

这是我们的要求。我们正在创建一个名为`make_script.py`的 Python 脚本。我们希望它从模板创建一个 Python 脚本，根据我们的用户输入填充一些变量名、文档字符串或其他小的变化。如果它能有合理的违约，那将是一个优势。让我们开始吧。

### 第一步:模板

首先，我想提出我们的模板应该是什么样子。打开一个名为`script.py.template`的文件。那不是惯例，是我编的一个文件结尾。你想怎么叫都行。

```
"""$docstring"""

import sys

def main($input):
    $output = ""
    return $output

if __name__ == "__main__":
    $input = sys.argv[1]
    $output = main($input)
    print($output) 
```

Enter fullscreen mode Exit fullscreen mode

没有太多，也不太花哨，但应该可以节省我们的打字时间。

但是，瑞恩！这些美元符号是怎么回事？我以为这是 Python，不是 PHP！

你是正确的。我们将在 Python 的标准库的`string`模块中使用`template`类。值得注意的是，有几个非常好的模板库不在标准库中，但功能更强大。 [Jinja2](http://jinja.pocoo.org/docs/2.10/) 和 [Django 模板](https://docs.djangoproject.com/en/2.0/ref/templates/api/)马上浮现在脑海里。但这会带我们去我们该去的地方。

使用这种模板语言，我们只需指定一个前面带有美元符号的变量。如果你想显示一个*实际的*美元符号，只需连续使用两个美元符号(`$$`)。这将呈现为一个美元符号。现在，让我们看看实际的代码。

### 第二步:填写模板

实际填充这个模板的代码并不多。创建一个名为`make_script.py`的新文件。

```
"""Creates a script from a basic template."""

import string

with open("script.py.template", "r") as f:
    template_text = f.read()

data = {
    "docstring": "Hey look at this cool script.",
    "input": "dat_arg",
    "output": "awesome_result"
}

template = string.Template(template_text)
result = template.substitute(data)

with open("new_script.py", "w") as f:
    f.write(result)

print(result)
print("----")
print("Script created!") 
```

Enter fullscreen mode Exit fullscreen mode

基本上有四个步骤:

1.  将模板读入一个字符串。
2.  从这个字符串创建一个模板对象(由标准库的`string`模块提供)。
3.  数据替代。这可以作为`substitute`方法的关键字参数来完成，或者(就像我们做的那样)作为一个字典来完成。无论哪种方式，关键字应该是模板中定义的变量的名称，值应该是您想要在中替换的内容。
4.  将新处理的结果写入新文件。

如果您尝试运行`python make_script.py`，您应该在您的终端以及一个名为`new_script.py`的新文件中看到替换的结果。很酷，是吧？

这很好，但是我们不希望在任何时候我们想要创建一个新的脚本时都不得不去改变`make_script.py`中的值。我们希望我们的脚本更动态一点，也许有一个更好的用户界面。看起来是时候…

### 第三步:`argparse`走向伟大

我们希望我们的脚本接受一些参数，一些选项，并可能显示一个帮助消息。我想再一次指出，如果您想要更强大的功能，可以使用一些优秀的 CLI 库。我觉得[点击](http://click.pocoo.org/5/)大概是我最喜欢的。我前阵子写了一篇关于[使用 Click](https://assertnotmagic.com/2016/11/27/discovering-click/) 的文章。温柔一点——这是我的第一篇博文！

不管怎样，我们已经承诺在这些指南中只使用*标准库*，所以我们将和我们的朋友`argparse`一起坚持下去。更多例子和信息，可以看一下 [`argparse`文档](https://docs.python.org/3/library/argparse.html)。现在，我认为最好是给你展示新的闪亮版本的`make_script.py`。

```
"""Creates a script from a basic template."""

import argparse
import string

parser = argparse.ArgumentParser(description="Create new Python scripts from a template.")
parser.add_argument("scriptname", help="The name of the new script to create")
parser.add_argument(
    "-d", 
    "--docstring",
    help="The docstring to be placed at the top of the script",
    default="Placeholder docstring"
)
parser.add_argument(
    "-i",
    "--input",
    help="The name of the variable used as the input parameter",
    default="inval"
)
parser.add_argument(
    "-r",
    "--result",
    help="The name of the variable used as the result/output",
    default="result"
)

args = parser.parse_args()

# ...  You'll see how we use these args in a minute 
```

Enter fullscreen mode Exit fullscreen mode

一旦导入了`argparse`模块，我们就可以创建参数解析器了。我们将告诉这个参数解析器我们期望的所有参数和选项。默认情况下，任何以`-`开头的参数都被认为是可选的……嗯……选项，而其他所有参数都被认为是必需的。

如果您为每个参数提供一个`help`值，这将使您的帮助文本看起来非常闪亮。最后，您用`parse_args`方法处理用户提供的参数。让我们来看看如何使用它们。

```
# ... Everything in the previous code block 
with open("script.py.template", "r") as f:
    template_text = f.read()

data = {
    "docstring": args.docstring,
    "input": args.input,
    "output": args.result
}

template = string.Template(template_text)
result = template.substitute(data)

with open(args.scriptname, "w") as f:
    f.write(result)

print(result)
print("----")
print("Script created!") 
```

Enter fullscreen mode Exit fullscreen mode

所有的参数都可以在`args` *名称空间*下获得，这基本上意味着你可以通过`args.whatever_your_variable_is`访问它们。变量名将是您传递给`add_argument`方法的任何名称。

现在，如果你运行你的脚本，它会抱怨没有传入正确的东西，*和*如果你运行`python make_script.py —help`，它会打印出一个非常小的帮助信息。

```
$ python make_script.py --help
usage: make_script.py [-h] [-d DOCSTRING] [-i INPUT] [-r RESULT] scriptname

Create new Python scripts from a template.

positional arguments:
  scriptname            The name of the new script to create

optional arguments:
  -h, --help            show this help message and exit
  -d DOCSTRING, --docstring DOCSTRING
                        The docstring to be placed at the top of the script
  -i INPUT, --input INPUT
                        The name of the variable used as the input parameter
  -r RESULT, --result RESULT
                        The name of the variable used as the result/output 
```

Enter fullscreen mode Exit fullscreen mode

下一次，我们将编写一个为我们构建项目目录的脚本。感谢阅读！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/06/16/python-toolbox-make-script/)*