# 用节点和角度构建一个粗糙的 SPA

> 原文：<https://dev.to/oktadev/build-a-crud-y-spa-with-node-and-angular-lp>

即使在 Angular 6 发布之前，Angular 这些年也经历了一些变化。最大的变化是从 AngularJS (v1.x)到 Angular (v2+)的跳跃，其中包括许多突破性的语法变化，并使 TypeScript 取代 JavaScript 成为默认语言。TypeScript 实际上是 JavaScript 的超集，但它允许您拥有强类型的函数和变量，并且它将被编译成 JavaScript，以便它仍然可以在您的浏览器中运行。鉴于 Angular 和 Node 的流行，如果您在下一个项目中考虑这个堆栈，这不会令人吃惊。

今天，我将向您展示如何构建一个具有基本 CRUD 功能的安全单页应用程序。您将使用 [Okta 的 OpenID Connect (OIDC) API](https://developer.okta.com/docs/api/resources/oidc) 来处理认证。Okta 提供了一个简单易用的 [Angular SDK](https://github.com/okta/okta-oidc-js/tree/master/packages/okta-angular) 来让你快速启动并运行。在后端，我将向您展示如何使用 [Okta JWT 验证器](https://github.com/okta/okta-oidc-js/tree/master/packages/jwt-verifier)来确保用户在提供任何敏感内容之前得到正确的身份验证。

我们将在这个项目中使用 Angular 6，所以你可以感受一下一些变化和新功能(在我们的 [Angular 6:新增功能和为什么要升级中阅读更多信息)。岗位](https://dev.to/oktadev/angular-6-whats-new-and-why-upgrade-3d0l-temp-slug-9604950)。

我们开始吧！

## 打造你的 Angular 6 App

Angular 团队维护着一个奇妙的命令行界面，叫做 [Angular CLI](https://cli.angular.io/) ，让创建新的 Angular 应用变得轻而易举。它也有大量的蓝图来生成新的类、组件、服务等等。要使用`npm`安装它，请运行以下命令:

```
npm i -g @angular/cli@6.0.8 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:我包括了写作时使用的版本号，以帮助确保本教程在未来的工作。更新的版本可能仍然有效，但是一些调整可能是必要的。

现在您应该已经将 CLI 安装为一个名为`ng`的命令。要引导一个新的应用程序，请键入以下内容:

```
ng new okta-node-angular-example
cd okta-node-angular-example 
```

Enter fullscreen mode Exit fullscreen mode

Angular CLI 将在创建包含裸项目的文件夹后自动为您安装软件包。它还将为您初始化一个 git 存储库，初始提交准备就绪，因此您可以非常容易地开始跟踪变更。

要启动应用程序，请运行以下命令:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该能够在`http://localhost:4200`访问一个非常简单的默认应用程序。当您对代码进行更改时，页面将会自动刷新以显示最新的更改。

[![Angular CLI default homepage](img/6232533769c173edc80569a05c26c0e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8js4xrkE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/ng-homepage-ddbda9b0addc1b39ff05d07bc213c5d43aa91939a16f41672c02d36a9ddb8e09.png)

## 用素材 UI 创建基本主页

为了让事情看起来更好，而不需要写很多额外的 CSS，你可以使用一个 UI 框架。谷歌的 Angular 团队维护着 [Angular Material](https://material.angular.io/) ，这是 Angular 的一个伟大框架，实现了[谷歌的材料设计](https://material.io/)原则。

要添加角度材料所需的依赖项，运行以下命令:

```
npm i @angular/material@6.4.1 @angular/cdk@6.4.1 hammerjs@2.0.8 
```

Enter fullscreen mode Exit fullscreen mode

这里的想法是在页面顶部制作一个应用程序栏，用于导航。这将在整个应用程序中保持一致。将会改变的部分将会在下面，并且将会因页面而异。现在，创建一个非常基本的主页组件。

```
ng generate component home-page 
```

Enter fullscreen mode Exit fullscreen mode

这将创建几个新文件:一个用于组件的 TypeScript 逻辑，一个用于 CSS，一个用于 HTML 模板，一个用于测试组件。

为了保持这个超级简单，只需将模板更改为如下所示:

**src/app/home-page/home-page . component . html**T2】

```
<h1>Welcome Home!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

您可以让其他生成的文件保持不变。

在 Angular 中，你需要在你的应用模块中添加新的组件。这是用`HomePageComponent`自动完成的，但是你还需要添加一些来设置有角度的材质。

现在，只需添加工具栏模块和动画模块(下面的 diff 也向您展示了应该已经为您添加的`HomePageComponent`):

**src/app/app.module.ts**

```
@@ -1,14 +1,20 @@
 import { BrowserModule } from '@angular/platform-browser';
+import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
 import { NgModule } from '@angular/core';
+import { MatToolbarModule } from '@angular/material';

 import { AppComponent } from './app.component';
+import { HomePageComponent } from './home-page/home-page.component';

 @NgModule({
   declarations: [
- AppComponent
+ AppComponent,
+ HomePageComponent
   ],
   imports: [
- BrowserModule
+ BrowserModule,
+ BrowserAnimationsModule,
+ MatToolbarModule,
   ],
   providers: [],
   bootstrap: [AppComponent] 
```

Enter fullscreen mode Exit fullscreen mode

棱角分明的材料使用[锤子。JS](https://hammerjs.github.io/) 提供更好的触摸屏支持。您之前已经添加了依赖项，所以要将它添加到页面中，您需要做的就是在应用程序的入口脚本顶部导入它。

**src/main . ts**T2】

```
import 'hammerjs'; 
```

Enter fullscreen mode Exit fullscreen mode

对于 CSS，默认的入口点是`src/styles.css`，但是每个组件也有自己的 CSS 文件，用于特定于该组件的样式。要完成角度材料的设置并为页面设置一些合适的默认值，请添加以下样式:

**src/styles.css**

```
@import "~@angular/material/prebuilt-themes/indigo-pink.css";
@import "https://fonts.googleapis.com/icon?family=Material+Icons";

body {
  margin: 0;
  font-family: Roboto, sans-serif;
}

* {
  box-sizing: border-box;
} 
```

Enter fullscreen mode Exit fullscreen mode

我选择了`indigo-pink`，但是如果你想要一些不同的东西，还有几个其他的预置主题。以下是撰写本文时的其他预建选项:

*   `deeppurple-amber.css`
*   `pink-bluegrey.css`
*   `purple-green.css`

工具栏本身非常简单。继续重写`app`组件模板，如下所示:

**src/app/app . component . html**

```
<mat-toolbar color="primary">
  <span>{{ title }}</span>
</mat-toolbar>

<main>
  <app-home-page></app-home-page>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

目前，`main`只包含您创建的主页。稍后，您将使用路由器替换它，这样当 URL 改变时，它会在那里呈现不同的页面。

在您添加到`app`模块的`MatToolbarModule`中，已经定义了`mat-toolbar`组件。

要修复页面上的填充，请更改应用程序的 CSS，如下所示:

**src/app/app . component . CSS**

```
main {
  padding: 16px;
  width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

这应该是它得到一个基本的主页启动和运行。您的站点现在应该看起来像这样:

[![basic home page with material design](img/57c97200052d3eaa3e0a6ee8e1e5813b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WVm5PZbS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/basic-crud-homepage-7163c463a944ecdde482b33391a1b2609b07c73f4cc5339ec72cbc1983babdb2.png)

## 用 Okta 给你的 Node + Angular App 添加认证

如果没有安全的[身份管理](https://developer.okta.com/product/user-management/)，你永远不会把你的新应用发布到互联网上，对吗？好吧，Okta 使这比你可能习惯的更容易和更可伸缩。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。注册时，您将获得一个组织 URL，这将是您登录开发人员控制台的方式。登录开发者控制台后，导航到**应用**，然后点击**添加应用**。选择**单页 App** ，然后点击**下一步**。

由于从 Angular CLI 生成的应用程序默认运行在端口 4200 上，因此您应该将其设置为基本 URI 和登录重定向 URI。您的设置应该如下所示:

[![Create New Application settings](img/7f0176a8b3e664fbf32e43df2a39bd7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FzKkMSB5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/create-new-application-settings-0fcc92ff623498d1363f98678f3bde1ad0adcfc35cd0e8c6ec46e0412d8f24f5.png)

点击 **Done** 保存你的 app，然后复制你的**客户端 ID** 。

在项目中创建一个名为`src/environments/.env.js`的新文件。在其中，您应该添加两个变量:

*   *oktaOrgURL* :这将是你注册 Okta 时收到的组织 URL，看起来应该类似于`https://dev-123456.oktapreview.com`
*   *oktaClientId* :这是您在 Okta 开发人员控制台中创建新应用程序时收到的客户端 Id

稍后您还将在节点服务器中使用这个文件，它不会使用 TypeScript，所以请确保它使用了`module.exports`而不是 es6 `export`语法:

**src/environs/. env . js**

```
module.exports = {
  oktaOrgURL: '{yourOktaDomain}',
  oktaClientId: '{yourClientId}'
}; 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Angular CLI 将用于开发和生产的环境变量加载到两个单独的文件中，这两个文件存储在源代码管理中。为了将敏感信息置于源代码控制之外，并使其他人可以轻松地重用代码，您可以将这个新创建的文件导入到这两个文件中。通过将它添加到`.gitignore` :
来防止它被添加到 git

```
echo .env.js >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

现在将它添加到您的开发和生产环境中:

**src/environments/environment . ts**

```
import dotenvVariables from './.env.js';

export const environment = {
  production: false,
  ...dotenvVariables
}; 
```

Enter fullscreen mode Exit fullscreen mode

**src/environments/environment . prod . ts**T2】

```
import dotenvVariables from './.env.js';

export const environment = {
  production: true,
  ...dotenvVariables
}; 
```

Enter fullscreen mode Exit fullscreen mode

向 Angular 应用添加 Okta 认证的最简单方法是使用 [Okta 的 Angular SDK](https://github.com/okta/okta-oidc-js/tree/master/packages/okta-angular) 。它是为 RxJS 的旧版本编写的，所以您也需要添加`rxjs-compat`来允许它与旧的模块一起工作。

```
npm i @okta/okta-angular@1.0.1 rxjs-compat@6.2.2 
```

Enter fullscreen mode Exit fullscreen mode

我将向您展示如何创建一个 Post Manager。现在，让 Angular CLI 为您创建一个组件:

```
ng g c posts-manager 
```

Enter fullscreen mode Exit fullscreen mode

要设置 Okta Angular，您需要将模块导入到您的`app`模块中。您还需要为回调创建一个路由，所以现在也是为您的不同页面添加路由的好时机。您还需要添加`MatButtonModule`来在您的应用程序中创建按钮(和看起来像按钮的链接)。

**T2】**

```
import { Routes, RouterModule } from '@angular/router';
import {
  MatToolbarModule,
  MatButtonModule,
} from '@angular/material';
import { OktaAuthGuard, OktaAuthModule, OktaCallbackComponent } from '@okta/okta-angular';

import { environment } from '../environments/environment';
import { AuthGuard } from './auth.guard';
import { HomePageComponent } from './home-page/home-page.component';
import { PostsManagerComponent } from './posts-manager/posts-manager-component';

const oktaConfig = {
  issuer: `${environment.oktaOrgURL}/oauth2/default`,
  redirectUri: `${window.location.origin}/implicit/callback`,
  clientId: environment.oktaClientId,
};

const appRoutes: Routes = [
  {
    path: '',
    component: HomePageComponent,
  },
  {
    path: 'posts-manager',
    component: PostsManagerComponent,
    canActivate: [OktaAuthGuard],
  },
  {
    path: 'implicit/callback',
    component: OktaCallbackComponent,
  },
];

// Later on in the @NgModule decorator:

@NgModule({
  // ...
  imports: [
    // After the other imports already in the file...
    MatButtonModule,
    RouterModule.forRoot(appRoutes),
    OktaAuthModule.initAuth(oktaConfig),
  ],
  providers: [OktaAuthGuard],
  // ...
})
// ... 
```

Enter fullscreen mode Exit fullscreen mode

OktaAuthGuard 提供者会这样做，这样当你试图进入帖子管理器页面时，你将被发送到 Okta 进行身份验证。只有在安全认证的情况下，您才能加载页面。

您还需要以几种方式修改您的应用程序组件。对于工具栏，您需要添加一些导航链接和一个登录和退出应用程序的按钮。此外，不是总是显示主页组件，而是通过给路由器一个出口来给它处理。

**src/app/app . component . html**

```
<mat-toolbar color="primary">
  <span class="title">{{ title }}</span>

  <a mat-button routerLink="/">Home</a>
  <a mat-button routerLink="/posts-manager">Posts Manager</a>

  <span class="spacer"></span>

  <button *ngIf="!isAuthenticated" mat-button (click)="login()">Login</button>
  <button *ngIf="isAuthenticated" mat-button (click)="logout()">Logout</button>
</mat-toolbar>

<main>
  <router-outlet></router-outlet>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

现在在`app`组件的 CSS 文件的末尾添加一些样式，使登录按钮出现在最右边，并且在应用程序的标题和导航链接之间有一点空间:

**src/app/app . component . CSS**

```
.title {
  margin-right: 16px;
}

.spacer {
  flex: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，组件类实际上并不知道它是否被认证，所以模板中的`isAuthenticated`将总是为 falsy。也没有`login`或`logout`功能。要添加这些组件，请对您的`app`组件进行以下更改:

**src/app/app.component.ts**

```
@@ -1,10 +1,30 @@
-import { Component } from '@angular/core';
+import { Component, OnInit } from '@angular/core';
+import { OktaAuthService } from '@okta/okta-angular';

 @Component({
   selector: 'app-root',
   templateUrl: './app.component.html',
   styleUrls: ['./app.component.css']
 })
-export class AppComponent {
+export class AppComponent implements OnInit {
   title = 'My Angular App';
+ isAuthenticated: boolean;
+
+ constructor(public oktaAuth: OktaAuthService) {
+ this.oktaAuth.$authenticationState.subscribe(
+ (isAuthenticated: boolean) => this.isAuthenticated = isAuthenticated
+ );
+ }
+
+ async ngOnInit() {
+ this.isAuthenticated = await this.oktaAuth.isAuthenticated();
+ }
+
+ login() {
+ this.oktaAuth.loginRedirect();
+ }
+
+ logout() {
+ this.oktaAuth.logout();
+ }
 } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该可以通过 Okta 登录和退出了，并且只有通过身份验证后，您才能访问帖子管理器页面。当您单击登录按钮或尝试转到帖子管理器时，您将被重定向到您的 Okta 组织 URL 以处理身份验证。您可以使用在开发人员控制台中使用的相同凭据登录。

[![Okta Sign In](img/2fabdfe16e37cc17f93323c0bddd7f00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qcJzNboU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/okta-sign-in-9d686e86e4d63d4e5ceb12e5cd266f3224311a8ca59d1caacfb040c7dc7de5d0.png)

您的应用程序现在应该如下所示:

[![Homepage with navigation](img/5993826cebce564409cb642688e4463b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MKf014ab--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/homepage-with-nav-00824599aef6756b8b036ac61ac1b31d8b9423355a370c8d228913936c170ad5.png)

[![Blank posts manager](img/9a90388fa2b090eb44438ee739d38b94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sRHBKxAR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/blank-posts-manager-27f8c3816b090630dec1c7d35a3e46ee4a34a95f6d2c14136e1fb8b5602b1ac1.png)

## 添加一个后端 REST API 服务器

既然用户可以安全地进行身份验证，那么您可以构建 REST API 服务器来对 post 模型执行 CRUD 操作。此时，您需要向您的项目添加相当多的依赖项:

```
# dependencies
npm i @okta/jwt-verifier@0.0.12 body-parser@1.18.3 cors@2.8.4 epilogue@0.7.1 express@4.16.3 sequelize@4.38.0 sqlite@2.9.2

# dev dependencies (-D is short for --save-dev)
npm i -D npm-run-all@4.1.3 nodemon@1.18.3 
```

Enter fullscreen mode Exit fullscreen mode

在 src 目录下为服务器新建一个文件夹:

```
mkdir src/server 
```

Enter fullscreen mode Exit fullscreen mode

现在创建一个新文件`src/server/index.js`。为了简单起见，我们只使用一个文件，但是你可以在这个文件夹中有一个完整的文件子树。将它保存在一个单独的文件夹中可以让你观察这个子目录中的变化，并且只在这个文件发生变化时才重新加载服务器，而不是在任何时候`src`中的任何文件发生变化。我将发布整个文件，然后解释下面的一些关键部分。

**src/server/index.js**

```
const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const Sequelize = require('sequelize');
const epilogue = require('epilogue');
const OktaJwtVerifier = require('@okta/jwt-verifier');

const { oktaClientId, oktaOrgURL } = require('../environments/.env.js');

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: oktaClientId,
  issuer: `{yourOktaDomain}/oauth2/default`
});

const app = express();
app.use(cors());
app.use(bodyParser.json());

app.use(async (req, res, next) => {
  try {
    if (!req.headers.authorization)
      throw new Error('Authorization header is required');

    const accessToken = req.headers.authorization.trim().split(' ')[1];
    await oktaJwtVerifier.verifyAccessToken(accessToken);
    next();
  } catch (error) {
    next(error.message);
  }
});

const database = new Sequelize({
  dialect: 'sqlite',
  storage: './test.sqlite'
});

const Post = database.define('posts', {
  title: Sequelize.STRING,
  body: Sequelize.TEXT
});

epilogue.initialize({ app, sequelize: database });

epilogue.resource({
  model: Post,
  endpoints: ['/posts', '/posts/:id']
});

const port = process.env.SERVER_PORT || 4201;

database.sync().then(() => {
  app.listen(port, () => {
    console.log(`Listening on port ${port}`);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将使用您的 okta 凭证设置 JWT 验证器。

```
const { oktaClientId, oktaOrgURL } = require('../environments/.env.js');

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: oktaClientId,
  issuer: `{yourOktaDomain}/oauth2/default`
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将设置 HTTP 服务器并添加一些设置以允许跨源资源共享(CORS ),并将自动解析 JSON。

```
const app = express();
app.use(cors());
app.use(bodyParser.json()); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这里检查用户是否得到了正确的身份验证。首先，如果没有`Authorization`头，就抛出一个错误，这就是发送授权令牌的方式。令牌实际上会看起来像`Bearer aLongBase64String`。您希望将 Base 64 字符串传递给 Okta JWT 验证器，以检查用户是否经过了正确的身份验证。验证者将首先向发行者发送一个请求，以获取有效签名的列表，然后在本地检查令牌是否有效。在后续的请求中，这可以在本地完成，除非它发现一个还没有签名的声明。

如果一切正常，对`next()`的调用告诉 Express 继续处理请求。但是，如果声明无效，将会引发一个错误。然后，错误被传递到`next`中，告诉 Express 出错了。然后，Express 将向客户端发回一个错误，而不是继续。

```
app.use(async (req, res, next) => {
  try {
    if (!req.headers.authorization)
      throw new Error('Authorization header is required');

    const accessToken = req.headers.authorization.trim().split(' ')[1];
    await oktaJwtVerifier.verifyAccessToken(accessToken);
    next();
  } catch (error) {
    next(error.message);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是你设置序列的地方。这是创建数据库模型的一种快捷方式。您可以使用各种各样的数据库进行排序，但是在这里您可以使用 SQLite 快速启动并运行，而不需要任何其他依赖。

```
const database = new Sequelize({
  dialect: 'sqlite',
  storage: './test.sqlite'
});

const Post = database.define('posts', {
  title: Sequelize.STRING,
  body: Sequelize.TEXT
}); 
```

Enter fullscreen mode Exit fullscreen mode

尾声与序列式和快速式配合得很好。它像胶水一样将两者结合在一起，仅用几行代码就创建了一组 CRUD 端点。首先，用 Express 应用程序和 Sequelize 数据库模型初始化 Epilogue。接下来，您告诉它为`Post`模型创建您的端点:一个用于帖子列表，它将有`POST`和`GET`方法；一个用于单个帖子，将有`GET`、`PUT`和`DELETE`方法。

```
epilogue.initialize({ app, sequelize: database });

epilogue.resource({
  model: Post,
  endpoints: ['/posts', '/posts/:id']
}); 
```

Enter fullscreen mode Exit fullscreen mode

服务器的最后一部分是告诉 Express 开始监听 HTTP 请求的地方。您需要告诉 sequelize 初始化数据库，当它完成时，Express 就可以开始监听您决定的端口。默认情况下，由于 Angular 应用程序正在使用`4200`，我们将只添加一个，使其成为端口`4201`。

```
const port = process.env.SERVER_PORT || 4201;

database.sync().then(() => {
  app.listen(port, () => {
    console.log(`Listening on port ${port}`);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以对`package.json`做一些小的改动，让它更容易同时运行前端和后端。替换默认的`start`脚本并添加几个其他脚本，这样您的脚本部分看起来就像这样:

**package . JSON**T2】

```
 "scripts": {
    "ng": "ng",
    "start": "npm-run-all --parallel watch:server start:web",
    "start:web": "ng serve",
    "start:server": "node src/server",
    "watch:server": "nodemon --watch src/server src/server",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以简单地运行`npm start`，服务器和 Angular 应用程序将同时运行，并在相关更改发生时重新加载。如果出于某种原因需要更改端口，可以分别用`PORT`和`SERVER_PORT`环境变量来更改 Angular 应用程序的端口和服务器的端口。比如`PORT=8080 SERVER_PORT=8081 npm start`。

## 添加帖子管理员页面

现在你有了一个后端来管理你的文章，你可以通过添加另一个页面来链接前端。这将发送获取、创建、编辑和删除帖子的请求。它还会随每个请求一起发送所需的授权令牌，这样服务器就知道您是一个有效的用户。

有几个实用程序会派上用场，所以继续添加它们作为依赖项:

```
npm i lodash@4.17.10 moment@2.22.2 
```

Enter fullscreen mode Exit fullscreen mode

你还需要几个材料模块，以及一个带有 angular 的表单模块:

**src/app/app.module.ts**

```
@@ -2,9 +2,14 @@ import { BrowserModule } from '@angular/platform-browser';
 import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
 import { Routes, RouterModule } from '@angular/router';
 import { NgModule } from '@angular/core';
+import { FormsModule } from '@angular/forms';
 import {
   MatToolbarModule,
   MatButtonModule,
+ MatIconModule,
+ MatExpansionModule,
+ MatFormFieldModule,
+ MatInputModule,
 } from '@angular/material';
 import { OktaAuthModule, OktaCallbackComponent } from '@okta/okta-angular';

@@ -46,8 +51,14 @@ const appRoutes: Routes = [
     BrowserModule,
     BrowserAnimationsModule,

+ FormsModule,
+
     MatToolbarModule,
     MatButtonModule,
+ MatIconModule,
+ MatExpansionModule,
+ MatFormFieldModule,
+ MatInputModule,

     RouterModule.forRoot(appRoutes),
     OktaAuthModule.initAuth(oktaConfig), 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个发布类

在`posts-manager`文件夹中创建一个新文件来定义一篇文章应该是什么样子。`Post`类将包含一些数据以及一些帮助管理文章本身的函数。同样，我将向您展示完整的文件，然后详细解释每个部分:

**src/app/post-manager/post . ts**

```
import * as moment from 'moment';

import { PostsManagerComponent } from './posts-manager.component';

export interface PostData {
  id?: number;
  title?: string;
  body?: string;
  updatedAt?: string;
}

export class Post implements PostData {
  id: number;
  title: string;
  body: string;
  updatedAt: string;

  loading = false;
  open = false;

  constructor(private data: PostData, private manager: PostsManagerComponent) {
    Object.assign(this, this.data);
  }

  get isDirty(): boolean {
    return this.data.title !== this.title || this.data.body !== this.body;
  }

  get updatedAtString(): string {
    const { updatedAt } = this;
    return updatedAt ? `Updated ${moment(updatedAt).fromNow()}` : '';
  }

  serialize(data: Post | PostData = this) {
    const { id, title, body, updatedAt } = data;
    return { id, title, body, updatedAt };
  }

  toJSON() {
    return this.serialize();
  }

  reset() {
    Object.assign(this, this.serialize(this.data));
  }

  async save() {
    this.loading = true;

    const data = await this.manager.api.savePost(this);

    if (data) {
      Object.assign(this.data, data);
      this.reset();
    }

    this.loading = false;
  }

  async delete() {
    this.loading = true;

    if (await this.manager.api.deletePost(this)) {
      this.manager.posts.splice(this.manager.posts.indexOf(this), 1);
    }

    this.loading = false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 允许您定义接口或类型，以定义某些数据的外观。在这种情况下，所有的数据字段都是可选的(键末尾的`?`):在新的 post 中，这些值都不存在。

```
export interface PostData {
  id?: number;
  title?: string;
  body?: string;
  updatedAt?: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以确保一个类实现一个接口。这意味着您将得到一个错误，除非您正在创建的类具有接口中所需的字段。这也意味着如果某个东西正在期待`PostData`，那么`Post`也应该工作，因为它保证有相同的字段。

```
export class Post implements PostData {
  id: number;
  title: string;
  body: string;
  updatedAt: string;

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

呈现文章的模板将使用`open`来确定是否应该显示文章的细节，使用`loading`来确定是否应该禁用某些元素。

```
loading = false;
open = false; 
```

Enter fullscreen mode Exit fullscreen mode

Post 需要从 Post Manager 访问一些属性。首先，这允许您从 post 类本身删除一篇文章。此外，Post Manager 将注入一个连接到后端的服务。通过在构造函数中设置`private data: PostData`，你是说 Post Manager 应该传入一些数据，它将被分配给`this.data`(同样，Post Manager 应该传入自己，它将被分配给`this.manager`)。

`Object.assign`调用获取`data`上的值，并将它们分配给自身。最初，`this.title`应该和`this.data.title`一样。通过创建一个`isDirty`的 getter 函数，它允许你检查数据是否有任何改变，这样你就知道它是否需要被保存。

```
 constructor(private data: PostData, private manager: PostsManagerComponent) {
    Object.assign(this, this.data);
  }

  get isDirty(): boolean {
    return (
      this.data.title !== this.title ||
      this.data.body !== this.body
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

`updatedAt`值将只是一个机器可读的日期字符串。虽然看起来不太漂亮。您可以使用`moment`以一种更易于阅读的方式对其进行格式化。下面会给你类似`Updated a few seconds ago`或者`Updated 2 days ago`的字符串。

```
 get updatedAtString(): string {
    const { updatedAt } = this;
    return updatedAt ? `Updated ${moment(updatedAt).fromNow()}` : '';
  } 
```

Enter fullscreen mode Exit fullscreen mode

有几个地方你需要发送数据到后端，但是你不想发送一堆额外的信息。这里有一个函数，它将序列化你给它的数据，默认情况下，它只从自身获取数据。在`JSON.stringify`中自动调用`toJSON`函数，所以任何试图连载一篇文章的东西都不需要键入`Post.serialize()`——它会像魔法一样工作！

“Cancel”按钮将使用`reset`函数将 Post 上的属性更新回其初始值。

```
 serialize(data: Post | PostData = this) {
    const { id, title, body, updatedAt } = data;
    return { id, title, body, updatedAt };
  }

  toJSON() {
    return this.serialize();
  }

  reset() {
    Object.assign(this, this.serialize(this.data));
  } 
```

Enter fullscreen mode Exit fullscreen mode

`save`和`delete`功能是异步的。首先，它将文章标记为`loading`,以触发 UI 更改。然后，它向 API 发送保存或删除帖子的请求。一旦完成，它将`loading`设置回 false 以触发另一次 UI 更新。

如果`save`函数成功，它将使用从 REST API 返回的新数据更新`data`变量。然后它会重置自己，以确保数据与 Post 同步。

如果`delete`功能成功，帖子将从帖子管理器的帖子列表中删除。

```
 async save() {
    this.loading = true;

    const data = await this.manager.api.savePost(this);

    if (data) {
      Object.assign(this.data, data);
      this.reset();
    }

    this.loading = false;
  }

  async delete() {
    this.loading = true;

    if (await this.manager.api.deletePost(this)) {
      this.manager.posts.splice(this.manager.posts.indexOf(this), 1);
    }

    this.loading = false;
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建发布 API 服务

您的本地 API 将在`http://localhost:4201`托管。但是，如果您将它部署在生产环境中的另一台服务器上，这种情况可能会发生变化。现在，向您的环境文件添加一个`api`变量:

**src/environments/environment . ts**

```
@@ -6,6 +6,7 @@ import dotenvVariables from './.env.js';

 export const environment = {
   production: false,
+ api: 'http://localhost:4201',
   ...dotenvVariables,
 }; 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`posts-manager`文件夹中的`ng generate service PostAPI`使用 Angular CLI 创建新服务。这将创建几个文件。修改`post-api.service.ts`,如下所示:

**src/app/post-manager/post-API . service . ts**

```
import { Injectable } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';

import { environment } from '../../environments/environment';
import { Post } from './post';

@Injectable({
  providedIn: 'root'
})
export class PostAPIService {
  constructor(private oktaAuth: OktaAuthService) {}

  private async fetch(method: string, endpoint: string, body?: any) {
    try {
      const response = await fetch(`${environment.api}${endpoint}`, {
        method,
        body: body && JSON.stringify(body),
        headers: {
          'content-type': 'application/json',
          accept: 'application/json',
          authorization: `Bearer ${await this.oktaAuth.getAccessToken()}`
        }
      });
      return await response.json();
    } catch (error) {
      console.error(error);
    }
  }

  async getPosts() {
    return (await this.fetch('get', '/posts')) || [];
  }

  async savePost(post: Post) {
    return post.id
      ? this.fetch('put', `/posts/${post.id}`, post)
      : this.fetch('post', '/posts', post);
  }

  async deletePost(post: Post) {
    if (window.confirm(`Are you sure you want to delete "${post.title}"`)) {
      await this.fetch('delete', `/posts/${post.id}`);
      return true;
    }

    return false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`@Injectable`装饰器允许这个服务通过构造函数注入到一个组件中。

```
@Injectable({
  providedIn: 'root'
}) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您设置了一个简单的助手函数来向服务器发送请求。这使用了所有现代浏览器内置的`fetch`功能。助手接受一个`method`(例如`get`、`post`、`delete`)、一个`endpoint`(这里可以是`/posts`或类似`/posts/3`的特定帖子)和一个`body`(一些可选的 JSON 值，在本例中是帖子内容)。

因为这只是一个助手函数，应该只在这个服务内部使用，所以我们将函数设为`private`。

这还设置了一些头来告诉后端它发送的任何主体都将是 JSON 格式，并且它通过从 Okta 获取访问令牌来设置授权头。Okta 返回一个承诺，所以我们需要`await`响应。

```
private async fetch(method: string, endpoint: string, body?: any) {
  try {
    const response = await fetch(`${environment.api}${endpoint}`, {
      method,
      body: body && JSON.stringify(body),
      headers: {
        'content-type': 'application/json',
        accept: 'application/json',
        authorization: `Bearer ${await this.oktaAuth.getAccessToken()}`,
      },
    });
    return await response.json();
  } catch (error) {
    console.error(error);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

其他函数(`getPosts`、`savePost`和`deletePost`)使用`fetch`助手来访问 API。

`getPosts`函数确保返回一个空数组，以防读取错误(错误将被记录到控制台)。

如果给了`savePost`一个没有 ID 的帖子，这意味着这是一个新帖子，所以它向 REST API 发送一个`POST`请求。否则，它使用`PUT`来更新文章。

在实际删除帖子之前，`deletePost`会通过浏览器内置的`confirm`功能向用户发送消息。从用户体验的角度来看，这可能不是最好的方法，因为它阻塞了用户界面，但这是一种无需编写大量额外代码就能获得响应的快速而肮脏的方法。

```
 async getPosts() {
    return (await this.fetch('get', '/posts')) || [];
  }

  async savePost(post: Post) {
    return post.id
      ? this.fetch('put', `/posts/${post.id}`, post)
      : this.fetch('post', '/posts', post);
  }

  async deletePost(post: Post) {
    if (window.confirm(`Are you sure you want to delete "${post.title}"`)) {
      await this.fetch('delete', `/posts/${post.id}`);
      return true;
    }

    return false;
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 编写帖子管理器页面

现在，您应该拥有了创建帖子管理器所需的所有组件。在您的 Posts Manager 类中，您需要注入 API 服务来访问 API。当组件初始化时，它将获取一个帖子列表，并从这些列表中创建 Post 对象，然后将其设置为可以在模板中访问的公共值。

为了添加新文章，会有一个按钮，你可以点击。它需要一个`addPost`函数来创建新的帖子。在这种情况下，如果你已经在编辑一篇文章，就让它打开那篇文章，而不是创建另一篇新文章。你也可以确保文章被排序，最近的文章在最上面。

**src/app/posts-manager/posts-manager . component . ts**

```
import { Component, OnInit } from '@angular/core';
import { sortBy } from 'lodash';

import { Post } from './post';
import { PostAPIService } from './post-api.service';

@Component({
  selector: 'app-posts-manager',
  templateUrl: './posts-manager.component.html',
  styleUrls: ['./posts-manager.component.css']
})
export class PostsManagerComponent implements OnInit {
  posts: Post[] = [];

  constructor(public api: PostAPIService) {}

  async ngOnInit() {
    // Do the initial fetch of posts, and map them to Post objects
    this.posts = (await this.api.getPosts()).map(data => new Post(data, this));
  }

  // The add button will be disabled if you're already editing a new post and it's open
  get newIsOpen() {
    const newPost = this.posts.find(post => !post.id);
    return !!(newPost && newPost.open);
  }

  // If you're already editing a post, but it's closed, then trigger the UI to open it
  addPost() {
    let newPost = this.posts.find(post => !post.id);

    if (!newPost) {
      // Create a new, empty post and add it to the beginning of the list of posts
      newPost = new Post({}, this);
      this.posts.unshift(newPost);
    }

    newPost.open = true;
  }

  get sortedPosts() {
    return sortBy(this.posts, ['updatedAt']).reverse();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

模板有点复杂，所以我将解释各个部分。这是它完整的样子:

**src/app/posts-manager/posts-manager . component . html**

```
<h1>Posts Manager</h1>
<mat-accordion>
  <mat-expansion-panel
    *ngFor="let post of sortedPosts"
    [expanded]="post.open"
    (opened)="post.open = true"
    (closed)="post.open = false"
  >
    <mat-expansion-panel-header>
      <mat-panel-title>{{post.title || '(new post)'}}</mat-panel-title>
      <mat-panel-description>
        {{post.updatedAtString}}
      </mat-panel-description>
    </mat-expansion-panel-header>
    <form>
      <div class="input-container">
        <mat-form-field>
          <input
            matInput
            [(ngModel)]="post.title"
            name="title"
            placeholder="Title"
            required
          />
        </mat-form-field>
        <mat-form-field>
          <textarea
            matInput
            placeholder="Body"
            required
            [(ngModel)]="post.body"
            name="body"
            cdkTextareaAutosize
            cdkAutosizeMinRows="4"
            cdkAutosizeMaxRows="10"
          ></textarea>
        </mat-form-field>
      </div>
      <mat-action-row>
        <button
          mat-button
          color="primary"
          [disabled]="post.loading || !post.isDirty"
          (click)="post.save()"
        >
          <span *ngIf="post.loading">Saving...</span>
          <span *ngIf="!post.loading">Save</span>
        </button>
        <button
          mat-button
          type="button"
          [disabled]="post.loading || !post.isDirty"
          (click)="post.reset()"
        >
          Cancel
        </button>
        <button
          mat-button
          type="button"
          color="warn"
          [disabled]="post.loading"
          (click)="post.delete()"
        >
          Delete
        </button>
      </mat-action-row>
    </form>
  </mat-expansion-panel>
</mat-accordion>
<button mat-fab class="add-button" (click)="addPost()" [disabled]="newIsOpen">
  <mat-icon aria-label="Create new post">add</mat-icon>
</button> 
```

Enter fullscreen mode Exit fullscreen mode

手风琴(`mat-accordion`)允许您创建可随动画展开和收缩的项目。除了在过渡期间，它通常一次只显示一个展开的项目。

展开面板(`mat-expansion-panel`)创建项目列表。您可以单击其中一个项目将其展开。`*ngFor`指令告诉 Angular 它应该为`sortedPosts`中的每个`post`创建一个新的。

属性周围的括号(`[]`)告诉 Angular 您想要为该参数赋值。在这种情况下，每当`post.open`改变时，它就更新`expanded`。

属性周围的括号(`()`)告诉 Angular 您想要对值的变化做出反应。在这种情况下，每当`opened`被触发时，`open`将被设置为该特定帖子的`true`。同样，当面板关闭时，`post.open`被设置为`false`。

```
<mat-accordion>
  <mat-expansion-panel
    *ngFor="let post of sortedPosts"
    [expanded]="post.open"
    (opened)="post.open = true"
    (closed)="post.open = false"
  >
    <!-- ... -->
  </mat-expansion-panel>
</mat-accordion> 
```

Enter fullscreen mode Exit fullscreen mode

扩展面板标题(`mat-expansion-panel-header`)是始终显示的面板部分。这是你设置文章标题和简短描述的地方。

```
<mat-expansion-panel-header>
  <mat-panel-title>{{post.title || '(new post)'}}</mat-panel-title>
  <mat-panel-description>
    {{post.updatedAtString}}
  </mat-panel-description>
</mat-expansion-panel-header> 
```

Enter fullscreen mode Exit fullscreen mode

当使用 Angular 表单时，`form`元素会自动以一种更友好的方式处理表单，而不是默认发送 POST 数据到 URL。在`form`元素中，我们放置了我们的模型。

指令使用材料设计的输入使它更时尚。没有它，您只能得到一个基本的输入框，但是有了它，您可以得到浮动占位符、更好的错误处理和与 UI 其余部分匹配的样式。

前面您看到用`[]`包装属性意味着它将设置一些值。将它包装在`()`中意味着它可以接收值。对于双向绑定，可以将属性包装在两者中，`ngModel`是一个表单指令。综上所述，`[(ngModel)]`将在帖子值改变时更新`input`，并且在用户改变输入值时更新帖子。

`input-container`类将允许我们稍后轻松地设计容器的样式。

```
<div class="input-container">
  <mat-form-field>
    <input
      matInput
      [(ngModel)]="post.title"
      name="title"
      placeholder="Title"
      required
    />
  </mat-form-field>
  <mat-form-field>
    <textarea
      matInput
      placeholder="Body"
      required
      [(ngModel)]="post.body"
      name="body"
      cdkTextareaAutosize
      cdkAutosizeMinRows="4"
      cdkAutosizeMaxRows="10"
    ></textarea>
  </mat-form-field>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在`form`里面还有动作按钮。通过将它们放在`form`元素中，当你按下键盘上的`Enter`键时，提交按钮可以工作。

`mat-action-row`组件创建一个单独的行并将按钮放到一边。

这里的"取消"按钮会触发 post 将`reset`恢复到原来的值。因为只有当值与原始值不同时，重置这些值才有意义，所以我们检查 post `isDirty`是否正确。你也不想在保存或删除的过程中重置值，所以你也可以检查`post.loading`。

出于与“取消”按钮相同的原因，“保存”按钮被禁用是有意义的，因此它对`disabled`使用相同的逻辑。当你点击按钮的时候，它应该告诉 post 到`save`。如果保存时间需要一些时间，你可以更新用户界面，在文章加载时显示`Saving...`，否则显示`Save`。为此，使用特殊的`*ngIf`指令。

如果帖子在等待 API 响应，应该禁用“删除”按钮，否则不应该关心帖子是否肮脏。

```
<mat-action-row>
  <button
    mat-button
    color="primary"
    [disabled]="post.loading || !post.isDirty"
    (click)="post.save()"
  >
    <span *ngIf="post.loading">Saving...</span>
    <span *ngIf="!post.loading">Save</span>
  </button>
  <button
    mat-button
    type="button"
    [disabled]="post.loading || !post.isDirty"
    (click)="post.reset()"
  >
    Cancel
  </button>
  <button
    mat-button
    type="button"
    color="warn"
    [disabled]="post.loading"
    (click)="post.delete()"
  >
    Delete
  </button>
</mat-action-row> 
```

Enter fullscreen mode Exit fullscreen mode

为了添加新帖子，您需要一个按钮。材料设计通常在屏幕的右下角有一个浮动的动作按钮(FAB)。添加一个类`add-button`将使以后的样式化更加容易。当帖子已经

```
<button mat-fab class="add-button" (click)="addPost()" [disabled]="newIsOpen">
  <mat-icon aria-label="Create new post">add</mat-icon>
</button> 
```

Enter fullscreen mode Exit fullscreen mode

### 淡淡的风情

最后，添加一点样式来总结文章管理器组件。上面，输入被包装在带有类`input-container`的`div`中。添加下面的代码将使每个输入都有自己的行，而不是并排堆叠。

同样，为了让*浮动*动作按钮真正“浮动”，你需要在屏幕右下角给它一个固定的位置。

**src/app/posts-manager/posts-manager . component . CSS**

```
.input-container {
  display: flex;
  flex-direction: column;
}

.add-button {
  position: fixed;
  right: 24px;
  bottom: 24px;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试你的 Angular + Node CRUD App

现在，您有了一个全功能的单页面应用程序，连接到 REST API 服务器，通过 Okta 的 OIDC 进行身份验证。

现在就开始测试这款应用吧。如果它们还没有运行，确保启动服务器和前端。在您的终端中，从您的项目目录运行`npm start`。

导航到 [http://localhost:4200](http://localhost:4200) 。你应该能够随心所欲地添加、编辑、查看和删除帖子！

[![New Post](img/343a2027ae8f3fa6a03b85a383b0076b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xy40zBAN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/new-post-0fb305a87c7ed83a360f1ba4a6ed74ccecf2f23912168490a85bd58801e7dfeb.png)

[![List of Posts](img/96067c0568a2f9948d797bcf9bf7a2dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7EsrR8Qu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/node-angular-crud/posts-list-0ee4fbe792169823b836a560ba31780fcaef3d03dd1ff7a675014c0521312ff2.png)

## 了解有关角度、节点和应用安全性的更多信息

我希望你喜欢这篇文章，并发现它是有帮助的。如果您正在尝试 JavaScript 框架或后端语言，并且还没有决定使用哪种堆栈，您可能想看看这些类似的教程:

*   [用 Vue.js 和节点](https://developer.okta.com/blog/2018/02/15/build-crud-app-vuejs-node)构建一个基本的 CRUD App
*   [用 Node 和 React 构建一个基本的 CRUD 应用](https://dev.to/oktadev/build-a-basic-crud-app-with-node-and-react-554c)
*   [使用 ASP.NET 内核构建一个安全的 CRUD 应用程序并做出反应](https://dev.to/oktadev/build-a-secure-crud-app-with-aspnet-core-and-react-46ha)
*   [用 ASP.NET 内核和 Angular 构建 CRUD 应用](https://dev.to/oktadev/build-a-crud-app-with-aspnet-core-and-angular-34a5-temp-slug-2435391)

如果您渴望了解更多信息，请查看其他一些优秀的文章或浏览 Okta 开发者博客。

*   [渐进式网络应用的终极指南](https://developer.okta.com/blog/2017/07/20/the-ultimate-guide-to-progressive-web-applications)
*   [教程:用 Node.js 构建一个基本的 CRUD App](https://dev.to/oktadev/tutorial-build-a-basic-crud-app-with-nodejs-1ohn)
*   [用 Angular 5.0 和 Spring Boot 2.0 构建一个基本的 CRUD 应用](https://developer.okta.com/blog/2017/12/04/basic-crud-angular-and-spring-boot)
*   [Angular 6:有什么新功能，为什么要升级](https://dev.to/oktadev/angular-6-whats-new-and-why-upgrade-3d0l-temp-slug-9604950)
*   [向您的 Angular PWA 添加认证](https://developer.okta.com/blog/2017/06/13/add-authentication-angular-pwa)

一如既往，我们希望听到您的意见。请在评论中或在 Twitter [@oktadev](https://twitter.com/oktadev) 上提出问题或反馈。