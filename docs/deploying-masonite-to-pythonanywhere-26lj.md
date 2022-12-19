# 将 Masonite 部署到 PythonAnywhere

> 原文：<https://dev.to/masonite/deploying-masonite-to-pythonanywhere-26lj>

# 简介

[PythonAnywhere](https://pythonanywhere.com) 是一种直接从浏览器开发和托管您的网站或任何其他代码的方式，无需安装软件或管理您自己的服务器。如果您想部署一个 WSGI 应用程序，这是一个非常好的选择，它从一开始就支持 Django 和 Flask。

在本文中，我将介绍如何在 PythonAnywhere 实例上设置应用程序。

# 创建账户

这个很简单:)。前往 [PythonAnywhere](https://pythonanywhere.com) 创建一个免费或付费账户。你想要哪个都行。

# 仪表盘

登录后，您将看到一个控制面板。在它的左下角，您会看到一个类似这样的部分:

[![](../Images/7edad9d27aa82a714c413596840a4e8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KLAiW_Gr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s0azh89y9w1mcrn4x5ne.png)

继续点击`$bash`链接。您将看到一个新的 bash 终端。在这里，您应该 pip 安装 masonite-cli，这样我们就可以开始使用 craft 命令了。

```
$ pip3 install masonite-cli --user 
```

Enter fullscreen mode Exit fullscreen mode

确保使用了`--user`标志，因为在这个 bash shell 中没有 sudo 特权。

# 设置目录

让我们继续创建一些我们将在这个项目中使用的目录。我们可能希望 PythonAnywhere 实例能够服务于多个应用程序，所以让我们创建一个“站点”目录。

```
~ $ mkdir /var/www/sites
~ $ cd /var/www/sites
/var/www/sites $ <we should now be here> 
```

Enter fullscreen mode Exit fullscreen mode

我们制作一个网站目录，因为这可能包含一个结构，我们可以把多个网站像:

```
/sites
  masonite/
  tutorial/
  blog/ 
```

Enter fullscreen mode Exit fullscreen mode

# 安装泥瓦匠

如果你只是想安装一个全新的应用程序，那么你可以按照正常的[文档来安装一个新的应用程序](https://masoniteproject.com)。但是对于本教程，我们将讨论如何直接从 github 安装应用程序。

为了方便起见，我们实际上将为 Masonite 安装一个新的应用程序，但是通过 github 库而不是`craft new`命令。

所以去你的 github 账户复制 github 链接。在这种情况下，我们将使用:

```
https://github.com/MasoniteFramework/masonite 
```

Enter fullscreen mode Exit fullscreen mode

确保您在站点目录中，并 git 克隆您的 repo:

```
/var/www/sites $ git clone https://github.com/MasoniteFramework/masonite
Cloning into 'masonite'...
remote: Counting objects: 1898, done.
remote: Compressing objects: 100% (57/57), done.
...
... 
```

Enter fullscreen mode Exit fullscreen mode

太好了！所以现在我们的存储库被克隆了。执行一个快速的`ls`命令应该会返回一个包含我们的新 repo 的新文件:

```
/var/www/sites $ ls masonite 
```

Enter fullscreen mode Exit fullscreen mode

# 虚拟环境

PythonAnywhere 提倡将您的依赖项放在虚拟环境中的想法(这是一个好的实践)。让我们来设置一下虚拟环境，这真的很简单。

```
/var/www/sites $ cd masonite
/var/www/sites/masonite $ virtualenv -p python3 venv 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们对新创建的 masonite 项目做了一个`cd`操作，并创建了一个虚拟环境。这个命令使用的是`python3`(`virtualenv`默认为 Python 2.7，Masonite 需要 3.4+)。最后的`venv`是我们虚拟环境的名字。随便你怎么命名。

# 安装依赖项

太好了。现在让我们安装我们的依赖项。首先，我们需要激活我们的虚拟环境，然后像平常一样运行`craft install`命令。

```
/var/www/sites/masonite $ source venv/bin/activate
(venv) /var/www/sites/masonite $ craft install Collecting waitress==1.1.0 (from -r requirements.txt (line 1))
  Using cached https://files.pythonhosted.org/packages/ee/af/ac32a716d64e56561ee9c23ce45ee2865d7ac4e0678b737d2f5ee49b5fd6/waitress-1.1.0-py2.py3-non
e-any.whl
Collecting masonite<=2.0.99,>=2.0 (from -r requirements.txt (line 2))
Collecting python-dotenv==0.8.2 (from masonite<=2.0.99,>=2.0->-r requirements.txt (line 2))
  Using cached https://files.pythonhosted.org/packages/85/9f/b76a51bb851fa25f7a162a16297f4473c67ec42dd55e4f7fc5b43913a606/python_dotenv-0.8.2-py2.py
3-none-any.whl
...
...
Key added to your .env file: 1KmdwFryf71PGKYm6NBoFXRHHlqqE0= 
```

Enter fullscreen mode Exit fullscreen mode

让这些依赖项安装。如果安装成功，你会得到一个绿色的消息，告诉你一个密钥被附加到你的`.env`文件中。太好了。现在让我们离开这个 bash shell，继续将我们的应用程序添加到 PythonAnywhere 仪表板中。

# 添加我们的 Web 应用程序

回到控制面板，在右上角我们会看到一个名为“Web”的选项卡，如下所示:

[![](../Images/a5cb33f621f72d8507cd58e217df352f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2yaAJ8n4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yvxy6gq6d8rm3o41qsqh.png)

点击它，然后点击“添加新的 Web 应用程序”:

[![](../Images/15dfcf224d015fb277c1062721266b00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tgOW0jSc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8jozaqrbyjd9my5qtpjc.png)

你需要点击列表底部的“手动配置”。

[![](../Images/ad6e31569b7f0e96a54d0253548582c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ttuofkpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrdnjenccknmz97v5q5n.png)

然后点击“Python 3.6”:

[![](../Images/8a9083e762f9399bdc4f999cb8cc92a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wrqBYExG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltz1m3qtbc6li0320c3n.png)

完成后，单击“下一步”,您应该会看到一个专门针对您的应用程序的新仪表板。

# 配置您的 Web 应用程序

## 代码段

如果您向下滚动这个新的 web 应用仪表板，您会看到一个“代码”部分:

[![](../Images/42a3b682c9e79b9db0e56491e76c0119.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7_tZdMqt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3lqw5hnxyj2mig0usdsq.png)

请记住，我们将应用程序放在一个`/var/www/sites/<your_project>`目录中，所以让我们将它添加到源代码和工作目录中，如上图所示。

现在我们需要编辑`wsgi.py`文件。我不完全确定他们为什么不默认使用源代码中的那个，但是很好。单击“Wsgi 配置文件”链接，这将打开一个编辑器。

我们必须将我们的`wsgi.py`文件复制并粘贴到这段代码中。因为我们使用 git，所以我们可以直接从 GitHub repo 中获取它。如果你没有修改你的`wsgi.py`文件，你可以在这里使用默认的:[https://github . com/masonite framework/masonite/blob/master/wsgi . py](https://github.com/MasoniteFramework/masonite/blob/master/wsgi.py)

最终产品应该是这样的:

[![](../Images/bc91ea8e8c8745f21034187151f784c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--22LNsz5_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tjbgnz8cgmsxerosaww0.png)

*确保点击右上角的保存*

## 虚拟章节

在 web 应用仪表板上向下滚动一点，您应该会看到一个新的虚拟环境部分。我们只需要告诉 PythonAnywhere 我们在哪里创建了我们的虚拟环境。记得我们刚刚在我们的项目目录中创建了一个`venv`目录。因此，让我们在已经添加的目录中添加一个`venv`:

[![](../Images/a9ba3daa529355f9dbcb96d9c1bd0b85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yU-hitFF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0j0dfmmlc984qktmdncl.png)

## 成功！

就是这样！现在让我们回到页面顶部，重新加载应用程序:

[![](../Images/de71a7cba7993f65f9fbaab9eb332ec1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zpkuobwe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9i8wcqsq3gmomn36wkex.png)

完成后，我们可以使用它上面的链接来查看它，该链接看起来像“. pythonanywhere.com”。这将打开我们的 web 应用程序！