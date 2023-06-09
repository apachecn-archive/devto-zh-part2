# WSL 提示:在 Windows 登录时启动 Linux 后台服务

> 原文：<https://dev.to/ironfroggy/wsl-tips-starting-linux-background-services-on-windows-login-3o98>

*注意:我从我的博客上交叉发布这篇文章，来试探一下开发者*

越来越多的开发人员发现 Linux 的 Windows 子系统(WSL)非常棒。能够在 Windows 上透明地使用大量已建立的*nix 工具，可以使您的机器成为用户和开发人员的最佳混合体。

WSL 的更新包括在所有 Linux 控制台窗口关闭时增加对后台任务的支持，而以前 WSL 进程必须在一个活动的 shell 下运行。但是，您仍然需要自己启动这些服务，因为 Linux 会话只能显式启动。没有启动配置来启动 Postgresql 或 RabbitMQ 之类的服务。

在几次重启之后，我忽略了手动启动这些服务，我想要一个解决方案，当我启动我的机器时，让 Postgres 在 WSL 层的后台自动运行。

以下是如何在 Windows 登录时启动 WSL 后台服务:

首先，你需要一个启动脚本。我调用我的`start_postgresql.sh`放在`~/.local/bin/`里。我所做的只是启动 Postgres 服务，但是您可以启动任意多的其他服务或者执行任意多的其他启动时操作。

```
mkdir -p ~/.local/bin/
echo "service postgresql start" >> ~/.local/bin/start_postgresql.sh
chmod +x ~/.local/bin/start_postgresql.sh 
```

Enter fullscreen mode Exit fullscreen mode

当然，运营服务需要特权。但是，由于我们需要在没有交互的情况下在启动时运行它，并且我不想启用无密码`sudo`,我们该怎么办？

原来你可以为特定的命令启用无密码`sudo`,而不启用允许你的用户`sudo`做任何事情而不进行任何安全检查的安全错误。打开 visudo 编辑您的`sudo`配置:

```
sudo visudo 
```

Enter fullscreen mode Exit fullscreen mode

然后添加这一行来启用`sudo`只运行这个脚本。

```
calvin ALL=(root) NOPASSWD: /home/calvin/.local/bin/start_postgresql.sh 
```

Enter fullscreen mode Exit fullscreen mode

太好了！当然，你的名字可能不像我的“卡尔文”。把它改成你自己的用户名。

剩下要做的就是让 Windows 在您登录时在 WSL 层运行这个脚本。我们可以使用任务计划程序来做到这一点，所以打开开始菜单，键入“任务计划程序”找到应用程序并运行它。

现在，单击左侧的“任务调度程序库”,然后单击右侧的“创建任务…”来创建一个新任务，您可以通过以下步骤配置来运行您的脚本:

*   你可以给任务起任何名字，比如“开始 Postgres”
*   在“触发器”选项卡下，单击“新建…”为该任务添加新的触发器
*   在“开始任务”下拉列表中选择“登录时”
*   选择“任何用户”
*   在“操作”选项卡下，单击“新建…”为此任务添加新操作
*   选择“启动程序”作为动作类型，然后输入`C:\Windows\System32\bash.exe`作为要运行的程序
*   最后，作为最后也是最重要的一步，在“添加参数(可选)”中，设置这个参数字符串来运行命令:`-c "sudo ~/.local/bin/start_postgresql.sh"`

这就是你所需要的。当你登录到 Windows 下一步这个任务将被触发，并运行您的自定义脚本作为根和启动 Postgres 和任何其他服务，你决定旋转。

希望这将使用 Linux 工具在 Windows 上开发比 WSL 已经做的更容易！