# 仅在安装 PostgreSQL with Homebrew 第 2 部分后安装 pgAdmin

> 原文：<https://dev.to/letsbsocial1/installing-pgadmin-only-after-installing-postgresql-with-homebrew-part-2-4k44>

[![postgresql](img/d0a236745c4fc03b006a2fc5625f59fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gaI7Ff9D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lu26u1oaysf8cdfiiux.png)

这篇文章假设你已经在某个地方备份了你现有的数据库(如果你关心它们的话！).我不会跳过这些步骤。

本帖原载于我的[开发者博客](http://www.mariadcampbell.com/2018/06/17/installing-pgadmin-only-after-installing-postgresql-with-homebrew-part-2/)***2018 年 6 月 17 日*** 。

作为开发人员，了解您使用的工具与学习编程一样重要。如果您不知道如何正确使用它们，您的编程将会受到影响！

这让我以一种适合我需要的方式回到我的机器上与 PostgreSQL 的战斗中。

正如我在之前的帖子中提到的，我希望能够使用 **PostgreSQL** 并在**图形用户界面(GUI)** 中查看它，同时还拥有**自制软件**安装的所有功能。不幸的是，最初我笨拙地、不完美地完成了这个过程，在这个过程中犯了一些错误。事实上，其中一部分与 Mac OSX(T9)上的(相对较新的)**迁移助手**有关。当它被移植到我现在的机器上时，它搞砸了我的 PostgreSQL 安装，并且清除我从 postgresql.org 下载的所有痕迹是一个挑战。它包括在我的**数据**的**恢复**和**后续迁移**到我的**新计算机**期间，从**迁移助手**已经**创建的** ***的***中获取 **rid** ，从**安装**中获取 **rid**

#### 卸载 postgresql.org 安装

要卸载**【postgresql.org】***安装*** ，点击桌面上的`Macintosh HD`图标，进入**库文件夹**，然后进入 **PostgreSQL 文件夹**，该文件夹位于**库文件夹**内。在里面，你会看到一个叫做 **PostgreSQL 卸载程序**的东西。点击**从你的机器上卸载 PostgreSQL** 。最后，它会告诉你卸载完成，但你的数据没有被删除。您必须将 **PostgreSQL 文件夹**和剩余的内容移入垃圾箱。进行此操作时，系统会提示您输入您的**管理员密码**。当您这样做时，该文件夹将被移到废纸篓。**

#### 用自制软件安装 PostgreSQL

用**自制软件**安装 **PostgreSQL** 非常容易。当然，首先要确保你已经安装了**自制软件***！要检查您是否确实安装了 **Homebew** ，请在**终端**中键入以下命令:* 

```
brew update 
```

Enter fullscreen mode Exit fullscreen mode

如果这个命令返回结果，这意味着**家酿软件**已经安装在你的机器上。如果没有返回任何结果，则不返回。要安装**自制软件**，请访问**自制软件**网站:

[自制网站](https://brew.sh/)

一旦你安装了**自制软件**，做以下事情:

```
brew update 
```

Enter fullscreen mode Exit fullscreen mode

这只是确保**自制软件**是最新的。最好的做法是在用**自制软件**安装程序之前运行这个命令。接下来，运行下面的命令来安装 **PostgreSQL** :

```
brew install postgresql 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，运行以下命令启动并运行**PostgreSQL**:

```
brew services start postgresql 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将**启动**你的 **postgres 服务器**。不要大惊小怪！要让**停止**postgres 服务器，运行以下命令:

```
brew services stop postgresql 
```

Enter fullscreen mode Exit fullscreen mode

但是现在还不要这么做！T3】

运行以下命令:

```
psql 
```

Enter fullscreen mode Exit fullscreen mode

登录 **PostgreSQL** 。关于**自制**安装的美妙之处在于，你可以简单地使用你用来控制你的机器的 ***本机*** **终端**窗口，而不是随**postgresql.org**安装而来的 **Sql shell** 。这只是一个额外的痛苦步骤，需要花费更多的时间来执行。

我喜欢用我的**简称**来代表**当地发展**。但是我**T5 不能 T7】用它登录 **pgAdmin** ！默认情况下， **Homebrew** 只设置你用
创建的账户**

```
createdb `whoami` 
```

Enter fullscreen mode Exit fullscreen mode

命令。

#### 创建 pgAdmin 所需的默认 postgres 用户

您必须创建一个名为 **postgres** 的 ***第二用户*** ，以便能够连接并登录 **pgAdmin** 。要创建 **postgres** 用户，在**终端**中键入以下命令，同时**仍然登录**到 **postgres** :

```
createuser -s postgres 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 **postgres 超级用户**。然后输入下面的命令来确认这个用户已经被创建:

```
\du 
```

Enter fullscreen mode Exit fullscreen mode

该命令返回您在 **PostgreSQL** 安装中可用的用户列表。

为了使与 **pgAdmin** 的连接成为可能，该过程还有最后一步。你必须**为你刚刚创建的 **postgres 用户**创建一个密码**。 ***默认为*** ，**用户**是用我这里提供的命令**创建** ***而没有*** **密码**。为了**为 **postgres 用户**创建一个密码**，在仍然登录到 **PostgreSQL** 的同时运行以下命令:

```
ALTER USER  postgres  WITH PASSWORD ‘password’; 
```

Enter fullscreen mode Exit fullscreen mode

只要用你想要分配的密码替换`password`。

***然后*** ，你会想在你的机器上安装 **pgAdmin** 。前往 **pgAdmin** 网站下载 **GUI** :

[pgAdmin 网站](https://www.pgadmin.org/)

下载完成后，点击下载文件夹中的 **dmg** (磁盘镜像文件)打开应用程序并安装。将弹出一个带有 **PostgreSQL** 图标的窗口。**拖动图标**到你的**应用文件夹**。这将把它安装到您的**应用程序文件夹**中。

接下来，您要确保您使用您的 **postgres 用户**登录到 **postgres** ，以便能够**登录并连接**和 **pgAdmin** 。你是 ***不是*** **默认登录***进入**用户 postgres** 当你用**自制**安装 **postgres** 时。这是我一开始很困惑的地方。这让我觉得我无法通过**自制**安装来连接 **pgAdmin** 。你所要做的就是使用不同的命令登录到另一个用户:* 

```
psql -U postgres 
```

Enter fullscreen mode Exit fullscreen mode

`psql` ***默认*** 为您的**用户帐户名**。在这种情况下，就是你机器上的**用户账号** ***简称*** 。这里的命令要求使用-U(用户)postgres 登录。

#### 登录 pgAdmin

一旦你用用户 **postgres** 登录到 **postgres** ，进入你的应用文件夹，点击 **pgAdmin** 图标。它将在您最喜欢的浏览器中的一个新标签中打开。

加载完成后，点击**浏览器**下浏览器窗口左侧的**服务器**选项卡。一个名为 **PostgreSQL 10** (或者你机器上的任何版本)的新标签将会出现。点击那个。

现在会出现一个连接到服务器的模式，提示您输入您的**用户 postgres** 的密码，以便连接到服务器:

```
Please enter the password for the user 'postgres' to connect the server - "PostgreSQL 10"
Password 
```

Enter fullscreen mode Exit fullscreen mode

***现在你明白有一个叫 postgres 的用户有一个专用密码的重要性了吧！*T3】**

一旦你 ***提供*** 你的 **postgres 密码**和**点击 **OK** 按钮上的**，你将 ***登录*** 到 **pgAdmin** ，一切就绪！您甚至可以看到您所有的用户数据库以及您在此过程中可能创建的任何其他数据库！

***快乐数据库*** 与 **PostgreSQL** ！

[在 OSX 上完全卸载并重装 PSQL](https://medium.com/@bitadj/completely-uninstall-and-reinstall-psql-on-osx-551390904b86)

[pgAdmin.org](https://www.pgadmin.org/)

[psql: FATAL:角色“postgres”不存在](https://stackoverflow.com/questions/15301826/psql-fatal-role-postgres-does-not-exist)

[在 macOS 上卸载 Postgres 10–enterprise db 安装程序](https://dba.stackexchange.com/questions/185476/uninstall-postgres-10-on-macos-enterprisedb-installer)**