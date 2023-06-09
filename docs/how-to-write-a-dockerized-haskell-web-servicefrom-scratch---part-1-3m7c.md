# 如何编写 Haskell web 服务(从头开始)——第 1 部分

> 原文：<https://dev.to/parambirs/how-to-write-a-dockerized-haskell-web-servicefrom-scratch---part-1-3m7c>

这是创建基于 Haskell 的 web 服务实践指南的第一部分。不需要以前了解 haskell。然而，对 RESTful web 服务的基本理解是假定的。本系列其他帖子:[第二部](https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-2-6pi)，[第三部](https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-3-5en6)。

在过去的几周里，我花了一些时间创建了一个简单的 Haskell web 应用程序。正如每一种新的语言或平台一样，我必须设置并理解很多东西，才能让我的应用程序运行起来。我在这里分享我的经验，希望它能帮助其他可能倾向于进入 Haskell 世界的人。

我们将经历以下 3 个步骤:

*   设置开发环境
*   使用`cabal`构建并打包一个简单的应用程序
*   使用 Scotty 运行一个基本的 Haskell web 应用程序

* * *

# 1。设置开发环境

*如果你的系统已经安装了`ghc`和`cabal`，你可以跳过这一步。*

## 安装哈斯克尔平台(GHC)

GHC 是 Glasgow Haskell 编译器包，它提供了编译器(`ghc`)和 REPL ( `ghci`)。Cabal 是 Haskell 的包管理器，工作方式类似于`npm` (NodeJS)、`sbt` (Scala)和`maven` (Java)。

```
% brew update
% brew install ghc cabal-install 
```

## 哈斯克尔·REPL(`ghci`

接下来，我们将启动名为`ghci`的 Haskell REPL，并熟悉一些 Haskell 语法。

```
% ghci 
```

让我们定义两个在摄氏和华氏之间转换温度值的简单函数:

```
Prelude> let f2c f = (f — 32) * 5 / 9
Prelude> let c2f c = (c * 9/5) + 32 
```

现在，我们可以调用这些函数来获得转换后的温度:

```
Prelude> f2c 800
426.6666666666667
Prelude> c2f 100
212.0
Prelude> c2f 37
98.6
Prelude> <Ctrl+D> — To exit 
```

## 运行 Haskell 脚本(`runhaskell`

我们将把这些函数定义移到一个 haskell 源文件中，并定义一个 main 方法来打印转换后的温度值。

将以下代码复制到名为`c2f.hs` :
的文件中

```
c2f c = (c * 9/5) + 32
main = do
  print (c2f 37) 
```

*注意:我们使用`let`来定义`ghci`内部的函数。写 Haskell 源文件时，不需要`let`。*

使用`runhaskell` :
从命令行运行脚本

```
% runhaskell c2f.hs
98.6 
```

## 构建一个可执行文件

当你编写一个 Haskell 应用程序时，使开发周期变得更容易。但是您也可以使用`ghc`编译器创建可执行文件。让我们将脚本转换成二进制文件，然后运行它:

```
% ghc c2f.hs
[1 of 1] Compiling Main ( c2f.hs, c2f.o )
Linking c2f …

% ls
c2f c2f.hi c2f.hs c2f.o

% ./c2f
98.6 
```

## 将你的源文件加载到`ghci`

您可以使用`:load`或`:l`命令将外部源文件加载到`ghci`中。让我们启动一个`ghci`会话并加载我们的`Main.hs`文件，这样我们就可以在 REPL:
中使用`c2f`函数

```
% ghci
GHCi, version 7.10.2: http://www.haskell.org/ghc/ :? for help
Prelude> :load Main.hs
[1 of 1] Compiling Main ( Main.hs, interpreted )
Ok, modules loaded: Main.
*Main> c2f 37
98.6 
```

现在我们的开发环境已经设置好了，在[第 2 部分](https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-2-6pi)中，我们将继续使用 Haskell 的包管理器`cabal`构建一个简单的应用程序。