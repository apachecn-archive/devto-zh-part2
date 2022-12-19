# 在 APP_INITIALIZER 中加载应用程序配置

> 原文：<https://dev.to/prestonjlamb/loading-app-configuration-in-the-appinitializer-304h>

如果你和我一样，你已经把 Angular 应用程序的配置放在了`environment.ts`文件中，然后把这个文件复制到了`environment.test.ts`和`environment.prod.ts`文件中。这还不算太糟。这些价值观中的大部分不会经常改变。但有时确实如此。当他们这样做的时候，痛苦就开始了。

对我来说，事情是这样的:

1.  该应用程序在我的本地开发环境中运行良好。
2.  我构建应用程序并部署到测试环境中。
3.  应用程序启动，但不按预期运行。
4.  我更改了一个环境值，重新构建了应用程序，然后重新部署。
5.  应用程序启动，但不按预期运行。
6.  我更改了一个环境值，重新构建了应用程序，然后重新部署。
7.  应用程序启动，但不按预期运行。
8.  我更改了一个环境值，重新构建了应用程序，然后重新部署。
9.  ...

你明白了。这是一个接一个的小改动，然后是部署，看起来并不能解决问题。每个构建/部署过程需要 5 到 10 分钟。这意味着浪费了大量时间。问题是:大多数配置值在构建时不需要存在。它们可以在应用程序运行时设置。因此，`environment.ts`文件是*而不是*存放这些配置值的地方。

很长一段时间我都在忍受这个，因为这是我所知道的全部。但是几周前，我在听 Angular podcast 中的[冒险，他们在谈论存储你的应用程序配置的合适位置。我兴奋极了！这就是我需要的！他们正在和一个叫戴夫·布什的人谈话，他写了这个关于这个话题的](https://devchat.tv/adv-in-angular/aia-203-where-to-store-angular-configurations-with-dave-bush/)[博客帖子](https://davembush.github.io/where-to-store-angular-configurations/)。我觉得我终于明白了我需要做什么来有效地将运行时配置值引入到我的应用程序中。

我开始在我的应用程序中实现这一点，但我没有走太远，就有点被难住了。我找不到任何我想做的事情的完整代码示例。最后，在阅读了关于这个主题的其他几篇文章后(包括[这篇](https://robinraju.github.io/developer/2017-11-14-loading-external-config-in-angular/)和[这篇](https://juristr.com/blog/2018/01/ng-app-runtime-config/)，在上面已经提到的那篇之上)，我得到了一个加载配置文件并将其提供给整个应用程序的服务。我把[这个 StackBlitz 例子](https://stackblitz.com/edit/app-config-in-initializer)放在一起，展示你需要的代码的基础。下面是这些步骤的简要概述。

## 代码审查

要加载配置，需要在应用程序中添加一些东西。首先是`app.module.ts`。在`@NgModule`装饰器上添加下面的函数:

```
export function initConfig(appConfigService: AppConfigService) {
    return () => appConfigService.loadConfig();
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数简单地获取`AppConfigService`(我们一会儿就会看到)并从服务中返回一个调用`loadConfig`函数的函数。这里有一个非常重要的部分让我困扰了几天:_ **不要忘记`export`这个函数！我没有导出那个函数，我也不知道为什么，但是它打乱了应用程序的构建。本质上，它不会产生错误，但是它不会构建任何懒惰加载的模块。当我添加了`export`之后，一切都如预期的那样构建并运行了。所以不要像我一样浪费时间。😃**

应用程序需要添加的第二个东西是一个服务，我称之为`AppConfigService`。在某种程度上，该服务如下所示:

```
constructor(private _http: HttpClient) {}

public loadConfig() {
    return this._http.get('./assets/config/config.json')
        .toPromise()
        .then((config: any) => {
            this.config = config;
            this.configSubject$.next(this.config);
        })
        .catch((err: any) => {
            console.error(err);
        })
}

public getConfig() {
    return this.config;
} 
```

Enter fullscreen mode Exit fullscreen mode

`HttpClient`是加载文件所必需的。此外，`APP_INITIALIZER`令牌需要一个承诺被返回的函数，所以在一个可观察对象上使用`toPromise`方法可以很好地工作。另外需要指出的是正在使用的`configSubject$`变量。我这样做的原因是一些服务在文件加载完成之前就已经加载了。因此，让他们订阅该主题并等待其价值最终对我来说非常有效。

对我来说，另一个导致问题的原因是没有显式地将函数标记为 public。我不确定这是否是在`APP_INITIALIZER`工厂中使用服务的结果，或者是什么，但是一旦我明确地将变量和函数标记为 public，事情似乎就像预期的那样工作了。

好了，现在让我们再次跳回到`app.module.ts`文件。在`@NgModule`装饰器的`providers`数组中，添加以下内容:

```
{
    provide: APP_INITIALIZER,
    useFactory: initConfig,
    deps: [AppConfigService],
    multi: true
} 
```

Enter fullscreen mode Exit fullscreen mode

这告诉应用程序使用`APP_INITIALIZER`令牌，给它一个工厂函数来使用，提供所需的依赖关系，最后允许在应用程序中使用多个`APP_INITIALIZER`。

完成这些设置后，您就可以开始在整个应用程序中使用服务中的配置对象了。在服务的构造函数中，您可以使用`getConfig`方法来获取配置对象，也可以使用 subject 并订阅它，等待配置被加载。我在上面提到过，但是一些服务在`APP_INITIALIZER`运行之前就已经被加载了。但并不是每次都这样。因此，由于这个原因，我在需要使用这个配置对象的每个服务/模块中检查了多次，并确定我是否需要这个主题。

现在，需要采取最后一个步骤来使这个配置真正运行，那就是在每个环境中提供一个不同的`config.json`文件副本。在我的例子中，我们构建了我们的应用程序，并制作了一个 Docker 映像来运行应用程序([参见本文了解更多信息](https://dev.to/blog/dockerizing-angular))。因此，为了在不重新构建应用程序的情况下将文件放入其中，我在`docker-compose.yml`文件中添加了一个卷，如下所示:`./config.json:/usr/share/nginx/html/assets/config/config.json`。这允许我在服务器上创建一个`config.json`文件，然后用新的配置值将其复制到应用程序中。如果您将应用程序克隆到您的服务器上，构建并运行应用程序，而不将它放入 Docker 容器中，您也可以做到同样的事情。

希望这能回答你们中一些人的一些问题。花了几天时间，大量的阅读和测试，才把所有这些东西组合在一起，但我对最终产品真的很满意。这将使我们的应用程序的许多配置选项变得更容易、更快。我们不再需要经历做出改变、创建 PR、构建新映像和部署的过程。现在只需对 JSON 文件进行简单的修改，并重启 Docker 容器。如果这能帮到你，我很乐意收到你的来信！伸出手让我知道！

## 更新

要轻松实现这里讨论的功能，使用这个包: [runtime-config-loader](https://www.npmjs.com/package/runtime-config-loader) 。它只需要将提供者添加到您的`app.module.ts`文件中，就像上面描述的那样。