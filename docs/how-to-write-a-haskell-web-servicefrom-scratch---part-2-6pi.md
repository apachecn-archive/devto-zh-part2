# 如何编写 Haskell web 服务(从头开始)——第 2 部分

> 原文：<https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-2-6pi>

这是创建基于 Haskell 的 web 服务实践指南的第二部分。不需要以前了解 haskell。然而，对 RESTful web 服务的基本理解是假定的。本系列其他帖子:[第一部](https://dev.to/parambirs/how-to-write-a-dockerized-haskell-web-servicefrom-scratch---part-1-3m7c)，[第三部](https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-3-5en6?)。

# 2。使用 cabal 构建和打包一个简单的应用程序

## 引入阴谋集团

Cabal(构建应用程序和库的通用架构)是一个用于构建和打包 Haskell 库和程序的系统。可以把它想象成 Haskell 的 Python 的`pip`、Node 的`npm`和 Scala 的`sbt`。

在我们开始之前，请确保您已经安装了`cabal`，并且位于系统路径:

```
% cabal — version
cabal-install version 1.22.6.0
using version 1.22.4.0 of the Cabal library 
```

## 创建一个 cabal app

Cabal 使构建、打包和测试你的应用程序变得容易。添加对 cabal 将为您获取的其他库的依赖也很简单。

不同的 Haskell 应用程序可能依赖于同一个库的不同版本。为了防止您的全局 Haskell 环境中的冲突，为您的应用程序创建一个沙箱是一个好主意。Cabal 将在沙箱中安装相关的库。

```
% mkdir cabal-example
% cd cabal-example
% cabal sandbox init
Writing a default package environment file to
/Users/psingh/tmp/haskell/eac-articles/cabal
example/cabal.sandbox.config
Creating a new sandbox at
/Users/psingh/tmp/haskell/eac-articles/cabal-example/.cabal-sandbox 
```

现在，我们可以初始化我们的应用程序。创建 cabal 应用程序最简单的方法是使用`cabal init`命令。按照提示操作，cabal 会为你生成配置文件。在我的系统上有一个交互的例子:

```
% cabal init
Package name? [default: cabal-example]
Package version? [default: 0.1.0.0]
Please choose a license:
* 1) (none)
2) GPL-2
3) GPL-3
4) LGPL-2.1
5) LGPL-3
6) AGPL-3
7) BSD2
8) BSD3
9) MIT
10) ISC
11) MPL-2.0
12) Apache-2.0
13) PublicDomain
14) AllRightsReserved
15) Other (specify)
Your choice? [default: (none)] 9
Author name? [default: Parambir Singh]
Maintainer email? [default: user@domain.com]
Project homepage URL?
Project synopsis?
Project category:
* 1) (none)
2) Codec
3) Concurrency
4) Control
5) Data
6) Database
7) Development
8) Distribution
9) Game
10) Graphics
11) Language
12) Math
13) Network
14) Sound
15) System
16) Testing
17) Text
18) Web
19) Other (specify)
Your choice? [default: (none)]
What does the package build:
1) Library
2) Executable
Your choice? 2
What is the main module of the executable:
* 1) Main.hs (does not yet exist)
2) Main.lhs (does not yet exist)
3) Other (specify)
Your choice? [default: Main.hs (does not yet exist)] 1
What base language is the package written in:
* 1) Haskell2010
2) Haskell98
3) Other (specify)
Your choice? [default: Haskell2010] 1
Include documentation on what each field means (y/n)? [default: n] n
Source directory:
* 1) (none)
2) src
3) Other (specify)
Your choice? [default: (none)] 1
Guessing dependencies…
Generating LICENSE…
Generating Setup.hs…
Generating cabal-example.cabal…
Warning: no synopsis given. You should edit the .cabal file and add 
one.
You may want to edit the .cabal file and add a Description field. 
```

现在，您的源目录中会有一个`cabal-example.cabal`文件。记得我们在`cabal init`过程中选择了`Main.hs`作为我们的主模块。现在添加一个`Main.hs`文件到你的源文件夹的根目录，内容如下:

```
c2f c = (c * 9/5) + 32
main = do
  print (c2f 37) 
```

## 运行你的 cabal 应用程序(cabal run)

要运行您的应用程序，请使用`cabal run`命令:

```
% cabal run
Package has never been configured. Configuring with default flags. If this fails, please run configure manually.
Resolving dependencies…
Configuring cabal-example-0.1.0.0…
Preprocessing executable ‘cabal-example’ for cabal-example-0.1.0.0…
[1 of 1] Compiling Main ( Main.hs, dist/build/cabal-example/cabal-example-tmp/Main.o )
Linking dist/build/cabal-example/cabal-example …
Running cabal-example…
98.6 
```

## 其他有用的阴谋集团命令

### `cabal clean`

删除生成的工件(可执行文件等。)

```
% cabal clean
cleaning…
cabal build
Compiles and links your source code to generate the executable.
% cabal build
Package has never been configured. Configuring with default flags. If this fails, please run configure manually.
Resolving dependencies…
Configuring cabal-example-0.1.0.0…
Building cabal-example-0.1.0.0…
Preprocessing executable ‘cabal-example’ for cabal-example-0.1.0.0…
[1 of 1] Compiling Main ( Main.hs, dist/build/cabal-example/cabal-example-tmp/Main.o )
Linking dist/build/cabal-example/cabal-example …
Run the generated executable:
% ./dist/build/cabal-example/cabal-example
98.6 
```

### `cabal install`

将生成的神器安装到 cabal 沙箱中。这有点类似于 Java 世界里的`mvn install`。

```
% cabal install
Resolving dependencies…
Notice: installing into a sandbox located at
/Users/psingh/tmp/haskell/eac-articles/cabal-example/.cabal-sandbox
Configuring cabal-example-0.1.0.0…
Building cabal-example-0.1.0.0…
Installed cabal-example-0.1.0.0 
```

### `cabal repl`

为您的项目启动一个`ghci`会话(即加载您的模块)。您可以在这里轻松测试您的功能。

```
% cabal repl
Preprocessing executable ‘cabal-example’ for cabal-example-0.1.0.0…
GHCi, version 7.10.2: http://www.haskell.org/ghc/ :? for help
[1 of 1] Compiling Main ( Main.hs, interpreted )
Ok, modules loaded: Main.
*Main> c2f 37 — call the c2f function defined in our Main.hs file
98.6
*Main> main — yes, this is the main function we defined in Main.hs
98.6
*Main> 
```

### 阴谋集团更新

下载 cabal 的最新包列表。在运行`cabal init`或`cabal install`之前运行`cabal update`是很有用的，这样可以确保 cabal 了解最新的包

```
% cabal update
Downloading the latest package list from hackage.haskell.org 
```

## 来源

本节的完整源代码可在 [github](https://github.com/parambirs/hello-cabal) 上获得

# 接下来…

在第 3 部分中，我们将使用 Scotty web framework for Haskell 编写一个简单的 web 应用程序。