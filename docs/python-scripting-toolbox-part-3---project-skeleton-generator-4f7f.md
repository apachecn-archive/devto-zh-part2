# Python 脚本工具箱:第 3 部分——项目框架生成器

> 原文：<https://dev.to/rpalo/python-scripting-toolbox-part-3---project-skeleton-generator-4f7f>

*米凯尔·克里斯滕森在 Unsplash 上拍摄的封面图片*

这是 Python 脚本工具箱系列的第 3 部分。这是一个由三部分组成的关于 Python 脚本工具的调查。我通过创建三个展示标准库不同部分的脚本来展示这个功能。在这个例子中，我们将使用`sys`、`os`、`shutil`、`pathlib`和`argparse`。请记住，我在这里使用的是 Python 3.6，所以您将看到一些 shwanky 特性，如“f-strings”和`pathlib`模块，它们在早期版本中不一定可用。如果可以的话，尝试使用 3.6(或至少 3.4)版本。

1.  在[第一部分](https://assertnotmagic.com/2018/06/05/python-toolbox-shout/)中，我们构建了`shout.py`:一个脚本，它喊出你传递给它的一切。
2.  在[第二部分](https://assertnotmagic.com/2018/06/16/python-toolbox-make-script/)中，我们创建了`make_script.py`:一个从模板生成起始脚本的脚本，用于像[项目欧拉](https://projecteuler.net/)或[罗莎琳德](http://rosalind.info/problems/locations/)这样的事情。
3.  在第 3 部分中，我们将创建`project_setup.py`:一个创建基本项目框架的脚本，并将一些新工具添加到我们的工具箱中。

现在，让我们开始吧。

## `project_setup.py`

再一次，在我们开始之前，重要的是要说有许多很棒的库已经为你做了这些。Cookiecutter 可能是最受欢迎的。它功能齐全，有很棒的文档，还有一个由插件和预制模板组成的庞大生态系统。现在我们已经解决了这个问题，是时候大胆地使用标准库重新发明轮子了！前进！

下面是对`project_setup.py`的要求。它需要接受一个输入，将它指向要复制的源模板，可能带有一些合理的默认值和错误检查。它还需要知道将副本放在哪里。最重要的是，它必须将源模板复制到目标位置。

## 初稿:基本功能

我们需要一个老朋友和两个新朋友来开始:[`sys`](https://docs.python.org/3/library/sys.html)[`pathlib`](https://docs.python.org/3/library/pathlib.html)，和 [`shutil`](https://docs.python.org/3/library/shutil.html) 。我们将使用`sys`来处理我们的输入参数(至少现在是这样)，使用`pathlib`以人性化的方式处理文件路径，使用`shutil`来完成复制方面的繁重工作。这是我们的第一次切割。

```
# project_skeleton.py 
import pathlib
import shutil
import sys

def main(source_template, destination):
    """
    Takes in a directory as a template and copies it to the requested
    destination.
    """

    # We'll use 'expanduser' and 'resolve' to handle any '~' directories
    # or relative paths that may cause any issues for `shutil`
    src = pathlib.Path(source_template).expanduser().resolve()
    dest = pathlib.Path(destination).expanduser().resolve()

    if not src.is_dir():
        exit("Project Skeleton: Source template does not exist.")

    if dest.is_dir():
        exit("Project Skeleton: Destination already exists.")

    shutil.copytree(src, dest)
    print(f"Project Skeleton: Complete! {src} template copied to {dest}")

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print(f"Usage: {sys.argv[0]} SOURCE_TEMPLATE_PATH DESTINATION_PATH")
    source, destination = sys.argv[1:]
    main(source, destination) 
```

Enter fullscreen mode Exit fullscreen mode

`pathlib`是一个*超级*强大的库，可能是我在本教程中最喜欢的一个。它有许多高级的易读的方法，它是跨平台的，没有太多的麻烦，这使得你再也不用输入另一个该死的`os.path.join`(大多数情况下)。我最喜欢的一点是，它重载了除法斜杠(`/`)，这样“划分”两个路径或者一个路径和一个字符串就创建了一个新路径，而不管你在什么平台上:

```
p = pathlib.Path("/usr")
b = p / "bin"
print(b)
# => /usr/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们实际上只将`shutil`用于一件事:将目录树从一个地方复制到另一个地方。`shutil`将成为您复制、移动、重命名、覆盖和获取文件和目录数据的首选模块。

最后，我们使用 good ole' `sys`来做一些基本的输入参数验证。

## 第二关:参数解析

就像上次一样，我想添加一些参数解析，通过`argparse`。不像上次需要那么多选择。

```
import argparse
# ... The other imports and main function don't change. 
if __name__ == "__main__":
    parser = argparse.ArgumentParser(
        description="Generate a project skeleton from a template.")
    parser.add_argument(
        "source", help="The directory path of the source template.")
    parser.add_argument("destination", help="The location of the new project.")

    args = parser.parse_args()
    main(args.source, args.destination) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的命令有了很好的参数计数、用法信息和帮助信息！

## 第三关:拥有模板目录

这个脚本会工作得很好，但是我们每次使用它时都必须键入模板框架的完整路径，就像某些农民一样！让我们用一个简单的技巧来实现一些合理的级联默认值。

```
"""Generates a project skeleton from a template."""

import argparse
import os        # <= Adding an 'os' import.  You'll see why below. import pathlib
import shutil

def main(source_template, destination):
    """
    Takes in a directory (string) as a template and copies it to the requested
    destination (string).
    """
    src = pathlib.Path(source_template).expanduser().resolve()
    dest = pathlib.Path(destination).expanduser().resolve()

    if not src.is_dir():
        exit(f"Project Skeleton: Source template at {src} does not exist.")

    if dest.is_dir():
        exit(f"Project Skeleton: Destination at {dest} already exists.")

    shutil.copytree(src, dest)
    print(f"Project Skeleton: Complete! {src} template copied to {dest}")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(
        description="Generate a project skeleton from a template.")

    # I want to tweak the 'source' argument, since now all we want
    # is for the user to provide the name of the template.
    parser.add_argument(
        "template", help="The name of the template to use.")
    parser.add_argument("destination", help="The location of the new project.")

    # This is the magic.  We're going to add an argument that specifies where
    # our templates live.  If the user doesn't specify, we'll see if they have
    # an environment variable set that tells us where to look.  If they don't
    # have that, we'll use a sane default.
    parser.add_argument(
        "-d",
        "--template-dir",
        default=os.environ.get("SKELETON_TEMPLATE_DIR") or "~/.skeletons",
        help="The directory that contains the project templates.  "
              "You can also set the environment var SKELETON_TEMPLATE_DIR "
              "or use the default of ~/.skeletons."
    )

    args = parser.parse_args()

    # One last tweak: We want to append the name of the template skeleton
    # to the root templates directory.
    source_dir = pathlib.Path(args.template_dir) / args.template
    main(source_dir, args.destination) 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！你可以通过多种方式调用你的脚本:

```
$ python project_skeleton.py big_project code/new_project
# This assumes you have a template called big_project in your ~/.skeletons dir

$ SKELETON_TEMPLATE_DIR="~/code/templates/" python project_skeleton.py big_project code/new_project
# Using an environment variable.  For long-term use, put this variable
# declaration in your .bashrc :)

$ python project_skeleton.py -d ~/my/favorite/secret/place/ big_project code/new_project 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

我打算在目录、文件名和文件中插入变量(类似于 Cookiecutter 的做法)，从远程 URL 克隆，等等，但这对于一篇文章来说似乎有点多。我想我会把它留在这里，也许会放到以后的文章中。如果有人想尝试一下，请给我发一个链接，链接到您的解决方案，以获得额外的互联网积分！