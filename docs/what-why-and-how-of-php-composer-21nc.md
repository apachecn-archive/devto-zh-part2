# PHP Composer 是什么，为什么和如何

> 原文：<https://dev.to/gauravmak/what-why-and-how-of-php-composer-21nc>

### 让我们揭开 Laravel 和其他框架使用的包管理器的神秘面纱

由于开发人员已经意识到了 DRY 方法的力量和好处，许多框架和库已经被私人开发和开源。

如果你和我一样(准确地说是两年前的我)，你可能会说——“为什么所有这些新框架都不允许直接。zip 下载？为什么我必须安装(并学习)这个新的 Composer 东西，只是为了下载一个框架？”

回到我使用 [Codeigniter](https://codeigniter.com/) 和 [CakePHP](https://cakephp.org/) 2 的时候，我会直接下载。zip 文件，解压到我的项目目录下，开始开发。太棒了，对吧？请继续阅读。

然后，我在 2015 年见证了新版本 CakePHP 3(太倒霉了，那次没有尝试 Laravel)。它迫使我使用 composer 来下载框架，而我对它非常陌生。我像其他人一样抱怨，直到我意识到这将为我节省多少时间和头痛。

## 它是什么作曲家？

> Composer 是 PHP 的依赖管理器和自动加载专家。

你听说过或者用过 [npm](https://www.npmjs.com/) 吗？或者如果你是一个 Rubyist，Bundler 对你来说一定不陌生。 [Composer](https://getcomposer.org/) 为 PHP 世界提供了类似的功能。早期曾经是 PEAR (PHP 扩展和应用程序库)。

这里需要注意的一件重要事情是，开源包通常依赖于 [Packagist](https://packagist.org/) 并且 composer 从那里下载依赖项，除非您指定其他位置。反过来，Packagist 保存并更新来自 Git/Svn 库的代码。您还可以通过使用私有 packagist 或自己托管它们来将私有存储库与 composer 一起使用。

我讨厌理论。让我们进行一些实际的谈话。

## 作曲家修复的痛苦

以下是作曲家减轻的一些痛苦:

### 依赖关系管理

[![Dependency Management](img/a88e79b9d4e8bbab44e34f8cd44e73f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dbAgbEnt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/hji5jgwp0cexdgv5p1e7.png)

当您使用框架处理一个新项目时，您依赖于它的更新。当他们发布包含错误修复和新特性的新版本时，出于安全、性能和其他原因，您应该保持框架更新。

但是，每次都要手动下载，替换现有文件并再次测试，这很痛苦。这需要时间。这就是为什么有这么多旧的代码库依赖于不再受支持的框架版本。从事遗留项目是我们所有人都试图避免的事情，不是吗？

### 项目目录大小

[![Project directory size](img/f6e39effd4a8ddb0b712aa38b89ab84e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OFQl09nd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/9q0pj4ofo41xtfqw31v8.png)

在 composer 出现之前，项目目录的大小曾经非常大。我们将携带我们的项目所依赖的所有库，并通过 u 盘或污染 VCS (git)历史来传递项目。

输入 Composer -大小减少到不到 20%？我们可以不保留项目中的依赖关系。gitignore)并借助于`composer.lock`文件避免版本不匹配。

> 可悲的事实- Codeigniter 3 仍然使用传统的下载系统。

### 小时的调试

您的项目所使用的库或框架依赖于一些其他的库(并且这个链条还在继续...).可能，框架正在使用一个库的 X 版本，而另一个库正在使用同一个库的 Y 版本。

由于一些冲突或不兼容，你已经得到了一些错误，必须投入大量的时间来找到问题的根源。没有更多的- composer 只保留一个具有合适版本的库的副本，或者拒绝安装请求。

### 自动加载

还记得那一长串`require`语句吗？随着项目规模的增长，我们需要在每个文件中包含的文件数量也在增长。PHP 自动加载解决了这个问题，但是随着依赖性的增加，设置和维护相对来说还是很难。较少讨论的 Composer 的强大功能是自动加载。它支持 PSR-0， [PSR-4](https://www.php-fig.org/psr/psr-4/) ，类映射以及文件自动加载。我们只需要一个文件就可以了。使用简单的名称空间，享受开发时间。

> 鲜为人知的 fact - Composer 提供三种不同级别的[自动装载机优化](https://getcomposer.org/doc/articles/autoloader-optimization.md)来满足您不同级别的需求。

### 冗长的安装程序

[![Lengthy Installation procedures](img/29462a3943b029f0a64cbd4f825dbc53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Re2rrVeh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/f0nph5equt1mjrcf4pu4.png)

通常，在下载库进行设置和安装后，需要遵循一个或多个步骤。软件包维护人员必须在文档中编写详细的安装过程，并且在用户不遵循这些过程并抱怨错误时仍然要回答支持查询。作曲家的剧本对他们来说是一个福音。

人们可以很容易地对这些步骤进行编码，这些步骤可以在包下载的各个阶段作为脚本自动运行。例如，Laravel 使用 composer 脚本创建一个`.env`文件，创建一个应用程序密钥，并执行包的[自动发现](https://medium.com/@taylorotwell/package-auto-discovery-in-laravel-5-5-ea9e3ab20518)。

### 平台要求

你的库/包的代码可能只支持最新的 PHP 版本或者依赖于特定的 PHP 扩展。您可以将此情况通知 Composer，从而阻止不符合要求的系统上的软件包下载。例如，

```
"require": {
    "php": "^7.1.3",
    "ext-mbstring": "*",
    "ext-openssl": "*",
    ...
}, 
```

Enter fullscreen mode Exit fullscreen mode

将这个添加到您的`composer.json`中，当 PHP 低于 v7.1.3 或者没有 mbstring 和 openssl 扩展的系统试图下载这个包时，composer 将抛出一个错误。

### 维护一个库/包

[![Maintaining a library package](img/f3bfb1f6e2d2326d8215d32bb1189375.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZOzt6ftQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/8dhurgoixvmwntcte225.png)

每当有重要的更新时，包的维护者要么为包的每个版本维护不同的目录，要么通过 GitHub 发布新的版本。由于 Composer 与 VCS 标记和分支的完美集成，我们只需要在提交和推送中添加一个标记。剩下的都搞定了。

Composer 强烈推荐[语义版本化](https://semver.org/spec/v2.0.0.html)，然后，包维护者可以专注于实际的开发，而不用担心发行版。

太棒了。我不再有任何抱怨，并准备好投入其中。接下来呢？

## 作曲者的具体细节

现在我们知道了作曲家的作品和原因。让我们跳到如何做的部分:

### 安装

composer 文档上的安装说明非常清楚，我认为我最好的工作是[将您链接到那里](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx)。

### 消费

如果想用 Laravel 构建项目，需要用 composer 安装。虽然有些人想走传统的道路，但这是非常不鼓励的。

Laravel 本身使用了很多有用的开源包，如 PHPUnit、Monolog、 [Carbon](http://carbon.nesbot.com/docs/) 等。这些依赖项也由 composer 管理。您应该了解版本管理，以避免潜在的问题。

> Fun Fact - Composer 默认在`vendor`目录下安装依赖项，但是它也支持[自定义安装程序](https://getcomposer.org/doc/faqs/how-do-i-install-a-package-to-a-custom-path-for-my-framework.md)，用于各种可以在其他目录下安装依赖项的包类型。

### 版本管理

了解 composer 如何基于您的`composer.json`文件中指定的版本约束下载库将对您有很大帮助。我建议你至少浏览一遍[原始文档](https://getcomposer.org/doc/articles/versions.md)文章。

> 你知道吗？-您可以通过包的版本、分支或
> 甚至通过特定的提交 SHA(不推荐)来要求包。

我个人的建议是，对于使用语义版本化和通配符版本范围的包，使用 Caret 版本范围(^)。*)为其他人，因为他们很容易掌握。

### 了解 composer.lock 文件

而当您使用`create-project`命令或者当您直接从终端请求一个包时,`composer.json`是自动管理的。`composer.lock`是你应该注意的事情。

首先，您[应该提交](https://getcomposer.org/doc/01-basic-usage.md#commit-your-composer-lock-file-to-version-control)您的 composer.lock 文件，以确保您的每个团队成员在处理一个公共项目时都使用相同的依赖副本。这是怎么发生的？

无论何时运行`composer install`，Composer 都会检查 composer.lock 文件是否存在。如果存在，它将按照包含确切版本号和要使用的 sha 的锁文件安装所有依赖项。如果锁文件不存在，Composer 只需读取`composer.json`文件，安装依赖项并创建`composer.lock`文件。下次您在服务器或其他计算机上运行 install 命令时，锁定文件将为您提供完全相同的副本。

另一方面，`composer update`命令直接查看`composer.json`文件，根据版本约束安装新版本的依赖项(如果可用)并更新锁文件。

### 作曲家的缓慢

[![Slowness of Composer](img/47c186c1a034472f4396b7f724f94d14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MBwrOw8I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/3m5tzwvuq1vd2rahdn3h.png)

我经历过 composer 缓慢的安装和更新。不知道后台发生了什么，我只是一直盯着屏幕，等待它完成，然后才能采取下一步行动。

如果你面临同样的问题，我建议你在 composer 命令后面加上`--profile`和`-vv`或者甚至`-vvv`，以获得更多关于后台处理的信息。例如，

```
composer install -vv --profile 
```

Enter fullscreen mode Exit fullscreen mode

如果您无法从输出中找到任何特定的问题，您也可以尝试[这个包](https://github.com/hirak/prestissimo)来启用并行下载。

### 包开发

如果您刚刚开始构建您的第一个包/库，有一个非常有用的技巧可以让开发变得简单和舒适。

大多数情况下，在开发期间，需要针对实际项目测试包/库，为此，维护人员可能需要将包保存在实际项目中，以避免不必要的多次发布，直到他完成。

composer[Path repositories](https://getcomposer.org/doc/05-repositories.md#path)可以将你从那个陷阱中解救出来。它将符号链接或镜像项目中的回购协议，您可以保持代码库独立，并轻松地管理和切换它们。

Laravel 超级工匠 Caleb Porzio 甚至[添加了一个 bash 别名](http://calebporzio.com/bash-alias-composer-link-use-local-folders-as-composer-dependancies/)来通过一个命令使用这个特性。

> 快速提示——如果你的软件包包含你想传递给用户的命令行脚本，考虑使用[供应商二进制文件](https://getcomposer.org/doc/articles/vendor-binaries.md)。PHPUnit 用它来快速让你开火`vendor/bin/phpunit`。

### 分支锯齿

一旦您的包/库已经被多个项目使用，您也应该考虑添加分支别名，以使开发人员的生活稍微轻松一些。[这篇文章](https://getcomposer.org/doc/articles/aliases.md)很好的解释了背后的原因，也具体说明了怎么做。

> 高级开发者- Composer 允许你通过使用 [composer-plugins](https://getcomposer.org/doc/articles/plugins.md) 来改变或扩展它的功能。您可以在加载包和触发某些事件时执行操作。

## 期末笔记

这个有点长。我们讲述了依赖项管理的快速历史，了解了 composer 是什么，讨论了它所解决的问题，然后浏览了 Composer 为新开发人员和软件包维护人员提供的一些有用的要点和提示。

这差不多就是我要说的。如果你是从顶部一路读到这里的，请阅读每一行，谢谢你。我觉得我们的思维方式很匹配。在下面的评论区问好。

如果您有任何问题，请随时发表您的意见，我会尽力解决您的疑问。

如果你认为这可以帮助你的在线网络中的其他开发者，我很乐意看到你与他们分享这个。谢了。