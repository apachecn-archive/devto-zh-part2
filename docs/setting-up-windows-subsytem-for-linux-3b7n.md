# 为 linux 设置 windows 子系统

> 原文：<https://dev.to/micahshute/setting-up-windows-subsytem-for-linux-3b7n>

相对来说，我在编程领域还是个新手，我决定记录一些新概念和其他花了我一段时间才弄明白的东西——这不仅是为了巩固我的知识，也是为了帮助其他可能试图学习或完成同样事情的人。

我使用各种不同的博客和资源建立了 Windows Subsystem for Linux (WSL ),这些博客和资源都有不同程度的过时。我没有遇到一个最近的，有我需要的一切，所以希望这可以为某个人的目的服务。

# 为开发用 Linux 设置 Windows 子系统

*不再需要为 Windows 子系统 Linux 启用开发人员模式来工作*

## 准备好窗户

1)确保你运行的是 Windows 10。Windows 子系统 Linux (WSL)是**而不是**在 windows 上自动启用的。因此，首先，我们需要启用它！

a)进入你的 Windows 搜索栏，输入`turn windows features on or off`

b)单击与您的查询相匹配的结果。它会在你的控制面板里。您应该会看到类似这样的内容:

[![toggle windows features](../Images/c867a4fac7d2db3e0f8f6ee2e7786a5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zaIeg_WE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k4x418deogy16bqva8ab.png)

c)向下滚动，直到您看到“Windows Subsystem for Linux”-确保其复选框被选中！

[![wsl checkbox](../Images/3dca3322a72480e77435825f5e948105.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mO_F4V4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2wkiufnn6iisl3h9dfm2.png)

2)重新启动计算机

3)通过前往`System Information`并检查您的版本来检查您的 Windows 10 版本。如果您的版本早于 16215，请跳过步骤 4，继续执行步骤 5。

[![windows version](../Images/9cd8a73561f7fce0bf743a9109b06585.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7HkdUoWV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6v6riw4zlxkcs52tiia1.png)

4)开辟微软商店。搜索“Ubuntu”

a)选择“Ubuntu”(我没有用 16.04 或 18.04 LTS)

[![ubuntu choice in microsoft store](../Images/faa928da6d623428e04a39474bb74c65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--24t_yLUo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7yp5rdmlaz4vbzbr15xa.png)

5)如果你遇到错误或者如果你有 Windows 10 的早期版本，请在此查看更多信息

6)打开 Ubuntu 应用程序。按照提示操作，包括输入用户名和密码。不要忘记你的密码！

注意:如果你在这一步得到一个错误提示`Windows Subsystem for Linux has no installed distributions. Distributions can be installed by visiting the Windows Store: https://aka.ms/wslstore Press any key to continue...`，尝试在你的服务桌面应用中手动启动或重启 LxssManager(只需在你的 windows 搜索栏中搜索`services`)。在`L`部分，可以右击`LxssManager`，选择`Restart`或`Start`。

## 开始配备新的 Linux 子系统:RVM / Ruby、git、NodeJS 和 Rails

1)一旦一切就绪，我们就可以开始设置我们的系统了！首先运行以下命令来下载我们的依赖项:

```
sudo apt-get update    
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev libpq-dev libgdbm-dev libncurses5-dev automake libtool bison gnupg postgresql postgresql-contrib 
```

Enter fullscreen mode Exit fullscreen mode

2)通过运行以下命令安装 RVM:

```
 gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB 
```

Enter fullscreen mode Exit fullscreen mode

然后

```
 \curl -sSL https://get.rvm.io | bash -s stable --ruby 
```

Enter fullscreen mode Exit fullscreen mode

最后

```
 source ~/.rvm/scripts/rvm 
```

Enter fullscreen mode Exit fullscreen mode

3)让我们安装 bundler gem:

```
 gem install bundler 
```

Enter fullscreen mode Exit fullscreen mode

4)并设置 git:

```
 git config --global color.ui true 
```

Enter fullscreen mode Exit fullscreen mode

对于接下来的几个命令，请确保输入您的具体信息:

```
 git config --global user.name "your_name"
    git config --global user.email "your_email@example.com" 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过 HTTPS 或 SSH 与您的远程存储库通信。如果你想使用 SSH(这意味着你不需要在每次推送前用你的 GitHub 用户名和密码进行认证)- [遵循这些指示。](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

5)现在让我们安装 NodeJS，然后安装 Rails，这样我们就可以做很多东西了！

*   首先，nodejs:

    ```
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
    sudo apt-get install -y nodejs 
    ```

*   接下来，Ruby on Rails:

    ```
    gem install rails 
    ```

## 选择你的编程编辑器:VSCode

您可以使用任何您想要的编辑器，但是我将介绍如何设置 VSCode。

1)在此下载 VSCode [。选择 Windows 下载，而不是 Linux 下载。任何必要的 Linux 下载都将通过 Ubuntu 终端完成，其他的都应该下载到你的 Windows 操作系统上。](https://code.visualstudio.com/download)

2)要在 VSCode 中配置您的 Ubuntu 终端:

*   打开 VSCode
*   通过`F1`或`Ctrl` + `Shift` + `p`打开命令面板
*   开始键入以下内容:`Terminal: Select Default Shell`直到您看到它。选择该选项。

    [![command palette](../Images/7e3bd06ea7005ceaf118a43033cae09f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BdfKC0CX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k2a2ft8ycroujlqxp7qj.png)

*   一旦你选择了它，一些选项将会弹出。您想要选择 WSL

    [![WSL Terminal](../Images/a91fbd0509ae3816e17878601c0ca54c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WjAAfzK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4dl6urgzipp4ah689dqf.png)

3)现在，你可以在 VSCode 中使用你的 Ubuntu 终端了。在“终端”下拉列表中选择“新终端”，或键入`Ctrl` + `Shift` +`以使用它！

4)附加用户设置:

*   您可以在用户设置中添加一些东西来定制和改进您的编程体验。最重要的是，我们可以让 VSCode 请客。erb 文件比如。html 文件，允许我们使用所有相同的快捷键，并获得相同的语法高亮。
*   要打开用户设置，再次使用`F1`或`Ctr` + `Shift` + `p`打开命令面板，并键入`Preferences: Open Settings (JSON)` ![Preferences - settings](../Images/adc8e922ada4b2fbc2a9f509f896fae5.png)
*   你会看到这样一个屏幕:![Preferences - settings](../Images/35eb2e060f7a51107072fc3162c39951.png)
*   要像 html 一样处理您的 erb 文件，请将以下内容添加到您的用户设置中(上面圈出的内容):

    ```
     "emmet.includeLanguages": {
            "erb": "html"
        } 
    ```

*   我喜欢的一些可选个人偏好:

    ```
     "window.zoomLevel": -1,
        "editor.acceptSuggestionOnEnter": false,
        "editor.fontSize": 11,
        "editor.fontFamily": "Hack, Consolas, 'Courier New', monospace", 
    ```

*   请注意，要使 Hack 字体可用，您必须下载它。你可以点击查看[。](https://sourcefoundry.org/hack/)

## 重要知识:基础使用

你的终端实际上正在访问一个运行在你的 Windows 操作系统中的 Ubuntu 虚拟机。如果你曾经在你的终端中``cd` `，你将被发送到你的 Linux 虚拟机的‘用户之家’。这也是你打开 Ubuntu 应用时的默认位置。实际上，您希望在正常的 Windows 文件空间内处理所有业务，而不是在 Linux VM 文件空间内。所以，你需要知道如何在你的终端上从你的 Ubuntu 文件系统导航到你的 Windows 文件系统。如果你了解系统是如何设置的，这就相当简单了，所以让我们快速地过一遍。``

 ``为了允许您访问 Linux 机器的功能，Windows 将 Ubuntu 操作系统和文件系统存储在其自己的文件系统中。你不应该通过你的 Windows 机器访问你的 Ubuntu 文件。你应该做的是把你正在做的所有东西保存在普通的 Windows 文件空间中，然后通过你的 Ubuntu 终端访问它。那么，你是怎么做到的呢？嗯，从 Ubuntu 的角度来看，它只是一台普通的 Linux 机器，但微软已经通过 Ubuntu 根目录下的`/mnt`目录在 Linux 虚拟机和你的 Windows 操作系统之间建立了一条通道。

下面是任何 Linux 文件系统的最初几个级别的大概情况:

[![linux file system](../Images/259ff1ba21f01268347c4b05babb14ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QTjT8r2A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4bua64p5b3buagyyspgs.png)

如您所见，`mnt`目录是为其他文件系统创建的，所以这是微软将您的 Linux 连接到您的 Windows 机器的地方:

[![linux file system](../Images/ffbc7eba1170fec684878aa2eb844ead.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4n_d0U9v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cfjnsjkfm0shewhzxiio.png)

因此，您可以通过键入以下命令从起点到达那里:

```
cd /mnt/c/users/your_windows_username 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
cd ../../mnt/c/users/your_windows_username 
```

Enter fullscreen mode Exit fullscreen mode

如果您将这两行中的任何一行作为`alias`放入。bashrc 文件(在`/home/your_linux_username/.bashrc`中找到)你可以快速进入你的 windows home 目录(在下面的例子中只需在你的终端中键入`home`):

```
alias home="cd /mnt/c/users/your_windows_username" 
```

Enter fullscreen mode Exit fullscreen mode

你可以很容易地改变你的。bashrc 文件，方法是导航到您的 Linux 主目录(只需运行`cd`)并键入:

```
nano .bashrc 
```

Enter fullscreen mode Exit fullscreen mode

你会想要备份你的。bashrc 文件，这样你就不会搞砸了。您可以通过使用以下命令复制备份来完成此操作

```
cp .bashrc .bashrc.bak 
```

Enter fullscreen mode Exit fullscreen mode

按照屏幕底部的指示，保存您在`nano`中修改的文件:点击`Ctrl` + `X`，然后点击`y`，然后点击`Enter`，保存您的更改。

如果您对这些命令感到不舒服，请查看参考资料中下面的基本 Linux 命令链接。

记住，把你所有的东西放在你的 windows 目录里！这样，您就可以通过 Linux 终端或 Windows 操作系统访问它，并且当您使用

```
code . 
```

Enter fullscreen mode Exit fullscreen mode

(如果您在 Linux 文件系统中，VSCode 将会打开，但不是在正确的位置)。

## PostgreSQL 数据库设置

设置 PostgreSQL 允许将简单的生产级数据库集成到 Rails 项目中。这使得在 Heroku 上只需要一点点设置就可以轻松托管。

1)首先，我们要[下载 PostgreSQL for Windows](https://www.postgresql.org/download/windows/)——下载交互式安装程序。
2)一旦下载并安装好所有东西，你就可以通过在你的终端上输入来验证操作是否正确

```
psql -p 5432 -h localhost -U postgres 
```

Enter fullscreen mode Exit fullscreen mode

验证连接后，您可以通过键入以下命令退出

```
\q 
```

Enter fullscreen mode Exit fullscreen mode

3)有几种方法可以完成接下来的几个步骤，但最简单的方法是使用 pgAdmin。打开 pgAdmin，这是 PostgreSQL 的图形用户界面。

[![PGAdmin in start](../Images/f278ced614869d47163c10765f64bb97.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QbbqHEHm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jfzek5lgz9hx0jmyp5yi.png)

*   这将在您的浏览器中打开 pgAdmin。通过右键单击“PostgreSQL”并选择“连接服务器”来启动服务器

[![PostgreSQL selection](../Images/4588e86a2e4b94c94c78113da85d0179.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ytRoCtH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pmjzy3vigf8vti6ydp5b.png)
[![PostgreSQL connect](../Images/5f04a836400f178699e4334256b80390.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--vwwRdlar--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fzj55nwefbnhfldekkzq.png)

*   输入您在下载过程中定义的密码
*   PostgreSQL 服务器正在运行，现在可以集成到您的项目中了。
*   您还需要设置一个新的用户/角色和密码，以便在您的 rails 应用程序中使用。确保在设置过程中为您的用户/角色提供所有可用的权限。
*   在设置过程中，确保在`General`选项卡中填写`Name`，在`Definition`选项卡中填写`Password`，并在`Privileges`选项卡中启用所有权限。

[![create user](../Images/e68d894453ef6809ab55a23ac628bf1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oz4Wmp8M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jvyrq5y7vt1gzel4mxma.png)

[![create user permissions](../Images/ec2a4635282b041fd58e3945a32be1ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZFTQZL8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/812erbbq4e73aia75v40.png)

**示例:将 POSTGRESQL 服务器连接到 RAILS 应用程序**

*   确保您的服务器已连接，并且角色已如上所述进行了配置。
*   确保您位于合适的目录中(在您的 Windows 文件系统空间中),并创建一个配置为使用 PostgreSQL 的 Rails 应用程序:

    rails new my-first-app-database = PostgreSQL

*   现在，让我们配置我们的`database.yml`文件，以允许我们的应用程序连接到我们的数据库。将以下内容添加到您的`config/database.yml`文件中的`development:`和`test:`下。您需要添加一个用户名和密码，该用户名和密码将与我们在 pgAdmin 中创建的角色相对应。

    ```
    host: localhost
    user: your-postgres-username
    password: your-postgres-usernames-password 
    ```

*   我们现在可以测试数据库和 rails 的互操作性，首先在我们的应用程序中设置一些架构:

    ```
    rails g scaffold Post title:string body:text 
    ```

*   现在创建并迁移数据库:

    ```
    rake db:create
    rake db:migrate 
    ```

*   如果这些失败，请确保您已经在开发和测试部分下添加了 database.yml 配置

*   现在，启动一个服务器:

    ```
    rails s 
    ```

*   如果你导航到`localhost:3000`，你应该会看到我们在轨道上！

[![on Rails](../Images/1d6f432cb77e1790eac71928d2273ec8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8CirmMdQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ccqm9f1z94jagsld82o4.png)

*   让我们通过“localhost:3000/posts”来测试我们的数据库

[![posts](../Images/949ea5bf85c5a59d75d3946b586a7248.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ttzA9vQQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/srkjlem5cr07d3m8jh0y.png)

*   现在只需跟随 crud 链接并尝试发布帖子！我们应该看到我们的数据库与我们的 rails 应用程序配合得非常好！

[![database works](../Images/65d264502b8af240ac2c8f6e8a289497.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EcB21GE5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eh5lv6c4t1owo1ua5onl.png)

*   希望一切都为你工作！现在您已经准备好使用 WSL 进行开发了！

* * *

## 资源

[微软文档:用于 Linux 的 Windows 子系统](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

[使用 GitHub](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

[设置 SSH 密钥基本 Linux 命令](https://maker.pro/linux/tutorial/basic-linux-commands-for-beginners)``