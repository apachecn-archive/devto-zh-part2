# 自动化 It -变懒-第二部分

> 原文：<https://dev.to/khmarbaise/automate-it---be-lazy---part-ii-3m3c>

正如我在[上一篇关于自动化的文章](https://dev.to/khmarbaise/automate-it---be-lazy---part-i-2m0m)中所写的，我将继续
我的文章。这就是了。

以下是我的过程中留下的部分:

## 流程

1.  为自己创建一个问题(如果已经存在，转到步骤 2)
2.  根据问题在适当的项目中创建一个分支
3.  将问题分配给我自己
4.  将问题迁移到`IN PROGRESS`状态
5.  完成了。
6.  完成了。
7.  完成了。
8.  完成了。
9.  在注释中引用 commit，关闭相应的 jira 问题。

如果我在我们的构建中对依赖项或父项进行更新，需要为这些更新中的每一个制作一张 JIRA 票(好吧，不是真的需要，但是很有用)。在 Jenkins 中分别测试这些更新是有意义的。最后，这对最终用户来说是一个重要的信息，因此他们可以看到[发生了什么变化](https://blogs.apache.org/maven/entry/apache-shared-component-apache-maven)。

## 自动化第 4 步

第一步是创建一个包含一些信息的标签，比如摘要、描述，并给出`Affected versions`和`Fix Version/s`有用的值。只有在我们还没有工作票的情况下才需要。

到目前为止，我进入浏览器，打开[合适的项目](https://issues.apache.org/jira/projects/MEJB)，按下`Create`按钮，输入合适的值。我已经掌握了从浏览器 url 到命令行的复制和粘贴部分。过了一段时间，我想了想，得出结论:一定有更方便的方法。

我尝试使用 JIRA 的 REST API 和一些 JavaScript，我之前用它们创建了一些[发布说明](https://github.com/khmarbaise/maven-release-notes)，但是通过 JavaScript 处理太复杂了。经过一番研究，我发现了一个看起来很有前途的[命令行客户端](https://github.com/Netflix-Skunkworks/go-jira/)。

我开始摆弄[命令行客户端](https://github.com/Netflix-Skunkworks/go-jira/releases)。关于它有一些事情需要提及:

1.  单个二进制伪像；只需下载并放在路径上。随你怎么命名(我决定命名为`jira-cli`)
2.  创建自定义命令的模板机制
3.  简单而强大的默认命令已经存在
4.  通过 [yaml](http://yaml.org/) 文件轻松配置。

第一件事是，您需要登录到 JIRA 实例，这可以通过以下方式处理:

```
$ jira-cli login --endpoint=https://issues.apache.org/jira 
```

Enter fullscreen mode Exit fullscreen mode

系统将提示您输入密码。用于登录的用户由您正在使用的用户派生而来。对我来说，这是错误的。所以我需要添加一个参数:

```
$ jira-cli login --endpoint=https://issues.apache.org/jira --user=khmarbaise 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我可以很容易地在命令行上查看票据:

```
$ jira-cli view MEJB-118
issue: MEJB-118
created: 339 days ago
status: Closed
summary: Remove "J2EE" from plugin description and site
project: MEJB
issuetype: Task
assignee: khmarbaise
reporter: afloom
fixVersions: 3.0.1
priority: Trivial
votes: 0
description: |
  The term "J2EE" was repalced by "Java EE" by Sun/oracle logn time ago and we shouldn't use "J2EE". It's currently used at least in the plugin's description (pom.xml) and on the intro page of the plugin's site.
  Replace "J2EE Enterprise Javabean (EJB)" with "Java Enterprise JavaBean (EJB)".

comments:
  - | # khmarbaise, 338 days ago
    Done in [r1805332|https://svn.apache.org/r1805332]
  - | # hudson, 338 days ago
    SUCCESS: Integrated in Jenkins build maven-plugins #9069 (See [https://builds.apache.org/job/maven-plugins/9069/])
    [MEJB-118] Remove "J2EE" from plugin description and site (khmarbaise: [http://svn.apache.org/viewvc/?view=rev&rev=1805332])
    * (edit) maven-ejb-plugin/pom.xml
    * (edit) maven-ejb-plugin/src/site/apt/index.apt.vm 
```

Enter fullscreen mode Exit fullscreen mode

仅仅浏览一下某个特定的问题，比通过浏览器浏览要快得多。

在更详细地阅读了文档之后，我发现我可以将 jira 应该使用的关于端点和用户的信息放入一个配置文件(在您的主目录中)`.jira.d/config.yml`，看起来像这样:

```
endpoint: https://issues.apache.org/jira/
user: khmarbaise
login: khmarbaise 
```

Enter fullscreen mode Exit fullscreen mode

这样，登录命令可以简化为`jira-cli login`。容易吗？`jira-cli`将安全会话信息保存在`.jira.d/`目录中，该目录存储在`cookies.js`文件中。为了方便起见，我在`.jira.d`中创建了一个 Git 存储库，并将`cookies.js`添加到了`.gitignores`文件中，以防止我意外提交`cookies.js`文件。

现在，我能够通过命令行创建 JIRA 门票。

到目前为止一切顺利。但是，我如何知道我需要为哪个项目创建票证呢？

但是等一下。`pom.xml`包含信息`project.issueManagement.url`，它是 JIRA 跟踪器的 URL。所以我需要想办法从`pom.xml`中提取它。

幸运的是 [maven-help-plugin](https://maven.apache.org/plugins/maven-help-plugin/) 就在这里，看起来像这样(为了简洁起见，我增加了`\`；通常这是单行):

```
ISSUE_URL=$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate \
  -Dexpression=project.issueManagement.url \
  -q \
  -DforceStdout) 
```

Enter fullscreen mode Exit fullscreen mode

这将从 pom 文件中提取 JIRA 的 URL。所以你会得到这样的结果:

```
https://issues.apache.org/jira/browse/MDEPLOY 
```

Enter fullscreen mode Exit fullscreen mode

但不幸的是这不是你需要的`jira-cli`的项目。我需要的是`MDEPLOY`。这可以通过
轻松实现

```
PROJECT=$(basename $ISSUE_URL) 
```

Enter fullscreen mode Exit fullscreen mode

这些是创建 JIRA 机票所需的信息。百米...实际上没有，`Fixed versions/s`和`Affected versions`的版本呢？这可以通过:

```
PLAIN_VERSION=$(mvn build-helper:parse-version \
  -Dx=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion}.\${parsedVersion.incrementalVersion} \
  org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate \
  -Dexpression=x \
  -q \
  -DforceStdout) 
```

Enter fullscreen mode Exit fullscreen mode

这将从 pom 文件中提取出不带结尾`-SNAPSHOT`的版本。它假设版本号有三位数。

所以现在来了一个`jira-cli`最喜欢的功能。你可以非常容易地创建你自己的[命令](https://github.com/Netflix-Skunkworks/go-jira#custom-commands-1)。您只需在您的`config.yml`文件:
中定义这样的东西

```
custom-commands:
  - name: mine
    help: display issues assigned to me
    script: |-
      {{jira}} list --template table --query "resolution = unresolved and assignee=currentuser() ORDER BY priority asc, created" 
```

Enter fullscreen mode Exit fullscreen mode

所以现在你可以简单地这样调用`jira-cli`:

```
$ jira-cli mine 
```

Enter fullscreen mode Exit fullscreen mode

您将看到分配给您自己的问题列表。详见`jira-cli`文档。基于以上内容，我创建了一个自定义命令，如下所示:

```
custom-commands:
  - name: createdependencyupgrade
    help: creates an Dependency Upgrade ticket.
    options:
      - name: affected
        help: The affected version
      - name: fix
        help: The fix versions.
      - name: project
        default: $JIRA_PROJECT
      - name: description
        default: ""
      - name: priority
        default: "Minor"
    args:
      - name: summary
        required: true
    script: |-
      {{jira}} create --project={{options.project}} --issuetype="Dependency upgrade" -o summary="{{args.summary}}" -o description="{{options.description}}" -o fixVersions="{{options.fix}}" -o versions="{{options.affected}}" -o priority="{{options.priority}}" --noedit 
```

Enter fullscreen mode Exit fullscreen mode

因此，我可以通过这个函数创建一张 JIRA 票:

```
$ jira-cli createdependencyupgrade --project MWAR --fix="3.0.2" --affected="3.0.2" "Summary Text" 
```

Enter fullscreen mode Exit fullscreen mode

这些都是最终以非常方便的方式创建 JIRA 机票的先决条件。
之前的事情和整合一些其他部分的结果现在是我的
[createdependencyupgradeisequest . sh](https://github.com/khmarbaise/automation-scripts/blob/1df1517f27f0ae831ba7f17085fd4feaa3f3e70f/createdependencyupgradeissue.sh)脚本我可以简单地这样叫:

```
$ createdependencyupgradeissue.sh "Upgrade maven-plugins parent to version 32" 
```

Enter fullscreen mode Exit fullscreen mode

此时，该脚本的输出如下所示:

```
$ createdependencyupgradeissue.sh "Upgrade maven-plugins parent to version 32"
Checking if we are on a branch...done.
Check that we are on master...done.
Extracting issue url from pom.xml file...done.
Extracting version from pom.xml file...done.
Creating JIRA issue...done.
OK MWAR-419 https://issues.apache.org/jira/browse/MWAR-419
M    pom.xml
Switched to a new branch 'MWAR-419' 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能已经意识到的，我已经添加了一些东西，以基于 JIRA 问题名称在 Git 中创建一个分支，并切换到该分支。

自动化的第四步完成了，这意味着更多的改进，但还不够。

## 自动化第五步

如果我想给自己分配一个票证，那么我必须转到票证页面并单击`assign to me`链接，这是了解问题编号并键入正确 URL 等的先决条件。

基于我的假设，分行名称是 JIRA 发行号，我唯一需要做的就是从分行中提取号码。这可以简单地通过:
实现

```
BRANCH=$(git symbolic-ref --short HEAD) 
```

Enter fullscreen mode Exit fullscreen mode

所以接下来的事情就是把票分配给我自己。这个
可以用`jira-cli`轻松完成，就像这样:

```
jira-cli take $BRANCH 
```

Enter fullscreen mode Exit fullscreen mode

我必须添加一些完整性检查以防出错，结果是我可以在分支中使用的 [takeissue.sh](https://github.com/khmarbaise/automation-scripts/blob/11b71f2d8e32975fe3eacfec86493db5f4e811f8/takeissue.sh) 脚本:

```
$ takeissue.sh 
```

Enter fullscreen mode Exit fullscreen mode

自动化的第五步已经完成，这意味着变得舒适，但我认为，我们可以做得更多。

## 自动化步骤 6

这一步与前一步大致相同，因为我想将问题的状态改为`IN PROGRESS`。这可以通过像这样使用`jira-cli`很容易地实现:

```
$ jira-cli start MDEP-234 
```

Enter fullscreen mode Exit fullscreen mode

基于之前的想法，这可以被烘焙成一个脚本 [startissue.sh](https://github.com/khmarbaise/automation-scripts/blob/11b71f2d8e32975fe3eacfec86493db5f4e811f8/startissue.sh) 从而产生一个简单的调用

```
$ startissue.sh 
```

Enter fullscreen mode Exit fullscreen mode

自动化的第六步完成了，这意味着更多的改进是可能的。

## 自动化第 7 步

好的，我可以在 JIRA 创建一个问题，包括在 Git 中创建分支，那么在这里提交什么呢？通常我会更新文件或者添加一些新的文件，这是由`git add .`完成的，然后我可以提交状态。

因此，基于这个问题，提交消息应该是这样的:

```
[XXX-123] - Summary 
Optional description 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，我只需要第一行。换句话说，这是从我之前创建的问题中复制和粘贴的。让我们让这变得更容易。我创建了一个脚本 [commitonissue.sh](https://github.com/khmarbaise/automation-scripts/blob/11b71f2d8e32975fe3eacfec86493db5f4e811f8/commitonissue.sh) 来完成这个任务。

它根据 Git 中的分支名称来标识分支，并通过来自 JIRA 的`jira-cli`获取关于摘要的信息，这导致了这样一行:

```
SUMMARY=$(jira-cli view $BRANCH | grep "^summary: " | cut -d " " -f2-) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我需要进行真正的提交，这可以通过下面的代码来处理:

```
git commit -a -m"[$BRANCH] - $SUMMARY" 
```

Enter fullscreen mode Exit fullscreen mode

自动化的第七步已经完成，这意味着我们已经大功告成。但还没有越界。

## 自动化第 8 步

这一步其实没什么新的，只是改进了一些已经存在的东西。第一篇帖子的 [gitmergeclean](https://github.com/khmarbaise/automation-scripts/blob/5e3545d17805ab768edcafd89c4ba1e76af8b82e/gitmergeandclean.sh) 脚本。这需要调整。它还应该在分支机构合并到 master 后关闭 JIRA 票证，并在其上添加适当的注释。

我在脚本中添加了以下几行来处理这个问题。

```
#
# Get the latest commit HASH
#
COMMITHASH=$(git rev-parse HEAD)
#
# Get the GIT URL from pom file:
# TODO: Can we do some sanity checks? Yes: scm:git:..  if not FAIL!
echo -n "Get the git url from pom file..."
GITURL=$(mvn help:evaluate -Dexpression=project.scm.connection -q -DforceStdout | cut -d":" -f3-)
echo " URL: $GITURL"
GITPROJECT=$(basename $GITURL)
GITBASE=$(dirname $GITURL)
#
echo "Closing JIRA issue $BRANCH"
jira-cli close -m"Done in [$COMMITHASH|$GITBASE?p=$GITPROJECT;a=commitdiff;h=$COMMITHASH]" --resolution=Done $BRANCH
## Error handling?
echo "Closing finished." 
```

Enter fullscreen mode Exit fullscreen mode

在脚本的[开头已经提取了`BRANCH`信息。脚本的最后几行是调用`jira-cli`用适当的注释结束问题，并将`resolution`设置为一个有用的值。在我们的例子中，它就是简单的`Done`。`GITURL`等。被提取以创建一个有用的注释，该注释包含一个到真实 git 存储库的链接，并显示了已经进行的提交的差异。您可以查看一下](https://github.com/khmarbaise/automation-scripts/blob/master/gitmergeandclean.sh#L9) [MCLEAN-87](https://issues.apache.org/jira/browse/MCLEAN-87) 问题，它显示了到 git 存储库的链接。

新的 [gitmergeandclean](https://github.com/khmarbaise/automation-scripts/blob/aa5dde35b986ded11bf7b65cab6cdce407e6f8b4/gitmergeandclean.sh) 脚本的完整输出如下所示:

```
~/ws-git-maven/plugins/maven-rar-plugin (MRAR-79)$ gitmergeandclean.sh
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
Updating 1ffdfba..055bbda
Fast-forward
 pom.xml | 3 +--
 1 file changed, 1 insertion(+), 2 deletions(-)
Total 0 (delta 0), reused 0 (delta 0)
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-rar-plugin.git
remote:    1ffdfba..055bbda  055bbda0907661aa6515cc8e79ba66cee43aec12 -> master
remote: Syncing refs/heads/master...
To https://gitbox.apache.org/repos/asf/maven-rar-plugin.git
   1ffdfba..055bbda  master -> master
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-rar-plugin.git
remote:  - [deleted]         MRAR-79
remote: Syncing refs/heads/MRAR-79 (FORCED)...
To https://gitbox.apache.org/repos/asf/maven-rar-plugin.git
 - [deleted]         MRAR-79
Deleted branch MRAR-79 (was 055bbda).
Get the git url from pom file... URL: https://gitbox.apache.org/repos/asf/maven-rar-plugin.git
Closing JIRA issue MRAR-79
OK MRAR-79 https://issues.apache.org/jira/browse/MRAR-79
Closing finished. 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

最后，对于依赖关系升级，过程如下所示:

1.  更改`pom.xml`文件中的适当信息
2.  `createdependencyupgradeissue.sh "Upgrade maven-plugins parent to version 32"`
3.  `commitonissue.sh`
4.  `gitpushwithlease.sh`
5.  `takeissue.sh`
6.  在那之后，我必须等待詹金斯的结果。如果构建成功，我可以合并回 master，否则我需要找到问题的原因。在这种情况下，结果是成功的，所以我可以这样做:
7.  `gitmergeandclean.sh`

总的输出如下所示:

```
~/ws-git-maven/plugins/maven-linkcheck-plugin (master *)$ createdependencyupgradeissue.sh "Upgrade maven-plugins parent to version 32"
Checking if we are on a branch...done.
Check that we are on master...done.
Extracting issue url from pom.xml file...done.
Extracting version from pom.xml file...done.
Creating JIRA issue...done.
OK MLINKCHECK-32 https://issues.apache.org/jira/browse/MLINKCHECK-32
M    pom.xml
Switched to a new branch 'MLINKCHECK-32'
~/ws-git-maven/plugins/maven-linkcheck-plugin (MLINKCHECK-32 *)$ commitonissue.sh
[MLINKCHECK-32 cd34b97] [MLINKCHECK-32] - Upgrade maven-plugins parent to version 32
 1 file changed, 1 insertion(+), 1 deletion(-)
~/ws-git-maven/plugins/maven-linkcheck-plugin (MLINKCHECK-32)$ gitpushwithlease.sh
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 1.53 KiB | 1.53 MiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-linkcheck-plugin.git
remote:  * [new branch]      cd34b97ddb661d5881bf7fc01e3291a9b0f67041 -> MLINKCHECK-32
remote: Syncing refs/heads/MLINKCHECK-32...
To https://gitbox.apache.org/repos/asf/maven-linkcheck-plugin.git
 * [new branch]      MLINKCHECK-32 -> MLINKCHECK-32
~/ws-git-maven/plugins/maven-linkcheck-plugin (MLINKCHECK-32)$ takeissue.sh
~/ws-git-maven/plugins/maven-linkcheck-plugin (MLINKCHECK-32)$ startissue.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在是我必须等待詹金斯的结果的时候了。在这种情况下，结果是成功的，所以我可以这样继续:

```
~/ws-git-maven/plugins/maven-linkcheck-plugin (MLINKCHECK-32)$ gitmergeandclean.sh
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
Updating ff5dfab..cd34b97
Fast-forward
 pom.xml | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
Total 0 (delta 0), reused 0 (delta 0)
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-linkcheck-plugin.git
remote:    ff5dfab..cd34b97  cd34b97ddb661d5881bf7fc01e3291a9b0f67041 -> master
remote: Syncing refs/heads/master...
To https://gitbox.apache.org/repos/asf/maven-linkcheck-plugin.git
   ff5dfab..cd34b97  master -> master
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-linkcheck-plugin.git
remote:  - [deleted]         MLINKCHECK-32
remote: Syncing refs/heads/MLINKCHECK-32 (FORCED)...
To https://gitbox.apache.org/repos/asf/maven-linkcheck-plugin.git
 - [deleted]         MLINKCHECK-32
Deleted branch MLINKCHECK-32 (was cd34b97).
Get the git url from pom file... URL: https://gitbox.apache.org/repos/asf/maven-linkcheck-plugin.git
Closing JIRA issue MLINKCHECK-32
OK MLINKCHECK-32 https://issues.apache.org/jira/browse/MLINKCHECK-32
Closing finished. 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你看看我之前的帖子，你可能会惊讶于数字处理步骤并没有显著减少？这些脚本取代了大量的手动步骤(git 命令行输入、浏览器中的 jira、点击输入等)。)现在只是由脚本处理，这意味着步骤总是以相同的方式完成，我确信我不会忘记步骤。此外，许多脚本也可以在其他情况下使用，这使得我的生活更容易，例如在处理拉请求等。

所以这是一个更长的帖子。我希望你喜欢它。