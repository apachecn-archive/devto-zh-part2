# VS 代码，在 Windows 命令中新建项目

> 原文：<https://dev.to/httpjunkie/vs-code-new-project-in-windows-command-58nc>

现在，随着我们在候机厅度过的时光，知道如何在不离开候机厅的情况下做最基本的事情是很好的。那么，如果我们必须创建一个新目录，初始化一个项目，创建一个索引文件并在代码编辑器中打开该文件，我们需要做什么呢？它实际上很简单，我只记得其中的一部分有困难。步骤如下:

例如，首先我们可能需要到达我们想要创建项目目录的任何目录。如果它在我的项目文件夹中，看起来可能是这样的:

```
cd C:\projects 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要创建我们的项目文件夹:

```
mkdir super-cool-project 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们需要更改目录才能进入:

```
cd super-cool-project 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要初始化一个 git 存储库来跟踪我们对项目的变更:

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

同时，我们需要引进与 NPM 合作的能力。这将提示我们为项目创建默认配置。

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了开始，我们将创建一个`index.html`文件，但实际上这可能是一个`index.js`或任何您想要开始工作的初始文件。

```
type nul > index.html 
```

Enter fullscreen mode Exit fullscreen mode

在 Visual Studio 中打开代码:

```
code . 
```

Enter fullscreen mode Exit fullscreen mode