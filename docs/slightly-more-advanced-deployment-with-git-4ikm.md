# (稍微高级一点)使用 git 部署

> 原文：<https://dev.to/blazselih/slightly-more-advanced-deployment-with-git-4ikm>

# 部署有时会很痛苦，甚至会出错

正如大多数开发人员可以确认的那样，部署和更新一个基本的 web 应用程序并不是真正的火箭科学。将更新的文件传输到服务器，也许做一些本地编辑，重新启动，然后期待最好的结果。在许多情况下，这种幼稚的方法可能已经足够好了，但是一旦涉及到数据库结构的更新和依赖关系的改变，事情就会变得很混乱。
此外，通过手动编辑和执行大量 SQL 文件来保持开发、测试和生产数据库结构同步是一项繁琐且容易出错的工作。手动管理包和依赖项也是如此。

因为我们无论如何都应该使用版本控制系统，所以我们将尝试利用 git 来执行上面的一些任务。

# Git(钩子)救援

互联网上有很多“使用 git 部署您的应用程序”的文章。其中大部分展示了如何使用 post-receive 挂钩将文件从存储库转储到项目文件夹中，并重启服务器。在这篇文章中，我将尝试对此进行扩展，并展示如何使用 shell 脚本来自动升级数据库和软件包。我还将展示如何保存一个简单的部署日志来提供基本的回滚功能。
这些都是非常基本的东西，但这是迈向持续集成(CI)和交付(CD)解决方案等更复杂系统的第一步。希望即使是这个简单的设置也能让你作为开发人员的生活变得轻松一些。

作为一个例子，我们将部署一个 Python ( [Flask](http://flask.pocoo.org/) )应用程序到 WebFaction 服务器。Pip 用于管理虚拟环境中的依赖性，而 [Flask-Migrate](https://flask-migrate.readthedocs.io/en/latest/) 用于处理 [SQLAlchemy](https://www.sqlalchemy.org/) 数据库迁移。
该应用程序是根据 [Miguel Grindberg 的](https://blog.miguelgrinberg.com/index)优秀的 [Flask Web 开发书籍](https://www.amazon.com/Flask-Web-Development-Developing-Applications/dp/1449372627/)和 [Flask Mega 教程](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)构建的。
无论使用何种编程语言、框架和库，类似的原则都应该适用。

假设您对 git 和 shell 脚本有一些基本的了解。我不会深入到每一个细节，代码片段在这里只是为了说明。接收前和接收后挂钩的示例在底部供参考。

# 本地设置和工作流程

在客户端，除了在创建 git remote 之后添加一个以外，没有什么需要设置的。

一旦我们完成了新的惊人特性的开发和测试，是时候部署它了。我们将不得不:

1.  激活虚拟环境。
2.  通过执行`pip freeze > requirements.txt`将当前包配置保存到一个文件中。
3.  因为 pip[并没有真正完成一个完整的依赖关系解析](https://github.com/pypa/pip/issues/988),所以最好进行验证，如果需要的话，手动编辑文件。参见 [pip 手册](https://pip.pypa.io/en/stable/user_guide/#requirements-files)。
4.  完成后，将 requirements.txt 添加到 repo 中。
5.  向 repo 添加任何新的数据库迁移文件，并提交更改。
6.  按下遥控器。

现在，服务器将接管，执行接收前和接收后脚本，并为我们部署应用程序。
快乐的日子！

上述所有步骤也可以作为 shell 脚本来执行。

# Initail 远程设置

*首先，我强烈建议您创建相同的试运行和生产环境，小型非关键应用除外。在部署到生产环境之前，在试运行环境中尝试一切。
此外，根据您的主机提供商，远程设置会略有不同，但基本原理应该是相同的。*

Ssh 到您的服务器，并创建一个空的 git 存储库(`git init --bare`)。像往常一样设置 SSH 密钥，然后添加新创建的 repo 作为远程。将初始配置推送到此遥控器。

将文件从 repo 中签出到您的工作目录。这可能不是绝对必要的，但我喜欢将我的工作目录和存储库保存在单独的文件夹中(裸 repo，见上文)。

上传不包括在版本控制中的文件(安全敏感信息)并设置所需的环境变量。

配置数据库、网络服务器等。并检查是否一切正常。

这是现在的初始配置。

# 使用服务器端钩子部署

一旦推送到达服务器，我们将不得不:

1.  检查是否部署了正确的分支。
2.  将更新的文件传输到服务器，并更新工作目录。
3.  安装和/或更新新的软件包/依赖项。
4.  升级数据库。
5.  最后，记录更改并重启服务器。

我们将使用[接收前和接收后挂钩](https://git-scm.com/book/gr/v2/Customizing-Git-Git-Hooks)来执行这些步骤。

### 1)分行检查

首先，我们需要确保只有正确的分支(主)得到部署。接收前和接收后挂钩都接收 stdin 上推送的引用列表(以旧哈希新哈希引用的形式)。我们可以迭代这些信息来执行我们的检查。如果预接收挂钩非零退出，则不接受推送的引用，部署将以这种方式中止:

```
#!/bin/bash
# pre-receive
while read OLD NEW REF
do
    if ! [[ $REF =~ '/master'$ ]] ; then echo "Pushing non-master branch $REF, aborting."
        exit 1
    fi
done 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这种情况下，存储库只包含“可部署的”主分支，所有其他分支都被拒绝。如果您希望存储其他分支，您可以很容易地将上述逻辑集成到 post-receive 挂钩中，而根本不使用 pre-receive。

### 2)文件传输

推送完成时会更新远程存储库。为了更新工作目录，我们需要使用 post-receive 钩子从存储库中取出文件:

```
#!/bin/bash
# post-receive
export GIT_WORK_TREE=/home/...        # working directory
git checkout $NEW -f master 
```

Enter fullscreen mode Exit fullscreen mode

通过强制覆盖(`-f`)覆盖任何本地更改。

### 3)软件包(虚拟环境)更新

首先，我们需要确定是否需要任何更新。因为我们使用 pip 来管理包，我们可以检查在推送过程中是否更新了 *requirements.txt* 文件:

```
git diff --name-only $OLD $NEW | grep 'requirements.txt' 
```

Enter fullscreen mode Exit fullscreen mode

如果 *requirements.txt* 被更改，我们将激活虚拟环境并运行`pip install -qr requirements.txt` :

```
PIP=0
if git diff --name-only  $OLD $NEW | grep -q 'requirements.txt'; then source /home/.../venv/bin/activate
    pip install -qr requirements.txt
    PIP=1
fi 
```

Enter fullscreen mode Exit fullscreen mode

选项-q 将抑制相当冗长的 pip 输出，但仍会显示错误。变量 PIP 稍后用于日志记录(见下文)。

### 4)数据库升级

数据库迁移文件存储在 *app/migrations/versions* 中，因此我们可以使用相同的 git diff 技巧来检测任何更改:

```
git diff --name-only  $OLD $NEW | grep 'app/migrations/versions' 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们需要激活虚拟环境，然后执行 manager.py 来升级数据库:

```
DB=0
if git diff --name-only $OLD $NEW | grep -q 'app/migrations/versions'; then source /home/.../venv/bin/activate
    python manager.py db upgrade
DB=1
fi 
```

Enter fullscreen mode Exit fullscreen mode

### 5)修改日志并重启服务器

我们将使用当前时间、作者、旧的 git 散列、新的 git 散列以及包和数据库更新状态来生成一个简单的 CSV 日志文件:

```
AUTHOR=`git show $NEW -s --format="%an"`
TIME=`date -Is`
echo "$TIME,$AUTHOR,$OLD,$NEW,$PIP,$DB" >> updates.log 
```

Enter fullscreen mode Exit fullscreen mode

最后，重启服务器应该是不言自明的。

如果一切按计划进行，更新后的应用程序现在应该正在运行。

# 回滚

首先，请注意，有时完全回滚甚至是不可能的，因为降级数据库时数据可能会丢失。部署一个小的补丁通常比执行完整的回滚更容易。

如果您仍然需要恢复到旧版本，您可以使用来自日志、存储库和本地环境的信息来恢复正常工作。

这最好通过手动反转部署步骤来完成:

1.  试着找出哪里出错了(到目前为止最重要的一步)！
2.  激活虚拟环境。
3.  请查阅日志并验证数据库是否已升级。如果需要，执行`manage.py db downgrade`。这将使数据库结构降级到以前的版本。在此步骤中，您可能会丢失一些数据！
4.  从存储库中签出旧文件，强制覆盖。
5.  检查软件包是否已升级，如果需要，通过执行`pip install -r requirements.txt`将其降级。幸运的是，大多数包都是向后兼容的，通常不需要这一步。
6.  重启服务器，希望一切顺利。

# 例子

### 预收

```
#!/bin/bash
# pre-receive
while read OLD NEW REF
do
    if ! [[ $REF =~ '/master'$ ]] ; then echo "Pushing non-master branch $REF, aborting."
    exit 1
    fi
done 
```

Enter fullscreen mode Exit fullscreen mode

### 后期接收

```
#!/bin/bash
# post-receive

ROOT=/home/.../app-folder
VENV=/home/.../virtual-environment-folder
export GIT_WORK_TREE=$ROOT
PIP=0
DB=0

read OLD NEW REF        # only master was accepted, no need to iterate

git checkout $NEW -f

if git diff --name-only $OLD $NEW | grep -q 'requirements.txt'; then
    $PIP=1
    source $VENV/bin/activate
    pip install -qr $ROOT/requirements.txt
    echo "Packages upgraded"
else echo "No package changes detected"
fi

if git diff --name-only $OLD $NEW | grep -q 'app/migrations/versions'; then
    $DB=1
    source $VENV/bin/activate
    python3 $ROOT/manage.py db upgrade
    echo "Database upgraded"
else echo "No database change detected"
fi AUTHOR=`git show $NEW -s --format="%an"`
TIME=`date -Is`
echo "$TIME,$AUTHOR,$OLD,$NEW,$PIP,$DB" >> $ROOT/updates.log

$ROOT/apache2/bin/restart 
```

Enter fullscreen mode Exit fullscreen mode