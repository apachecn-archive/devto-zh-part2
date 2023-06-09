# 清理源上不存在的 git 分支

> 原文：<https://dev.to/mehmetseckin/clean-up-git-branches-that-do-not-exist-on-origin-1afj>

# 清理原点上不存在的 git 分支

在我们的团队在 git 存储库上工作了一段时间后，我们积累了许多不再被使用的无用分支。我们定期从原点移除这些分支，但本地分支像僵尸一样留在每个人的工作站中。

是时候做点什么了。

那时，我们决定将`git fetch --prune`和 shell 脚本的力量结合起来，打造一个 PowerShell 函数，杀死本地存储库中的僵尸分支。

该脚本简单地使用`git fetch --all --prune`来更新所有的远程引用(`--all`)并丢弃已删除的引用(`--prune`)。

之后它通过抓取`git branch -vv`的输出过滤掉原点上不存在的局部分支，删除僵尸局部分支。

事情是这样的:

```
<#
.SYNOPSIS
Remove local git branches that are deleted from the origin.

.DESCRIPTION
Removes local git branches that are deleted from the origin using `git fetch --prune` and `git branch -[dD]`.

.PARAMETER Force
Switch to to force removal using `git branch -D` instead of `git branch -d`.

.EXAMPLE
Remove-DeletedGitBranches
Removes merged non-existing branches.

.EXAMPLE
Remove-DeletedGitBranches -Force
Removes all non-existing branches.

.NOTES
This cmdlet uses `git fetch --prune`, so it will delete references to non-existing branches in the process. Use with caution.
#>
function Remove-DeletedGitBranches
{
    param
    (
        [Parameter()]
        [Switch]
        $Force
    )

    $null = (git fetch --all --prune);
    $branches = git branch -vv | Select-String -Pattern ": gone]" | ForEach-Object { $_.toString().Split(" ")[2] };
    if($Force)
    {
        $branches | ForEach-Object {git branch -D $_};
    }
    else 
    {        
        $branches | ForEach-Object {git branch -d $_};        
    }
}

Set-Alias -Name "rmdelbr" -Value Remove-DeletedGitBranches 
```