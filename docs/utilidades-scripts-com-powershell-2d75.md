# 实用程序:使用 Powershell 编写脚本

> 原文：<https://dev.to/andersonbraz/utilidades-scripts-com-powershell-2d75>

在生产环境的应用程序支持或操作中发挥作用。我们一直面临着要求在短时间内处理事故或工作活动的腐蚀情况。

在 Windows 环境中，我通常会使用 Powershell 执行一些例行程序。在这里，我想分享一些我最常用的程序。

## 执行政策审查

```
Get-ExecutionPolicy -List 
```

Enter fullscreen mode Exit fullscreen mode

## 启用不受限制的执行政策

```
Set-ExecutionPolicy Unrestricted 
```

Enter fullscreen mode Exit fullscreen mode

## 文件删除

用于从特定目录中删除临时文件或具有特定扩展名的文件的脚本。

```
 $FilePath = "C:\Windows\Temp\*"
$KeyWord = "*.tmp"
$Files = Get-ChildItem -Path $FilePath -Include $KeyWord -Recurse

ForEach($File In $Files)
{
    $Item = $File.FullName
    Write-Output $Item
    Remove-Item $Item -Force
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 目录删除

用于删除在特定位置包含特定单词的目录的脚本。

```
 $FolderPath = "C:\Temp\"
$KeyWord = "*Quarantine*"
$Files = Get-ChildItem -Path $FolderPath -dir -r | Where-Object {$_.name -like $KeyWord}

ForEach($Folder In $Folders)
{
    $Item = $Folder.FullName
    Write-Output $Item
    Remove-Item $Item -Force -Recurse
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 文件压缩(7zip)

```
 $KeyWord = "*.log"
$LogPath = "E:\Logs\W3SVC*\"
$Limit = (Get-Date).AddDays(-150)

$Files = Get-ChildItem -Path $LogPath -Include $KeyWord -Recurse | Where CreationTime -lt $Limit
ForEach ($File In $Files) 
{
    $Item = $File.FullName
    Write-Output "File Found: " $Item 
    & "E:\Program Files\7-Zip\7z.exe" a -tzip ($Item + ".zip") $Item

} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 验证 Ip 访问:端口

```
New-Object System.Net.Sockets.TcpClient("192.168.0.15", 80) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 检查目录大小

```
$Path = "C:\inetpub\wwwroot\andersonbraz\"
$colItems = Get-ChildItem $Path | Where-Object {$_.PSIsContainer -eq $true} | Sort-Object

ForEach ($i In $colItems)
{
    $subFolderItems = Get-ChildItem $i.FullName -Recurse -Force | Where-Object {$_.PSIsContainer -eq $false} | Measure-Object -Property Length -Sum | Select-Object Sum
    $i.FullName + " -- " + "{0:N2}" -f ($subFolderItems.sum / 1MB) + " MB"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 克雷蒂托

由 [Pakata Goh](https://unsplash.com/@pakata?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片