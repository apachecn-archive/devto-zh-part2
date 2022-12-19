# Python 中的自动报告——第 1 部分:从规划到 Hello World

> 原文：<https://dev.to/goyder/automatic-reporting-in-python---part-1-from-planning-to-hello-world-32n1>

我想记录并逐步完成 Python 中一个简单概念的执行:创建一个自动 HTML 报告工具。

结果将是一个单独的 HTML 文件。HTML 文件是一个很好的报告工具:即使去掉后端服务器，你也可以在一个页面中打包大量的信息，并具有方便的交互性。

[![](../Images/91d6b7e36d6f0be493d042bca7c9809d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sHKWsmoT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3ikmh861byddzvvczmd.png)

现在，公平地说:这不是一个非常令人兴奋或性感的项目。同样，自动生成报告是一个绝妙的锦囊妙计，尤其是在商业环境中。[把无聊的东西自动化，就像他们说的](https://automatetheboringstuff.com/)。

就个人而言，我主要打算用它来自动报告机器学习空间中的模型性能，但是你当然可以在任何环境中使用它！

**自然，像这样的工具已经存在**。但是我想做我自己的，因为它给了我经验和灵活性。

### 这个指南是怎么呈现的？

我不仅仅是提供一堆有用的代码，我更愿意一步一步地逐步完成开发。你会看到我所看到的——编码我所编码的！

就我个人而言，我一直认为这类教程更有效，因为:

*   它让我们了解其他人是如何解决问题的
*   它实际上反映了我是如何编码的——一个特性接一个特性，一次次提交。

### 这是给谁的？

假设用户对 Python 的理解“深及膝盖”。我不会重复标准语法之类的内容，但我一定会停下来解释任何特别复杂的东西。只要有可能，我会引用更聪明、更清晰的解释。

* * *

## 零步停一分钟

我实际上是化学工程师出身。虽然我确实从这一背景中获得并应用了许多有用和/或深奥的知识，但迄今为止，我学到的最有用的建议可能也是最简单的:

> 当处理一个新问题时，画一幅画。

我说的“画一幅画”，意思是把问题具体化。画出草图，把想法和主意写在纸上。

在着手这个(相对小规模的)项目之前，我坐下来花了 10-15 分钟勾勒出我认为我应该如何着手，哪些部分会移动，哪些部分会保持静止，以及一个(非常)粗略的项目结构。

我自己几乎无法理解的草图如下。

[![](../Images/9b6d7ad981bc440948a681e0cb9826f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZVmS80N9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5mpuuejc6kmc2wbj0z0q.png)

### 早期规划的一些重要经验

基于我早期的“规划”，有些事情变得清晰了:

*   HTML 模板系统很有意义。这些系统被设计成将动态内容输入到静态模板中:这听起来正是我们所追求的。在 Python 中， [Jinja](http://jinja.pocoo.org/) 是常见的选择，可能玩过 [Flask](http://flask.pocoo.org/) 的人都比较熟悉。
*   在机器学习领域，我不断发现我可能想要寻找的新事物，或者比较模型的新方法，或者分析数据集的新方法——所以报告的*内容和我可以打包到任何报告*中的*功能都应该是灵活的。*
*   我处理的很多数据都在。csv 格式，所以使用工具来处理它并从中生成可查询的 HTML 块会很方便。

* * *

## 第一步——打基础

对于几乎任何比在命令行上花几分钟时间修补更大的项目，我都喜欢设置 Github repo 和一致的环境。

我认为，这将是一个非常小规模的项目，所以我并不十分关心制定一个完整的项目结构，但是这是值得的。

这个:

*   鼓励保持项目清洁的良好习惯
*   帮助我毫不费力地从我的 Mac 笔记本电脑跳到我的 Linux 台式机(或者任何其他环境)

让我们从几个快速目标开始:

### 建立虚拟环境

我们希望为这个项目创建一个独立的 Python 实例——或者“虚拟环境”——而不是使用这台机器上现有的任何通用安装。这隔离了我们的依赖关系，并使得项目的移动更加容易。我在 Pycharm 工作，它让你可以选择从头开始创建新的虚拟环境。

为了使其可转移，我将在我的项目目录的根目录下保存一个`requirements.txt`文件，它详细描述了环境中需要哪些包。

关于这个话题有很多[很多](https://realpython.com/python-virtual-environments-a-primer/)[很多](http://docs.python-guide.org/en/latest/dev/virtualenvs/)的指南，更具体的细节可以参考一下。

### 设置 Git/Github 回购

为了实现版本跟踪并允许从 Github 来回传输，我们将在本地初始化一个 Git repo(在项目文件夹中)并在 Github 中创建一个新的 repo。然后可以将本地 Git repo 推送到 Github。

在 Github.com 上可以找到一本简明指南[。](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)

作为在 Git/Github repo 中工作的一部分，我们将在根项目目录中添加以下内容:

*   简要描述项目的`readme.md`文件，以及
*   一个`.gitignore`文件，指示哪些文件和文件夹不应该被 Git 跟踪并推送到 Github。这可能包括自动生成的文件(例如，从 IDE 或虚拟环境中生成的文件)、敏感文件和配置数据，或者您在本地创建的任何其他不需要与外界共享的文件。对我来说，这看起来像:

```
# .gitignore

# Don't add the virtual environment, IDE, and Jupyter notebook info
venv
.idea
.ipynb_checkpoints
Write-up.ipynb 
```

Enter fullscreen mode Exit fullscreen mode

### Github 状态

此时，你的项目框架应该看起来有点像[这个](https://github.com/goyder/autoreporting/tree/c1f1ba68cae351d0de851f0f4831b0f2caa0a407)。

* * *

## 第二步——“你好，世界”模板化

此时，我们有了一个非常简单的项目结构，我们可以开始填充代码和模板。为了进行一点冒烟测试，并给我一个构建的地方，我想创建一个尽可能简单的报告。

第一个实现应该不输入任何信息，只输出一个简单的“Hello，world！”HTML 页面。

### 关于模板化的简要介绍

这个项目建立在模板和[模板处理器](https://en.wikipedia.org/wiki/Template_processor)的概念之上。这是网络出版中一个巨大但非常关键的领域。

简单来说:我们将一个 HTML 页面的*结构*和要发布的*内容*分开。当需要生成一个新的网页(或者在这种情况下，一个报告！)我们可以将内容输入到一个标准化的结构中，然后推出一个新的网页。

[![](../Images/ceeb548ab770e5a66bc4e5a2cce224a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QmkQPbQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ze46l15yyxhmsrp722xf.jpg)

模板化现在确实无处不在，但是如果你想要一个没有一致的手动或自动模板化的例子，你可以在 [Geocities 档案](http://www.oocities.org/)中找到许多独特迷人的例子。

### 构建‘你好，世界’功能

基于*结构*和*内容*之间的区别，我们可以为两者创建非常简单的例子来测试系统。

#### 结构

在这种情况下，我们的结构是一个几乎为空的 HTML 页面，带有输入数据的位置。我们称之为“模板”,我们将把它和其他的保存在我们的根项目目录中的一个`template`文件夹下。我们将在这个文件夹下创建一个新的 HTML 文件- `{project_folder}/template/report.html`。这个文件的内容如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    {{ content }}
</head>
<body>
{{ content }}
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

注意花括号中的元素(在这个例子中，*用双*花括号- `{{`和`}}`)。这是动态内容将被输入的地方。

像 Jinja 这样的模板处理器非常强大，除了简单的插入之外，还允许在模板中呈现许多特性。看一看 [Jinja 主页](http://jinja.pocoo.org/docs/2.10/)上的第一个例子，看看你是否能理解它。

#### 内容

目前，我们的内容可以简单到`hello, world!`。

我们将在项目目录的根目录下创建一个名为`{project_folder}/autoreporting.py`的新文件，并开始硬编码我们的“内容”。

```
content = "Hello, world!" 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能

我们有了结构和内容——现在我们只需要用 Jinja 把它们结合起来。

首先，创建一个`outputs`目录，并将其添加到您的`.gitignore`文件中。我们将转储我们在此文件夹中创建的内容，但没有理由在您的回购中共享任何最终内容。

Jinja 需要一个`Environment`对象来存储和定义像配置和加载器这样的东西——也就是说，我们将从哪里提取模板。在这种情况下，我们运行的是本地文件系统，所以指定在`templates`目录中搜索的`FileSystemLoader`就可以了。

使用我们的`env`实例，我们可以呈现我们的基本模板并将其写入文件。

我们的`autoreporting.py`脚本现在看起来像这样:

```
from jinja2 import FileSystemLoader, Environment

# Content to be published content = "Hello, world!"

# Configure Jinja and ready the template env = Environment(
    loader=FileSystemLoader(searchpath="templates")
)
template = env.get_template("report.html")

def main():
    """
    Entry point for the script.
    Render a template and write it to file.
    :return:
    """
    with open("outputs/report.html", "w") as f:
        f.write(template.render(content=content))

if __name__ == "__main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

注意参数为`template.render`，`content=content`。这链接了模板和我们的代码。第一个`content`是指模板中的`{{ content }}`，第二个是脚本中定义的字符串。我们定义了这种关系，字符串被输入到模板中进行替换。

### 结果至此

如果您运行这个脚本并且一切顺利，一个新的`report.html`文件将被写入`outputs/`下。打开这个文件，看看你创造了什么！

[![](../Images/d03a46d467c82af5ea41f6a6e2257e22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D5cvubX_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/twx3hgldja5dxwjket0e.png) 
这当然符合“非常简单，但是实用”的标准

如果您检查页面的 HTML，您会注意到模板中的`{{ content }}`标记被整齐地替换为`Hello, world!`字符串，这就是问题的症结所在。

## Github 状态

你已经开发了一个新功能，所以提交并推送到 Github 是个好主意。

你的回购可能看起来有点像这个。

## 下一步

记者边走边工作，但在这一点上我们肯定没有任何有意义的投入或灵活性。我们的下一步将是探索如何读入数据和报告数据，并开始使用 JavaScript 和 CSS。