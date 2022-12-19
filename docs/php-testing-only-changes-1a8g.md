# PHP:测试只改变

> 原文：<https://dev.to/mykeels/php-testing-only-changes-1a8g>

[![changes](../Images/7bb044e21dfaa9d354769c1e470a9967.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZBDjZgs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mausatf.com/wp-content/uploads/2015/05/change_sign1.jpg)T3】

<center><small><u>Ref: [USATF Mid-Atlantic](https://mausatf.com/background-screening-process-changes/)</u></small></center>

我最近发现这个很棒😍工具，它让我只对自上次提交以来修改过的文件运行测试，就像我在[Jest 框架](https://jestjs.io)中所熟悉的。

因为我找不到用 PHPUnit Watcher 或 PHPUnit 本身来做这件事的方法，而且它对我来说用了几个小块，所以我决定分享一下。

> 如果你知道怎么做，请在评论中告诉我。

### 拦截器

这个包的 PHPUnit 依赖项被标记为`^6.0`，所以它不能在我的项目上工作，我的项目有一个被标记为`^7.0`的依赖项。

### 修罗

我在这里做了一个分叉[，我在我的项目中使用了它的`dev-master`分支，将它添加到`composer.json`中，比如:](https://github.com/mykeels/WhatsChanged) 

```
 "repositories":  [  {  "type":  "vcs",  "url":  "https://github.com/mykeels/WhatsChanged"  }  ],  "require-dev":  {  "icyapril/whats-changed":  "dev-master"  } 
```

Enter fullscreen mode Exit fullscreen mode

这使得 composer [从我的 GitHub fork](https://stackoverflow.com/a/13500676/7396801) 解析这个包，而不是使用官方的包。

### 用法

现在，当运行`./vendor/bin/whatschanged`时，我得到:

```
tests/Feature/AuthTest.php     OK (3 tests, 6 assertions)
tests/Feature/IntentTest.php   OK (5 tests, 21 assertions) 
```

Enter fullscreen mode Exit fullscreen mode

而不是

[![image](../Images/db73cb03537722fe794ac764a2fb8660.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ORtr8xS5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11996508/42683545-85d8f218-8685-11e8-8762-82ec753a8497.png)

🎉💃编码快乐！