# Angular +科尔多瓦

> 原文：<https://dev.to/jspizziri/angular-cordova-1nej>

[![](img/22e83a2fddcc2aafafc568a675eb1a39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mGzboPwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jspizziri.cimg/banner-angularjs.jpg)

让我们在不使用 Ionic 或 NativeScript 的情况下，将新的 angular 项目移植到 Cordova。**为什么？**因为那些框架有时候可能太固执己见，约束太多。到本文结束时，您将拥有一个功能正常的 angular/cordova 项目，它将尽可能与 cordova 松散耦合。您将能够遵循 angular 风格惯例，并安装您想要的任何插件、框架或库。(例如 bootstrap、ng-bootstrap、pug 等。)

首先，我想[赞扬这篇文章](https://www.becompany.ch/en/blog/2016/10/19/creating-apache-cordova-app-with-angular2),因为它让我朝着正确的方向前进，然而我发现它有点不完整。

你可以在这里找到演示源代码

* * *

好的。让我们开始吧。

* * *

### 1。设置一个普通的角度应用程序

首先我们要用下面的命令创建一个名为`hello-cordova`的普通 angular 6 项目:`ng new hello-cordova`。

此时你可能想做的一些事情是安装和配置像`bootstrap` & `ng-bootstrap`或任何其他你感兴趣的 angular 库。

### 2。基本科尔多瓦设置

现在我们已经有了一个基本的 angular 应用程序，我们将创建一些基本的脚手架来设置我们的 cordova 构建。

首先让我们确保安装了 Cordova CLI:`npm install -g cordova`。

现在让我们创建一个基本的 cordova 应用程序。我们将在`cordova`子目录中创建 cordova 应用程序，并给它一个 id`com.hellocordova.www`和一个名称`Hello Cordova`。

```
cordova create cordova com.hellocordova.www "Hello Cordova" 
```

现在让我们添加一些 git 跟踪文件来忽略一些构建文件并保留目录:

```
touch cordova/.gitignore
touch cordova/www/.gitkeep 
```

并将以下内容添加到`cordova/.gitignore` :

```
node_modules
www/*
!www/.gitkeep
platforms
plugins 
```

最后，我们来补充一下 iOS 平台。要添加平台(和插件)，我们需要确保我们在`cordova`目录中，然后运行以下命令:

```
cordova platform add ios 
```

### 3。添加脚本

在我们项目的**根**目录中(不是在`cordova`文件夹中的那个)，让我们添加一些基本脚本到`package.json`来自动化一些事情:

```
"clean": "rm -rf dist cordova/www/* && touch cordova/www/.gitkeep",
"build:cordova": "npm run clean && ng build --base-href=. --output-path=cordova/www",
"cordova:prepare": "cd cordova && rm -rf platforms plugins node_modules && npm ci && cordova prepare && cd ..",
"cordova:build:ios": "npm run build:cordova && cd cordova && cordova build ios && cd ..",
"cordova:emulate:ios": "npm run cordova:build:ios && cd cordova && cordova emulate ios && cd ..",
"open:xcode": "open $(find ./cordova/platforms/ios -name '*.xcworkspace')" 
```

我们还将修改`build`脚本如下:

```
"build": "ng build --base-href=/" 
```

这将为您提供以下实用程序:

*   其他开发者应该能够运行这个脚本来初始化他们环境中的 Cordova 应用。
*   `npm run open:xcode` -运行这个来轻松地在 Xcode 中打开项目
*   `npm run cordova:build:ios` -运行此命令来重建 iOS 项目
*   `npm run cordova:emulate:ios` -运行这个来运行项目的 iOS 模拟器

### 4。性能改进(400 毫秒延迟)

是的。现在让我们去掉臭名昭著的 400 毫秒延迟,这是 iOS 默认的 webview 点击延迟。我们将把 [FastClick.js](https://github.com/ftlabs/fastclick) 添加到我们的 angular 应用程序中。

```
$ npm i fastclick 
```

将以下内容添加到您的`src/main.ts` :

```
import * as FastClick from 'fastclick';

// attach FastClick.js
FastClick.attach(document.body); 
```

**注意:**应该将它添加到 main.ts 中尽可能靠近顶部的位置，这样它可以立即执行。

### 5。正在初始化插件

现在，如果你想安装其他插件，你需要配置它们。这可以在你的`app.component.ts` :
的`ngOnInit()`中完成

```
// src/app/app.component.ts
import { Component, OnInit } from '@angular/core';
import { CordovaService } from './service/cordova.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  title = 'app';

  constructor(protected _cordovaService: CordovaService) {}

  public ngOnInit(): void {
    this._cordovaService.initialize();
  }
}

// src/app/service/cordova.service.ts
import { Injectable } from '@angular/core';

@Injectable()
export class CordovaService {

  protected hasBegunInitialization: boolean = false;

  constructor() {}

  public initialize(): void {
    // listen for 'deviceready' event in case it hasn't
    // already been fired during application bootstrap
    document.addEventListener('deviceready', () => {
      window['isDeviceReady'] = true;
      this.init();
      console.log('deviceready fired after bootstrap finish');
    }, false);

    // check if 'deviceready' has already fired during
    // application bootstrap.
    if (window['isDeviceReady'] === true) {
      this.init();
      console.log('deviceready fired before bootstrap finish');
    }
  }

  protected init(): void {

    // lock initialization
    if (this.hasBegunInitialization) {
      return;
    }

    this.hasBegunInitialization = true;

    // do init stuff here
  }
} 
```

确保为`CordovaService`类添加了一个提供者。

### 故障排除

*   `Error: Cannot read property 'replace' of undefined`运行`cd platforms/ios/cordova && npm install ios-sim`

## - [图像/资产没有出现？](https://stackoverflow.com/questions/42830752/ionic-2-images-not-displaying-on-device)

死亡白屏？

*   cordova 不喜欢`<base href="/"></base>`，而是使用`<base href="."></base>`来发布 Cordova。这通常不是你想要的网络分布，因为它会扰乱你的路由。
*   如果您遇到这个问题，请查看添加脚本部分，并查看`--base-href`标志。