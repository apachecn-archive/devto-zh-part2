# 如何为 Composer 创建 PHP 包

> 原文：<https://dev.to/whoisryosuke/how-to-create-a-php-package-for-composer-g31>

最近，我一直在尽力上传尽可能多的代码到开源库，比如 [**Github**](http://github.com/whoisryosuke) 和 [**Codepen**](http://codepen.io/whoisryosuke) 。当我可以从我的 Github 中`git clone`一个样板文件，或者从 CodePen 中 *copypasta* 一个 CSS/JS 片段时，这使得引导新项目变得更加简单。但是，当我需要将一个库或模块包含到几个项目中时，会发生什么呢？克隆和复制粘贴只是不要在这一点上削减它。

切到**[**作曲**](http://getcomposer.com) (或者真的[包装家](http://packagist.com)。NPM 是一个使用 Node 的 Javascript 包管理器，Composer 对 PHP 也是如此。**

 **今天我们将通过 Packagist 向 Composer 提交一个 PHP“包”,这样我们就可以使用`composer require`将我们的包安装到任何项目中！

## 简单的步骤

这就像 4 个步骤一样简单(*减去用 PHP* 编写可重用包的部分)——我也很惊讶。确保在开始之前，您已经在项目中创建了一个 git 存储库并提交了代码。还需要在 [Github](http://github.com) 和[packagest](http://packagist)上有账号。

1.  在您的项目中创建一个`composer.json`:

```
{  "name":  "your-brand-name/your-project",  "type":  "library",  "description":  "Your package description goes here",  "keywords":  ["relevant","tags","go","here"],  "homepage":  "https://yourcompany.com",  "license":  "MIT",  "authors":  [  {  "name":  "Jordi Boggiano",  "email":  "j.boggiano@seld.be",  "homepage":  "http://seld.be",  "role":  "Developer"  }  ],  } 
```

Enter fullscreen mode Exit fullscreen mode

1.  [在你的 Github 上创建一个新的存储库](https://github.com/new)并将你的本地 git(如果你还没有的话，初始化)推送到远程 Github repo。

2.  [将您的包裹提交给包装商](https://packagist.org/packages/submit)

3.  搞定了。您的软件包应该是在线的，并且 Packagist 应该提供一个带有您的项目名称(例如`composer require username/package-name`)的示例 Composer require

> 您应该可以在:`https://packagist.org/packages/your-username/package-name`实时查看您的包裹

## 这么容易，没有借口

我尝试应用的编程最大原则之一是**干**，*不要重复自己*。当您的代码可能已经托管在 Github 上时，将您的代码放在一个受版本控制的地方只需要两个步骤。

与依赖`git clone`或子模块相比，使用 composer 依赖项对您的代码库来说更有效。

## 拿我的代码来试运行

我在 Packagist 上发布的包是为使用 Metrc API 的开发人员发送和接收大麻监管数据准备的。它基本上是一个 API 的包装器，使用一个 Guzzle 客户端与之接口(使用您的授权凭证)。

如果您是 Metrc 开发人员，或者只是对使用代码感兴趣，您可以使用以下方法将它添加到您的项目中:

`composer require kushy/metrc-php-sdk`

希望对你有所帮助**