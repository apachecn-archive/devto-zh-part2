# PowerShell 列表文件在过去 7 天内发生了更改

> 原文：<https://dev.to/cfleischhacker/powershell-list-files-changed-in-the-last-7-days-34ng>

$startingpath="C:\ "

$startingdate=$(获取日期)。添加天数(-7)

get-child item $ starting path-Recurse | where-object {！($_.psiscontainer)} | where { $_。LastWriteTime-gt $ starting date } | foreach { Write-Host " $($ _。LastWriteTime) :: $($_。全名)" }

# 或

foreach {Write-Output "$($_。LastWriteTime) :: $($_。全名)" > > changedfiles.csv }