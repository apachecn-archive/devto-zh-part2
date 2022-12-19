# 适用于 Windows 的专业小技巧

> 原文：<https://dev.to/saltyshiomix/tiny-pro-tips-for-windows-3n88>

嗨，今天我将向你展示我最喜欢的 Windows 小技巧。

通常我们使用“右键单击”并看到一些上下文菜单，如下所示:

[![context menu](../Images/567e566111bc088b19e439c8f27b9c49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--34Z091bq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ka5xe0f41otjumsot7cr.png)

该右键上下文菜单可由`regedit.exe`编辑。

现在我将推荐一些我经常使用的技巧。

## CMD.EXE 在此

您要打开`cmd.exe`并在此更改目录吗？我愿意。！

[![cmd-here1](../Images/4be4d5161bf40e17dce2c1a4466fe9c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1yD1acFO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zsgvncnwyspe3gcitnhd.png)

[![cmd-here2](../Images/717af6a3acbbffe96861693e52e936e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ZB9Q63R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3lt0g0up5yvu1nx3zzfc.png)

下面是两个源代码:

(目录和目录背景)

**目录**

```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\directory\shell\cmd-here]
@="CMD.EXE HERE"
"Icon"="cmd.exe"

[HKEY_CLASSES_ROOT\directory\shell\cmd-here\command]
@="cmd.exe /K \"cd /d %1\"" 
```

Enter fullscreen mode Exit fullscreen mode

**目录背景**

```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\directory\background\shell\cmd-here]
@="CMD.EXE HERE"
"Icon"="cmd.exe"

[HKEY_CLASSES_ROOT\directory\background\shell\cmd-here\command]
@="cmd.exe /K \"cd /d %v\"" 
```

Enter fullscreen mode Exit fullscreen mode

只需打开`cmd.exe`和`cd` `%1`(或`%v`论证)，够简单了吧？

要安装它，您可以从这个存储库中下载任何`*.reg`文件:

[https://github.com/saltyshiomix/tiny-pro-tips-for-windows](https://github.com/saltyshiomix/tiny-pro-tips-for-windows)

## 复制路径

接下来，“复制路径”上下文菜单。

[![copy-path](../Images/31710607a9d56381f13cf2fe762ab729.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zt6biowE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1csbs91d4k6cuv2a4me.png)

下面是三个源代码:

(文件、目录和目录后台)

**文件**

```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\*\shell\copy-path]
@="COPY PATH"

[HKEY_CLASSES_ROOT\*\shell\copy-path\command]
@="cmd.exe /c \"(echo.|set /p=\"%1\")|clip\"" 
```

Enter fullscreen mode Exit fullscreen mode

**目录**

```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\directory\shell\copy-path]
@="COPY PATH"

[HKEY_CLASSES_ROOT\directory\shell\copy-path\command]
@="cmd.exe /c \"(echo.|set /p=\"%1\")|clip\"" 
```

Enter fullscreen mode Exit fullscreen mode

**目录背景**

```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\directory\background\shell\copy-path]
@="COPY PATH"

[HKEY_CLASSES_ROOT\directory\background\shell\copy-path\command]
@="cmd.exe /c \"(echo.|set /p=\"%v\")|clip\"" 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

“CMD。EXE 在这里”和“复制路径”是非常有用的，当你在 Windows 机器上开发。

如果你使用这些有用的提示，我会非常高兴:)

感谢您阅读我的文章！

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [ saltyshiomix ](https://github.com/saltyshiomix) / [窗口小提示](https://github.com/saltyshiomix/tiny-pro-tips-for-windows)

### 针对 Windows 的微小但专业的提示

<article class="markdown-body entry-content container-lg" itemprop="text">

# 适用于 Windows 的专业小技巧

## 如何使用

双击`*.reg`或用注册表编辑器(`regedit.exe`)执行。

## 登记处

### 我是 CMD.EXE

打开`cmd.exe`，将目录切换到目标路径。

*   [注册表\目录\cmd-here.reg](https://raw.githubusercontent.com/saltyshiomix/tiny-pro-tips-for-windows/master/./registry/directory/cmd-here.reg)
*   [注册表\目录\后台\cmd-here.reg](https://raw.githubusercontent.com/saltyshiomix/tiny-pro-tips-for-windows/master/./registry/directory/background/cmd-here.reg)

### 复制路径

以 Windows 路径格式复制目标的完整路径。

*   [registry\copy-path.reg](https://raw.githubusercontent.com/saltyshiomix/tiny-pro-tips-for-windows/master/./registry/copy-path.reg)
*   [registry \ directory \ copy-path . reg](https://raw.githubusercontent.com/saltyshiomix/tiny-pro-tips-for-windows/master/./registry/directory/copy-path.reg)
*   [注册表\目录\后台\复制路径. reg](https://raw.githubusercontent.com/saltyshiomix/tiny-pro-tips-for-windows/master/./registry/directory/background/copy-path.reg)

</article>

[View on GitHub](https://github.com/saltyshiomix/tiny-pro-tips-for-windows)