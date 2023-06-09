# 什么是 MVC 模式？

> 原文：<https://dev.to/ashok6266/what-is-a-mvc-pattern-3k85>

# MVC -模型视图控制器

模型视图控制器(mvc)是当今生产中使用的设计模式。
**模型**——它包含了管理数据库的代码。例如，您可以看到类似 INSERT INTO tasks(id，title，description，creation_date，edition_date)
值(' 55 '，' " $_POST['title']" '，' " $_POST['task']" '，' " $_POST['date']" '，' " $_POST['edit_date']")")")。
**视图**——从名字本身就暗示它是视图部分，通常包含 HTML 代码。有时它有 PHP 变量和条件。例如，如果我们想要显示一个用户的帖子。

**控制器** -控制器有所有的决策代码，否则逻辑部分在这里。它是模型和视图之间的中介。例如:如果用户请求一个页面，控制器从模型中调用方法，并将其返回给视图。

对于初学者，我创建了一个 mvc 的目录结构，你可以用 git 克隆或者下载 zip 文件，你可以解压到你要创建 mvc 应用程序的目录中

## 使用 git 命令克隆结构:

```
git clone https://github.com/ASHOK6266/myOwnStructure_mvc.git 
```

Enter fullscreen mode Exit fullscreen mode

我还在结构中添加了数据库文件，这是一种单例模式

一旦你克隆了这个结构，你会在 Models/Application/db.php
中看到这个数据库文件。数据库连接是一个单例模式，它一次又一次地限制了类的实例化。此外，单例模式使你的应用程序更快。不要忘记在 db.php 文件中添加您的数据库名、用户名和密码

**步骤 0:**
我创建了名为 myOwnStructure_mvc 的数据库和一个包含 id、标题和描述的 todolist 表。
**第一步:**
Models/Application/app . PHP
我创建了类任务和方法 get _ task()；方法 get_task()将从数据库中检索所有的文章。
**第二步:**
Controllers/Application/AppController . PHP
我创建了类 app controller 和方法 show _ tasks
方法 show_tasks 将从 Models/Application/App.php 中调用模型(即方法 get _ task)

不要忘记在这里包含模型文件 include('../../Models/Application/app . PHP ')；
**第三步:**
Views/Application/app . PHP
我刚刚在这里调用了控制器方法，也就是显示任务。通常视图中不应该有逻辑和安全性。我也在每个文件里都加了注释。可以借鉴一下。我刚刚给出了基本的文档。您可以开始构建 todolist 应用程序。
如果您希望构建除待办事项之外的应用程序。你可以替换代码。

### 附加信息

是啊！！！！！我们已经学完了基础知识，但是还有很多东西要学。
项目中的索引文件是应用程序的入口点。为了使它成为入口点，您已经配置了您服务器。
。htaccess 是运行在 web 应用程序上的 web 服务器的配置文件。