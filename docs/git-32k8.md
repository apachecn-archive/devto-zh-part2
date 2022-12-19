# 关于 Git 的使用方法

> 原文：<https://dev.to/programmingmonky/git-32k8>

本文对简单的 git 的使用方法进行说明。

笔者的环境

*   操作系统:windows 7 32 位
*   控制台:电源外壳

# git 的说明稍有不同

git 是管理源代码变更历史的软件。
的特征如下

*   无论管理的源大小
    *   类似 Linux 的超过 2 千万行的巨大代码
    *   个人创作小插件
    *   等各种软件中活用。
*   有多年的使用实绩
    *   2005 年开发了最初的版本，有 10 年以上的使用实绩。
*   免费

由于未部署 git 而带来的挑战包括

*   不知道在生产环境中运行的二进制文件的版本。
*   报告的数据在开发团队的版本中不再现
*   源代码的合并是由有技术能力的技术人员小心翼翼地进行的。
*   无法快速捕获并即时验证未合并到生产中的他人的更改。
*   并行开发中进行的源代码与生产过于背离

当然，git 不是银子弹，所以引进并不能解决
问题，但
管理员和开发者的工作量可以大幅减少。

# 马上试试看

## 1 .移动到版本管理对象的文件夹

首先，创建要进行版本控制的文件夹并移动到那里。

```
cd c:\
mkdir story
cd story 
```

Enter fullscreen mode Exit fullscreen mode

## 2 .初始化 git

初始化 git 时点击`git init`和命令

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

然后在 story 文件夹中创建了一个名为. git 的文件夹。

[![git](../Images/7a7f8b2a90f4f60d69ef0ffa6a58fd66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4GLus4_R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ei38bnbspa80z7xjsu4.png)

git 通过内部处理在这个文件夹中记录变更
(如果只是利用 git 的话就不需要自己触摸这个文件。 ）

## 3 .设定要记录的用户名

在 git 中，作为变更记录，与变更内容一起，是谁进行了变更？ 记录。
用以下命令设置要注册到你的记录中的姓名和邮箱地址。

> note :请试着把`hogehoge`和`hogehoge@example`作为你的名字和邮箱地址。

```
git config --global user.name hogehoge
git config --global user.email hogehoge@example.com 
```

Enter fullscreen mode Exit fullscreen mode

## 4 .编辑记录对象的文件

那么我马上制作记录对象的文件。
首先制作作为最初版本的文件。
请用适当的编辑器在 story 正下方创建名为`memo.txt`的空文件

```
PS C:\story> dir

    ディレクトリ: C:\story

Mode                LastWriteTime     Length Name
---------                -------------     ------ ----
-a---        2018/07/11     11:35          0 memo.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 5 .记录( commit )变更为 git 的文件

使用以下命令在 git 中注册更改:

```
git add memo.txt
git commit -m "first commit" 
```

Enter fullscreen mode Exit fullscreen mode

记录了第二行的" first commit "时的消息(提交消息)称为
。

The result of transportation

```
PS C:\story> git add memo.txt
PS C:\story> git commit -m "first commit"
[master (root-commit) 7bbaaca] first commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 memo.txt 
```

Enter fullscreen mode Exit fullscreen mode

该命令完成后，使用`git log`命令确认 git 的历史记录。
知道最初的版本被记录了！

```
PS C:\story> git log
commit 7bbaacae2f0ff56573ce60497ec96db2886cc78e (HEAD -> master)
Author: hogehoge <hogehoge@example.com>
Date:   Wed Jul 11 11:37:55 2018 +0900

    first commit 
```

Enter fullscreen mode Exit fullscreen mode

## 6 .重复几次步骤 4、5

那么在 memo.txt 中添加合适的故事，并将变更记录作为版本进行记录。

```
PS C:\story> echo "A long time ago " >> memo.txt
PS C:\story> type .\memo.txt
A long time ago
PS C:\story> git add .\memo.txt
PS C:\story> git commit -m "add period"
[master 531432b] add period
 1 file changed, 0 insertions(+), 0 deletions(-)

PS C:\story> echo "in a glaxy far," >> memo.txt
PS C:\story> type .\memo.txt
A long time ago
in a glaxy far,
PS C:\story> git add .\memo.txt
PS C:\story> git commit -m "add place"
[master aa3c2de] add place
 1 file changed, 0 insertions(+), 0 deletions(-)

PS C:\story> echo "far away.... ">> memo.txt
PS C:\story> type .\memo.txt
A long time ago
in a glaxy far,
far away....
PS C:\story> git add .\memo.txt
PS C:\story> git commit -m "explain about place"
[master 74768be] explain about place
 1 file changed, 0 insertions(+), 0 deletions(-)
PS C:\story> t 
```

Enter fullscreen mode Exit fullscreen mode

分别进行了 3 次利用 echo 指令的文件写入和
版本记录。

※type 指令是为了确认内容而打的。

memo.txt 文件的内容更改如下:

```
A long time ago
in a glaxy far,
far away.... 
```

Enter fullscreen mode Exit fullscreen mode

## 7 .确认 git 的履历

那么，我们来确认一下记录的版本的历史记录吧。

`git log`至今为止的版本记录可以用命令确认。

※通常以新顺序显示，但这次带有--reverse 选项，按照旧顺序显示。

```
PS C:\story> git log --reverse
commit 7bbaacae2f0ff56573ce60497ec96db2886cc78e
Author: hogehoge <hogehoge@example.com>
Date:   Wed Jul 11 11:37:55 2018 +0900

    first commit

commit 531432b6a496d0a9d25b6c43f8bf773baa737c82
Author: hogehoge <hogehoge@example.com>
Date:   Wed Jul 11 11:44:02 2018 +0900

    add period

commit aa3c2de5e9ed92be14ef324878c6354be1c200ea
Author: hogehoge <hogehoge@example.com>
Date:   Wed Jul 11 11:45:21 2018 +0900

    add place

commit 74768be738a0dc3213ca6913ccdca9c6665e5fc7 (HEAD -> master)
Author: hogehoge <hogehoge@example.com>
Date:   Wed Jul 11 11:46:42 2018 +0900

    explain about place 
```

Enter fullscreen mode Exit fullscreen mode

## 8 .试着恢复到想要恢复的版本( reset )

那么我想在这里发挥版本管理系统的威力。
在本例中

*   第一个版本:创建空文件
*   第二个版本:时间记载( A long time ago )
*   第三个版本:位置说明( in a glaxy far，)
*   第四个版本:地点补充( far away ... )

这样记录了版本。
现在我想把文件的内容恢复到第二个版本的状态。

返回文件时使用`git reset --hard <バージョン>`。

commit 的 id (确切地说，称为提交的散列)是 git log 命令结果中每个" commit "右侧显示的值。

| 版本 | commitのid |
| --- | --- |
| 第一个版本 | 7 bbaacae 2 f 0 ff 56573 ce 60497 EC 96 db 2886 cc 78 e |
| 第二个版本 | 531432 b 6 a 496d 0 a 9d 25 b 6 c 43 f 8 BF 773 baa 737 c 82 |
| 第三个版本 | aa 3c 2 de 5 e 9 ed 92 be 14 ef 324878 c 6354 be 1c 200 ea |
| 第四个版本 | 74768 be 738 a0dc 3213 ca 6913 cdca 9 c 6665 e5fc 7 |

※请注意，此 commit 的 id 值因环境而异。

第二个版本的 commit 是`531432b6a496d0a9d25b6c43f8bf773baa737c82`，所以指定
作为要还原的版本

```
PS C:\story> git reset --hard 531432b6a496d0a9d25b6c43f8bf773baa737c82
HEAD is now at 531432b add period 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看看 memo.txt。

```
PS C:\story> type memo.txt
A long time ago 
```

Enter fullscreen mode Exit fullscreen mode

memo.txt 的内容恢复到了版本 2 时的状态！

以上是简单的 git 版本控制示例。
git 功能更高，但仅凭本步骤书内的内容，可以进行以下操作。

> 在半年的会计处理中发现了与 3 月前的数据不一致的东西。
> 好像是最新版本做不到的数据。
> 本地复制最新版本的源，还原到
> 数据制作时期的版本，
> 通过调试探索制作该数据的原因

之类的。

这里说明的内容只是初步的，请推进
公式的 git 教程等进一步加深理解。

[https://git-scm.com/book/ja/v2](https://git-scm.com/book/ja/v2)

希望大家学习 git，实践进一步的版本控制的运用。