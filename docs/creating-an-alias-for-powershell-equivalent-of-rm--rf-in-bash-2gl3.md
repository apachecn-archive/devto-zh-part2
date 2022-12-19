# 在 Bash 中创建一个相当于“rm -rf”的简短 PowerShell 命令

> 原文：<https://dev.to/tomoyukiaota/creating-an-alias-for-powershell-equivalent-of-rm--rf-in-bash-2gl3>

###### (此处有日文翻译[。)](https://qiita.com/TomoyukiAota/items/f94c9b0dfdeeb8a092f7)

在 Bash 中，`rm -rf`删除一个文件或一个目录。在 PowerShell 中，我也想用一个简短的命令做同样的事情。因此，我创建了一个 PowerShell 函数来完成这项工作。

# 简介

假设我要删除`target`。在 PowerShell 中，以下命令可以完成这项工作。

```
Remove-Item -Recurse -Force target 
```

Enter fullscreen mode Exit fullscreen mode

即使使用制表符结束，这也是一个很长的命令。以下命令是使用官方提供的别名的缩短版本。

```
rm -r -fo target 
```

Enter fullscreen mode Exit fullscreen mode

与 Bash 中的`rm -rf`相比，这仍然很长。
因此，我决定为这项工作创建一个 PowerShell 函数`rmrf`。

# 如何创建命令

需要创建 PowerShell 配置文件。
`echo $profile`显示配置文件的文件路径。如果该文件不存在，请先创建它。

在 PowerShell 配置文件中，添加以下函数。