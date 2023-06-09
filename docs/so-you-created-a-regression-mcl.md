# 所以你创造了回归？

> 原文：<https://dev.to/ferricoxide/so-you-created-a-regression-mcl>

有时，当您在 git 管理的文件中修复文件时，您将通过 nuking 一行(或整个代码块)代码来创建一个回归。如果您能记起那个被核化的代码块中的一些内容，您可以编写一个快速脚本来查找引用该代码块的所有以前的提交。

在这个特殊的例子中，我的一个同事正在编写一些 Jenkins 管道定义。管道需要自动创建 S3 预签名的网址。在某种程度上，这样做的例行程序遭到了破坏。因为提出必要的插值保护是一件痛苦的事情，我们真的不想经历重新发明那个特殊轮子的痛苦。

所以，如何让 git 帮我们找到缺失的片段。`git log`，快速循环迭代，可以做到这一点:

> ```
>  for FILE in $( find <PROJECT\_ROOT\_DIR> -name "\*.groovy" )
> do
>    echo $FILE
>    git log --pretty=" %H" -Spresign $FILE
> done | grep ^commit 
> ```

在上面:

*   我们在由最初的`git clone`调用创建的目录中执行。为了限制搜索范围，您还可以从项目的子目录中运行它。
*   因为在这个例子中，我们知道所有的 Jenkins 管道定义都以扩展名`.groovy`结尾，所以我们将搜索限制在那些文件类型上。
*   `-Spresign`用于告诉`git log`寻找字符串`presign`。
*   `--pretty=" %H"`抑制来自`git log`命令输出的所有其他输出——确保只打印 commit-ID。引用字符串中的前导空格提供了一点缩进，使输出分组更容易理解。

这个快速循环为我们提供了一个很好的提交 id 列表，如下所示:

> ```
>  ./Deployment/Jenkins/agent/agent-instance.groovy
>    64e2039d593f653f75fd1776ca94bdf556166710
>    619a44054a6732bacfacc51305b353f8a7e5ebf6
>    1e8d5e40c7db2963671457afaf2d16e80e42951f
>    bb7af6fd6ed54aeca54b084627e7e98f54025c85
> ./Deployment/Jenkins/master/Jenkins\_master\_infra.groovy
> ./Deployment/Jenkins/master/Jenkins\_S3-MigrationHelper.groovy
> ./Deployment/Jenkins/master/Master-Ec2-Instance.groovy
> ./Deployment/Jenkins/master/Master-Elbv1.groovy
> ./Deployment/Jenkins/master/parent-instance.groovy 
> ```

在上面的例子中，只有文件`Deployment/Jenkins/agent/agent-instance.groovy`包含我们搜索的字符串。首先列出的 commit-ID(在文件名下缩进)包含目标字符串的减法 diff。类似地，第二个 commit-ID 包含我们实际上在寻找的代码片段*。剩余的提交 id 包含原始的“轮子的发明”，*

 *在这种特殊的情况下，我们不能简单地返回到特定的提交，因为还有许多其他需要的更改。然而，它确实允许开发者使用`git show`,这样他就可以在当前版本的管道中复制出他想要的完整片段。*