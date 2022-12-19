# 使用 Python 与 AWS Lambda 的“make”持续集成

> 原文：<https://dev.to/leahein/continuous-integration-with-make-for-aws-lambda-using-python-3dll>

[![pipeline](../Images/5b8d21ba897fbfb5f277aa04fcab99f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xgmecsuF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.skilljar.com/hs-fs/hubfs/salesforce_integration.png%3Ft%3D1533167571059%26width%3D440%26height%3D212%26name%3Dsalesforce_integration.png)

## 目标

将 python 代码部署到 AWS Lambda 时，需要将所有代码压缩到一个 zip 文件中，并将所有包依赖项(通常 pip 安装到 virtualenv 中的 python 库)添加到目录的根级别。

我想要一种简单的方法，不需要登录 AWS 控制台
,也不需要在每次修改源代码时使用 GUI 压缩我所有的内容。我还想要一种方法来测试代码，如果测试没有通过，就停止部署流程。如果你有一个持续集成的服务器，比如 Jenkins，这就是你所期望的。目标是能够修改代码或 pip 安装包，并且通过一个命令就能够测试我的代码，压缩我的代码，并将其部署到 lambda。

## 制作简介

如果你不熟悉`make`，这里有一个描述:

> 一个 GNU 构建自动化工具，通过读取指定如何构建程序的 Makefiles 文件，从源代码自动构建程序。

这通常在 c 程序中用于编译所有源代码并将其捆绑到一个可执行文件中，并且预安装在大多数 UNIX 系统中，如 MacOS 和 Linux。`make`的另一个很大的好处是，它足够聪明，只重新构建*已更改的*文件，而不会每次都重新生成每个文件的*。规则将按您指定的顺序执行，如果任何命令失败，规则将停止执行。*

最终，对于我们的需求来说，这似乎是一个很好的用例，因为我们本质上
想要将我们的源文件转换成一个单一的目标结果(zip 文件)！如果测试失败，我们还希望部署过程暂停并停止执行命令。

首先，简单介绍一些`make`命令和语法:

*   要使用 make 做一些事情，你可以在终端的一个名为“Makefile”的目录中输入 **`make`**
*   Makefile 文件是规则的集合。每条规则都是做一件特定事情的**秘方**。
*   配方语法:

```
 <target>: <prerequisites...>
    <commands> 
```

**目标**将是您从终端调用来执行命令的对象，比如`make <target>`，类似于一个函数名。
**先决条件**是这个目标依赖的任何东西，可以是一个文件，也可以是另一个目标。
**命令**是你可以指定一系列你想要执行的命令的地方，就像一个函数体。

目标是必需的，先决条件和命令是可选的，但您必须拥有其中一个。

*   神奇变量:这是特殊的 make 语法，允许我们轻松地引用目标/先决条件。

```
# Example: libs/: requirements.txt requirements-test.txt
  $@   # refers to target: "libs/" 
  $<   # refers to the first prerequisite: "requirements.txt" 
  $^   # refers to all prerequisites: "requirements.txt requirements-test.txt" 
```

语法是独特的，可能需要时间来适应！但在我看来，这是使`make` (uh)在编译源代码时成为强大工具的一部分，而不是使用 bash 脚本。[这里有一张小抄供参考。](https://devhints.io/makefile)

## 如何

我们将希望做到以下几点:

1.  测试我们的代码
2.  压缩我们的代码
3.  部署我们的代码
4.  清理我们创建的任何文件/目录

本教程假设您已经安装了以下软件:

*   点
*   aws cli
*   制造
*   活力

### 测试我们的代码

```
test: mypy pylint nose tox ## Run all tests
    pip install $^ # install your test dependencies
    ## run your tests here.  some examples:
    python -m unittest discover tests/
    nose
    pylint main.py
    mypy main.py
    tox
    # etc. 
```

### 压缩我们的代码

我们从安装库依赖项开始。

```
# Our target is the libs dir, which has a requirements file as a prerequisite 
libs: requirements.txt ## Install all libraries
    @[ -d $@ ] || mkdir $@ # Create the libs dir if it doesn't exist
        pip install -r $< -t $@ # We use -t to specify the destination of the
        # packages, so that it doesn't install in your virtual env by default 
```

压缩源代码和库。

```
# Our target is the zip file, which has libs as a prerequisite 
output.zip: libs ## Output all code to zip file
        zip -r $@ *.py # zip all python source code into output.zip
        cd $< &&  zip -rm ../$@ * # zip libraries installed in the libs dir into output.zip

        # We `cd` into the directory since zip will always keep the relative 
     # paths, and lambda requires the library dependencies at the root. 
        # Each line of a make command is run as a separate invocation of 
     # the shell, which is why we need to combine the cd and zip command here. 
        # We use the -rm flag so it removes the libraries after zipping, 
     # since we don't need these. 
```

### 部署我们的代码

```
# Our target is deploy, which has the zip file as a prerequisite 
# Note: since deploy doesn't refer to an actual file or directory, it's good 
# practice to declare this as .PHONY, so make knows not to look for that file. 
.PHONY: deploy
deploy: output.zip ## Deploy all code to aws
    -aws lambda update-function-code \
        --function-name my-lambda-function \
        --zip-file fileb://$<
 # This assumes you have the aws cli, but if not, run `pip install awscli` 
 # The "-" says to ignore the exit status, and continue executing even if it
 # fails.  This is so we can clean up the files when we're done, even if
 # deployment failed. 
```

### 最后，清理文件

```
.PHONY: clean
clean: ## Remove zipped files and directories generated during build
    rm output.zip
    rmdir libs 
```

然后，我们希望运行所有这些目标，并用一条命令执行它:

```
# `make all` will execute all prerequisite targets! 
.PHONY: all
all: test deploy clean 
```

## 摘要

Make 是一个功能强大的工具，可以用来运行一系列命令，并且在部署到 lambda 时可以成为一个有用的构建自动化工具。

我们用它来压缩我们的 python 源代码，以及我们的依赖项，并将代码推送到 aws。

现在，只需在您的终端中输入一条命令`make all`，我们就为 lambda 建立了持续集成管道！

### 资源

Make 可以通过它的特殊语法做更多的事情，所以如果你想了解更多，[看看这个教程](https://gist.github.com/isaacs/62a2d1825d04437c6f08)。