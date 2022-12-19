# Git -在不丢失历史记录的情况下将文件夹从一个暂停移动到另一个暂停

> 原文:# t0]https://dev . to/quagle/git-dplacer-des-files-dun-repo-un-other-non-loser-its historical-3kc 4

**用两个字来说，就要说:**

*   将原始休息复制到临时本地文件夹
*   隔离要保留的文件
*   将文件复制到目标 rest
*   做一个〔t0〕

还需要采取一些预防措施，以防止处理错误将混乱置于最初的休息状态。

下面描述的方法是总结/翻译/报告——对[栈溢出](https://stackoverflow.com/questions/1365541/how-to-move-files-from-one-git-repo-to-another-not-a-clone-preserving-history)上 *mcarans* 所描述方法的经验总结。

准备档案

要么 a:要移动的休息
和 b:目的地休息

*   克隆本地休息，然后进入:

    `git clone <lien vers le repo A>`
    T1】

    如果您已经拥有本地休息的副本，建议您创建一个临时文件夹并将其克隆到。

    *   要避免误操作造成的损坏，请断开与 origin 的链接:

    `git remote rm origin`

    现在，如果搞砸了，你就是*安全*。

    *   如果您只想移动特定的资料夹，下列指令只会保留该资料夹及其历程:

    `git filter-branch --subdirectory-filter <~/dossier à garder> -- --all`

    备注:在我的案例中，此订单会传送错误讯息:

    `fatal: <~/dossier à partager> est hors du dépôt`
    T1】

    我没有找到导致这一错误的原因，但这并没有阻止这一进程的继续。

    *   上一步是将文件夹中的所有文件按原样放入您的主休息文件夹中。您可以在移转之前重新排列它们:

    `mkdir <nouveau dossier>`
    `mv * <nouveau dossier>`
    `git add .`

    不需要`push`，因为我们和 origin 的联系已经不多了。

**拉迁移**

*   如果本地没有，则克隆休息 B:
    `git clone <lien vers le repo B>`
    `cd <dossier du repo B>`

*   创建从 b 到 a 的远程连接(其中 a 是 b 的分支):

    `git remote add branche-A <chemin/local/vers/repoA>`

    分支-A 这个词可能是你想要的，这是你选择的任意名称。长话短说，这能节省你的时间，当你不得不修理它的时候。

    StackOverflow 上的解决方案指出了要传输的子文件夹的路径。在我尝试的时候，它会发出错误信息。如果提到 est 文件夹(克隆时创建的主文件夹)，则效果也是一样。

    *   做一件好的旧毛衣:

    `git pull branche-A master --allow-unrelated-histories`

    重要提示:您可能需要更新 git 以识别“`--allow-unrelated-histories`”。在 Ubuntu 中，需要添加 repo:

    `sudo add-apt-repository ppa:git-core/ppa`
    `sudo apt-get update`
    T2】

    *   稍微清理一下，然后我们推:

    `git remote rm <branche-A>`
    T1】

    通常，文件与其提交历史记录一起存放在您的备用 b 上。如果有错的话，您的本地副本和 GitHub 上的休息时间都是*安全*。

    好地方！

*Crédits:[http://gbayer . com/development/moving-files-from-one-git-repository-to-another-preserving-history/](http://gbayer.com/development/moving-files-from-one-git-repository-to-another-preserving-history/)，[https://stack overflow . com/questions/1365541/how-to-move-files-from-one-git-repo-to-anothere-not-a-clone-preserving-history](https://stackoverflow.com/questions/1365541/how-to-move-files-from-one-git-repo-to-another-not-a-clone-preserving-history)。*