# 在 Powershell 中搜索/替换文件名

> 原文：<https://dev.to/nektro/searchreplace-file-names-in-powershell-10d9>

发布一个我最近发现的命令，允许你在 Windows 上的 Powershell 中查找/替换文件名。`<search>`和`<replace>`都接受正则表达式，或者您也可以将`-LiteralPath`选项添加到`rename-item`

```
get-childitem *.txt | foreach { rename-item $_ $_.Name.Replace("<search>", "<replace>") } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在一个 Denvercoder69 真的会回来回复那篇博文的世界里。