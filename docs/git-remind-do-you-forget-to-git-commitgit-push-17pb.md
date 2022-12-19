# git-提醒:忘记 git commit/git push 了吗？

> 原文：<https://dev.to/suin/git-remind-do-you-forget-to-git-commitgit-push-17pb>

你忘了 git commit 还是 git push？ [git-remind](https://github.com/suin/git-remind) 就是为这种健忘的人(比如我)准备的命令行工具。它监视 Git 存储库的状态，并报告哪些存储库需要提交或推送。

## 特征

### git 提交/git 推送状态

git-remind 检查您计算机中的所有 git 存储库，并显示是否有未提交的文件，以及是否有应该推送到远程的提前提交。

[![](../Images/3e877dac212d4a9ace11ab53ec5a1c91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UtRm7Hur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vfx2blo98d2k0nxqn10b.png)

### 桌面通知

git-remind 使用桌面通知来通知您 git-commit/git-push 状态。这项功能已经在 macOS 上测试过了，但它可以在 Windows 和 Linux 上运行。

[![](../Images/6962bf1370e5da5abbee6d3d57ee6a12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OK_HLVZw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/daza4qkeldvq6r33ga4q.png)

## 装置

### 公司自产自用

```
brew install suin/suin/git-remind 
```

### 用手

从[版本](https://github.com/suin/git-remind/releases)下载编译二进制文件。

## 配置

使用您自己的存储库目录的绝对路径，将`remind.paths`添加到您的`.gitconfig`中。

```
[remind]
    paths = /Users/you/projects/foo,/Users/you/projects/bar 
```

如果您在同一个目录中(例如在`~/projects`中)有一些 git 库(例如`~/projects/foo`和`~/projects/bar`)，您可以使用通配符指定路径(例如`/Users/you/projects/*`):

```
[remind]
    paths = /Users/you/projects/* 
```

使用以下命令检查配置:

```
git-remind paths # Shows path patterns configuration
git-remind repos # Shows git repositories to be reminded 
```

## 使用

### 显示 git 提交/git 推送状态

```
$ git-remind status
Need to commit: /Users/suin/projects/myapp1
Need to commit and push: /Users/suin/projects/myapp2 
```

#### 类似`git status -s`的短状态

```
$ git-remind status -s
C  /Users/suin/projects/myapp1
CP /Users/suin/projects/myapp2 
```

#### 显示所有状态，包括最新的存储库

```
$ git-remind status -a
Need to commit: /Users/suin/projects/myapp1
Need to commit and push: /Users/suin/projects/myapp2
Up-to-date: /Users/suin/projects/myapp3 
```

### 桌面通知

```
git-remind status-notification 
```

## 高级用法

### 预定提醒(cron)

要获得预定提醒，请设置 crontab。以下示例每 20 分钟发送一次通知:

```
PATH=/usr/local/bin:/usr/bin:/bin

*/20 * * * * git-remind status-notification > /dev/null 2>&1 
```

### 跳到储存库

通过结合 git-remind 和 [fzf](https://github.com/junegunn/fzf) ，您将能够跳转到需要提交或推送的存储库:

```
cd $(git remind status -n | fzf) 
```

[![](../Images/9a9505254c94d717ea54301a3c2be777.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FYn0MzI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7lzjcngv47h6zusc8ufe.gif)

在您的。bashrc/。zshrc:

```
alias grj='cd $(git remind status -n | fzf)' 
```

## 参考

*   GitHub: [suin/git-remind:永远不要忘记 git 提交和推送](https://github.com/suin/git-remind)