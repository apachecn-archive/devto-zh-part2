# 升级到 Angular 6！

> 原文：<https://dev.to/chiangs/upgrading-to-angular-6-309p>

[![](img/7f0b42af35193aaf10906f291bb8ef08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mWMpY7k_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tk3tdc05xhqdgi8e4z7h.jpg)

## 这么有棱角的 6 就 out 了！它带来了许多令人兴奋的东西，新的工具和更简单的方法来管理依赖关系的更新。

[以下是官方博客更新](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4)

[这是来自 ngConf 的关于 Angular 6 的最新消息](https://blog.angular.io/trip-report-from-ng-conf-2018-1e8654053205)

Angular CLI(现在也更新到版本 6 以匹配 Angular)将通过使用 **ng update** 使项目更新到 Angular 的未来版本变得更加简单。

我一直在更新我所有的项目，从 Angular 4 或 5.2 到 6，只是为了看看这个过程是如何进行的，它并不顺利，但到目前为止，它都解决了。

你应该做的第一件事是检查[检查角度更新清单](https://update.angular.io/)。

升级只是几个简单的步骤，但是您可能会遇到一些问题，所以我将只讨论我遇到的一些问题。

首先，我必须用
全局更新 Angular CLI

```
> npm install -g @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

在这里你可以运行`ng -v`，你应该看到:

```
 _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/

Angular CLI: 6.0.0
Node: 9.11.1
OS: darwin x64
Angular:
...

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.6.0
@angular-devkit/core         0.6.0
@angular-devkit/schematics   0.6.0
@schematics/angular          0.6.0
@schematics/update           0.6.0
rxjs                         6.1.0
typescript                   2.7.2 
```

Enter fullscreen mode Exit fullscreen mode

然后进入项目根文件夹，在那里更新 CLI:

```
> ng update @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

然后更新角芯包:

```
> ng update @angular/core 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是有棱角的材料，一定要更新它:

```
> ng update @angular/material 
```

Enter fullscreen mode Exit fullscreen mode

当你还在你的项目的根目录下的时候，检查一下情况如何:

```
> ng -v 

should see: 

     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/

Angular CLI: 6.0.0
Node: 9.11.1
OS: darwin x64
Angular:
...

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.6.0
@angular-devkit/core         0.6.0
@angular-devkit/schematics   0.6.0
@schematics/angular          0.6.0
@schematics/update           0.6.0
rxjs                         6.1.0
typescript                   2.7.2 
```

Enter fullscreen mode Exit fullscreen mode

这里有几次在升级各种项目时，一些包有`<error>`而不是版本，所以我只是重新运行了更新脚本。

### **RxJS 6 突破性变化**

清单提供了 RxJs 6 的安装脚本，但无论我怎么尝试，它都不起作用。

有了 Angular 6，我们得到了最新的 RxJs 6，它包含了对一些导入路径的突破性改变。因此，如果你想让旧的导入仍然工作，或者不能让这部分更新工作，那么为了使升级你的应用程序更简单，你可以安装下面的兼容性模块:

```
> npm i rxjs-compat 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，更新相对来说并不痛苦，但是在我的小项目中立即看到效果是很有趣的。例如，它们都没有显示 loading spinner，因为这些项目是自举的，并且提供得非常快。

享受每一个人…我知道我的周末被毁了，因为我将在 Angular 上忙得不可开交！

***快速提示*** :记得在开始更新过程之前将最新的本地更改推送到 repo，以便在更新出错时可以恢复。