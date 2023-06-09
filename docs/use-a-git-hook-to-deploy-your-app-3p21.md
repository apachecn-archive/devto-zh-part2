# 使用 git-hook 部署您的应用程序

> 原文：<https://dev.to/aikedejongste/use-a-git-hook-to-deploy-your-app-3p21>

将应用部署到服务器的方法有很多，这里有一个我经常使用的简单方法。如果你不熟悉 Git，这可能会有点混乱，但是我保证这是最简单的方法！

它是这样工作的:您在服务器上创建一个空的 git 存储库，然后将您想要的分支从您的开发机器推送到这个存储库中。服务器上的存储库有一个小脚本(hook)，它将文件放在正确的目录中(“rails_project”)，并运行所有的包命令。您最终得到两个目录:空的存储库和项目的“检出”。

### 1。在服务器上创建空存储库

使用要用来运行应用程序的用户帐户登录到服务器，并在主目录中创建空存储库。

```
mkdir bare-repository cd bare-repository git init --bare 
```

或者作为一句俏皮话:

```
mkdir bare-repository && cd $_ && git init --bare 
```

你不能像对待一个普通的存储库那样对待一个空的存储库。但是你可以推它，让它自动在服务器上运行钩子。这对于这个设置来说已经足够了。

我们还需要一个用于结帐的目录，让我们也创建它。我使用一个通用的名称来结帐，这样我就不必在每台服务器上更改我的脚本。我喜欢‘rails _ project’或者只是‘check out’。

```
mkdir ~/rails_project 
```

### 2。安装提交挂钩(脚本)

应用程序不会在没有存储库目录的情况下运行。这就是我们把代码推到的地方。post-receive 钩子将把代码从空存储库签出到‘rails _ project’目录。

使用“cd”返回到您的主目录，并编辑接收后挂钩:

```
nano bare-repository/hooks/post-receive 
```

我们需要添加一个命令来完成结帐并为我们运行 bundle install。我们稍后会添加更多功能。

```
GIT_WORK_TREE=~/rails_project 
git checkout -f cd ~/rails_project 
bundle install --without development test 
```

这将把裸存储库中的代码签出到目录‘rails _ project’中，并在其中运行 bundle install。您机器上的 git 客户机将向您显示这个脚本在通常的通知之后生成的输出。

并给这个 post-receive 钩子足够的运行权限:

```
chmod a+x bare-repository/hooks/post-receive 
```

### 3。让我们加上遥控器，按点什么

要将服务器上的空存储库作为远程存储库添加到您的本地 git 存储库，请使用:

```
git remote add newserver rails@test.runrails.com:bare-repository 
```

您必须在您的开发机器上运行这个命令，而不是在服务器上。“新服务器”只是遥控器的名字，你可以随便叫它什么。我经常有‘登台’或者‘制作’。

并将主分支推送到服务器。同样，newserver 是远程服务器的名称，master 是您想要推送的分支的名称。

```
git push newserver master 
```

您将在您的终端或 Git 客户机中看到 post-receive 钩子的输出。如果服务器出现问题，要再次推送相同的代码，请创建一个空的 commit 并再次推送。

```
git commit --allow-empty 
git push newserver master 
```

### 4。改进和故障排除

如果您使用 RVM，捆绑安装部分将无法工作。您必须像这样使用 rvm 包包装器:

```
/usr/local/rvm/gems/ruby-2.4.0/wrappers/bundle install --without development test 
```

在这种部署方法中，没有像 capistrano 那样的回滚选项，但是可以很容易地使用:
将数据库备份添加到脚本中

```
pg_dump your_db_production > ~/pgdump_before_deploy.sql 
```

您可以使用 ssh 登录并手动迁移您的数据库，但是将它添加到 post-receive 挂钩要容易得多。资产也是如此:预编译

```
bundle exec rake db:migrate bundle exec rake assets:precompile 
```

如果您使用 Passenger:
，轻触 restart.txt 自动重启您的 rails 应用程序

```
touch ~/rails_project/tmp/restart.txt 
```

如果您的脚本生成大量输出，您可能希望添加一个错误通知，以防出错。将该块添加到钩子的顶部:

```
#!/bin/bash exit_with_error() { echo "[DEPLOY] !!!!!!!!!!!!!!!!!!!! An error has occurred !!!!!!!!!!!!!!!!!!!!!!!" exit 1 } 
```

并在您希望得到通知的每个命令后添加 **||** exit_with_error。比如:

```
bundle exec rake assets:precompile || exit_with_error 
```

仅此而已。如果你对这个方法有任何疑问或者发现任何错误，请告诉我。你可以给我发电子邮件到[info@runrails.com](//mailto:info@runrails.com)或者发微博到 [@runrails.com](https://twitter.com/runrails)

帖子[使用 git-hook 部署你的应用](https://www.runrails.com/deploying/use-a-git-hook-to-deploy-your-app/)首先出现在 [RunRails](https://www.runrails.com) 上。照片由 Harvey Enrile 在 Unsplash 上拍摄。