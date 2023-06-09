# 用 Angular 和 Node 构建一个基本的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-basic-crud-app-with-angular-and-node-1818>

近年来，单页面应用程序变得越来越流行。SPA 是一个只包含一个页面的网站。这个单独的页面充当 JavaScript 应用程序的容器。JavaScript 负责获取内容并在容器中呈现它。内容通常从 web 服务获取，RESTful APIs 已经成为许多情况下的首选。组成 SPA 的应用程序部分通常被称为*客户端*或*前端*，而负责 REST API 的部分被称为*服务器*或*后端*。在本教程中，您将基于 Node 和 Express 开发一个带有 REST 后端的简单单页应用程序。

您将使用 Angular，因为它遵循 MVC 模式，并且清晰地将视图与模型分开。创建动态填充数据并在数据发生变化时自动更新的 HTML 模板非常简单。我开始喜欢这个框架，因为它非常强大，有一个巨大的社区和优秀的文档。

对于服务器，您将使用 Node with Express。Express 是一个框架，通过允许定义在服务器上为不同请求运行的代码，使得创建 REST APIs 变得容易。附加服务可以全局插入，或者根据请求插入。有许多构建在 Express 之上的框架，可以自动将数据库模型转换成 API。为了突出重点，本教程将不会使用其中任何一个。

Angular 鼓励使用 TypeScript。TypeScript 为 JavaScript 添加了类型信息，在我看来，它是用 JavaScript 开发大规模应用程序的未来。因此，您将使用 TypeScript 开发客户端和服务器。

以下是您将用于客户端和服务器的库:

*   **Angular:** 用于构建客户端应用的框架
*   **Okta for authorization:**在客户端和服务器端使用 Okta 管理单点登录授权的插件
*   **Angular Material:** 一个提供开箱即用材质设计的 Angular 插件
*   **节点:**运行 JavaScript 代码的实际服务器
*   **Express:** 用于响应服务器请求和构建 REST APIs 的路由库
*   **TypeORM:** 一个用于 TypeScript 的数据库 ORM 库

## 启动基本的 Angular 客户端应用程序

让我们从使用 Angular 实现一个基本客户端开始。目标是开发一个产品目录，让您管理产品，它们的价格，和它们的库存水平。在本节的最后，您将有一个简单的应用程序，包括一个顶栏和两个视图，Home 和 Products。产品视图还没有任何内容，也没有密码保护。这将在下面的章节中讨论。

开始你需要安装 Angular。我假设您已经在系统上安装了 Node，并且可以使用`npm`命令。在终端中键入以下命令。

```
npm install -g @angular/cli@7.0.2 
```

根据您的系统，您可能需要使用`sudo`运行这个命令，因为它将全局安装软件包。`angular-cli`包提供了用于管理角度应用的`ng`命令。安装完成后，转到您选择的目录，使用以下命令创建您的第一个 Angular 应用程序。

```
ng new MyAngularClient 
```

使用 Angular 7，这将提示您两个查询。第一个询问您是否要包括路由。对此回答**是**。第二个查询与您想要使用的样式表类型有关。保留默认的 **CSS** 。

`ng new`将创建一个名为`MyAngularClient`的新目录，并用一个应用程序框架填充它。让我们花点时间来看看前面的命令创建的一些文件。在应用程序的`src`目录下，你会发现一个文件`index.html`，它是应用程序的主页面。它包含的内容不多，只是简单地扮演一个容器的角色。你还会看到一个`style.css`文件。这包含应用于整个应用程序的全局样式表。如果您浏览文件夹，您可能会注意到一个包含五个文件的目录`src/app`。

```
app-routing.module.ts
app.component.css
app.component.html
app.component.ts
app.component.spec.ts
app.module.ts 
```

这些文件定义了将被插入到`index.html`中的主要应用程序组件。以下是每个文件的简短描述:

*   `app.component.css`文件包含主`app`组件的样式表。可以在本地为每个组件定义样式
*   `app.component.html`包含组件的 HTML 模板
*   `app.component.ts`文件包含控制视图的代码
*   `app.module.ts`定义您的应用程序将使用哪些模块
*   `app-routing.module.ts`是为您的应用程序定义路线而设置的
*   `app.component.spec.ts`包含对`app`组件进行单元测试的框架

我不会在本教程中讨论测试，但是在实际应用中，您应该利用这个特性。在开始之前，您需要再安装几个软件包。这些将帮助你快速创建一个设计精美的响应布局。导航到客户端的基本目录`MyAngularClient`，并键入以下命令。

```
npm i @angular/material@7.0.2 @angular/cdk@7.0.2 @angular/animations@7.0.1 @angular/flex-layout@7.0.0-beta.19 
```

`@angular/material`和`@angular/cdk`库提供基于谷歌材料设计的组件，`@angular/animations`用于提供平滑过渡，`@angular/flex-layout`为您提供工具，使您的设计反应灵敏。

接下来，为`app`组件创建 HTML 模板。打开`src/app/app.component.html`并用以下内容替换内容。

```
<mat-toolbar color="primary" class="expanded-toolbar">
  <button mat-button routerLink="/">{{title}}</button>

  <div fxLayout="row" fxShow="false" fxShow.gt-sm>
    <button mat-button routerLink="/"><mat-icon>home</mat-icon></button>
    <button mat-button routerLink="/products">Products</button>
    <button mat-button *ngIf="!isAuthenticated" (click)="login()"> Login </button>
    <button mat-button *ngIf="isAuthenticated" (click)="logout()"> Logout </button>
  </div>
  <button mat-button [mat-menu-trigger-for]="menu" fxHide="false" fxHide.gt-sm>
    <mat-icon>menu</mat-icon>
  </button>
</mat-toolbar>
<mat-menu x-position="before" #menu="matMenu">
  <button mat-menu-item routerLink="/"><mat-icon>home</mat-icon> Home</button>
  <button mat-menu-item routerLink="/products">Products</button>;
  <button mat-menu-item *ngIf="!isAuthenticated" (click)="login()"> Login </button>
  <button mat-menu-item *ngIf="isAuthenticated" (click)="logout()"> Logout </button>
</mat-menu>
<router-outlet></router-outlet> 
```

`mat-toolbar`包含材料设计工具栏，而`router-outlet`是将由路由器填充的容器。应该编辑`app.component.ts`文件，使其包含以下内容。

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  public title = 'My Angular App';
  public isAuthenticated: boolean;

  constructor() {
    this.isAuthenticated = false;
  }

  login() {
  }

  logout() {
  }
} 
```

这是`app`组件的控制器。您可以看到它包含一个名为`isAuthenticated`的属性以及两个方法`login`和`logout`。目前这些什么也做不了。它们将在下一节中实现，下一节将介绍 Okta 的用户认证。现在定义您将使用的所有模块。用下面的代码替换`app.module.ts`的内容:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { FlexLayoutModule } from '@angular/flex-layout';
import {
  MatButtonModule,
  MatDividerModule,
  MatIconModule,
  MatMenuModule,
  MatProgressSpinnerModule,
  MatTableModule,
  MatToolbarModule
} from '@angular/material';
import { HttpClientModule } from '@angular/common/http';
import { FormsModule } from '@angular/forms';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    AppRoutingModule,
    BrowserModule,
    BrowserAnimationsModule,
    HttpClientModule,
    FlexLayoutModule,
    MatToolbarModule,
    MatMenuModule,
    MatIconModule,
    MatButtonModule,
    MatTableModule,
    MatDividerModule,
    MatProgressSpinnerModule,
    FormsModule,
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

请注意所有的材料设计模块。`@angular/material`库要求您为您希望在应用程序中使用的每种类型的组件导入一个模块。从 Angular 7 开始，默认的应用程序框架包含一个名为`app-routing.module.ts`的单独文件。编辑它以声明以下路线。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProductsComponent } from './products/products.component';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  },
  {
    path: 'products',
    component: ProductsComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

这定义了对应于根路径和`products`路径的两条路线。它还将`HomeComponent`和`ProductsComponent`连接到这些路由。现在创建这些组件。在 Angular 客户端的基本目录中，键入以下命令。

```
ng generate component Products
ng generate component Home 
```

这将为每个组件创建`html`、`css`、`ts`和`spec.ts`文件。它还更新`app.module.ts`来声明新的组件。打开`src/app/home`目录下的`home.component.html`，粘贴以下内容。

```
<div class="hero">
  <div>
    <h1>Hello World</h1>
    <p class="lead">This is the homepage of your Angular app</p>
  </div>
</div> 
```

在`home.component.css`文件中也包含一些样式。

```
.hero {
  text-align: center;
  height: 90vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  font-family: sans-serif;
} 
```

暂时将`ProductsComponent`留空。一旦您创建了后端 REST 服务器并能够用一些数据填充它，这将被实现。为了让一切看起来漂亮，只剩下两个小任务。将以下样式复制到`src/style.css`

```
@import "~@angular/material/prebuilt-themes/deeppurple-amber.css";

body {
  margin: 0;
  font-family: sans-serif;
}

.expanded-toolbar {
  justify-content: space-between;
}

h1 {
  text-align: center;
} 
```

最后，为了渲染材质设计图标，在`index.html`文件的`<head>`标签内添加一行。

```
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet"> 
```

现在，您已经准备好启动 Angular 服务器，看看到目前为止您已经取得了哪些成果。在客户端应用程序的基本目录中，键入以下命令。

```
ng serve 
```

然后打开浏览器并导航至`http://localhost:4200`。

## 为您的 Node + Angular App 添加认证

如果你曾经从零开始开发过 web 应用程序，你会知道仅仅允许用户注册、验证、登录和退出你的应用程序就需要做多少工作。使用 Okta 可以大大简化这个过程。首先，你需要一个 Okta 的开发者账户。

[![developer.okta.com](img/4bc9c01419f4cf6fbc6a07675f0278af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7jyaOkb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-node/developer.okta.com-bd04e258d4735d02eec724bd1f79178cf140cfc8708c521473b6b4a8b5d8696f.png)

在浏览器中，导航至[developer.okta.com](https://developer.okta.com/)，点击**创建免费账户**，输入您的详细信息。

[![Start building on Okta](img/f3e5015ef5d505b4e40789cf102528a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nr5IvRUU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-node/start-building-6f4d161cb1219552c1c41257326f8fbeb89fbcf6ae116e9b29801ea6a20ae762.png)

完成后，您将进入您的开发人员控制面板。点击**添加应用**按钮创建一个新的应用。

[![Add Application](img/544336f6e58aff41d272d53db5b2e0d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7zdExOum--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-node/add-application-ed60bfbd375116e64fc23ba3c85d4b86ae8478a62303194d3a5067adab135109.png)

首先创建一个新的单页应用程序。选择**单页 App** ，点击**下一步**。

[![Create new Single Page App](img/6f1769e76267c76cd613631dd2ea9e89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c9jLlS7f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-node/single-page-app-54193ed48ed3e03b9b12a3f8d425c390940cb6b7dfed337d28424045b470915a.png)

在下一页，您需要编辑默认设置。确保端口号为 4200。这是角度应用的默认端口。

[![My Angular App](img/bcd56f95104312c45a332d2182d90ed3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nTy7ZM1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-node/my-angular-app-1290784d87169fbcd6e9e9b124efdcc4e5e76efa58f2ccc48f634b7dfca92652.png)

就是这样。现在您应该看到一个**客户端 ID** ，您需要将它粘贴到您的 TypeScript 代码中。

要在客户机中实现身份验证，需要为 Angular 安装 Okta 库。

```
npm install @okta/okta-angular@1.0.7 --save-exact 
```

在`app.module.ts`中导入`OktaAuthModule`。

```
import { OktaAuthModule } from '@okta/okta-angular'; 
```

在`app`模块的`imports`列表中，添加:

```
OktaAuthModule.initAuth({
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  redirectUri: 'http://localhost:4200/implicit/callback',
  clientId: '{YourClientId}'
}) 
```

这里的`yourOktaDomain`应该替换为当您导航到 Okta 仪表板时在浏览器中看到的开发域。`YourClientId`必须替换为您在注册应用程序时获得的客户端 ID。上面的代码使 Okta 身份验证模块在您的应用程序中可用。在`app.component.ts`使用，导入服务。

```
import { OktaAuthService } from '@okta/okta-angular'; 
```

修改构造函数来注入服务并订阅它。

```
constructor(public oktaAuth: OktaAuthService) {
  this.oktaAuth.$authenticationState.subscribe(
    (isAuthenticated: boolean) => this.isAuthenticated = isAuthenticated
  );
} 
```

现在，身份验证状态的任何变化都将反映在`isAuthenticated`属性中。当组件被加载时，您仍然需要初始化它。创建一个`ngOnInit`方法并将`implements OnInit`添加到你的类定义

```
import { Component, OnInit } from '@angular/core';
...
export class AppComponent implements OnInit {
  ...
  async ngOnInit() {
    this.isAuthenticated = await this.oktaAuth.isAuthenticated();
  }
} 
```

最后，实现`login`和`logout`方法对用户界面做出反应，并让用户登录或注销。

```
login() {
  this.oktaAuth.loginRedirect();
}

logout() {
  this.oktaAuth.logout('/');
} 
```

在路由模块中，您需要注册将用于登录请求的路由。打开`app-routing.module.ts`，导入`OktaCallbackComponent`和`OktaAuthGuard`。

```
import { OktaCallbackComponent, OktaAuthGuard } from '@okta/okta-angular'; 
```

向`routes`数组添加另一条路线。

```
{
  path: 'implicit/callback',
  component: OktaCallbackComponent
} 
```

这将允许用户使用**登录**按钮登录。为了保护`Products`路线免受未经授权的访问，在`products`路线中添加以下行。

```
{
  path: 'products',
  component: ProductsComponent,
  canActivate: [OktaAuthGuard]
} 
```

这就是全部了。现在，当用户试图访问产品视图时，他们将被重定向到 Okta 登录页面。登录后，用户将被重定向回产品视图。

## 实现一个节点 REST API

下一步是实现一个基于 Node 和 Express 的服务器来存储产品信息。这将使用一些较小的图书馆，使你的生活更容易。要在 TypeScript 中开发，您需要`typescript`和`tsc`。对于数据库抽象层，您将使用**类型的表单**。这是一个方便的库，它将行为注入到 TypeScript 类中，并将它们转换成数据库模型。创建一个新目录来包含您的服务器应用程序，然后在其中运行以下命令。

```
npm init 
```

回答所有问题，然后运行:

```
npm install --save-exact express@4.16.4 @types/express@4.16.0 @okta/jwt-verifier@0.0.14 express-bearer-token@2.2.0 tsc@1.20150623.0 typescript@3.1.3 typeorm@0.2.8 sqlite3@4.0.3 cors@2.8.4 @types/cors@2.8.4 
```

我不会详细介绍所有这些库，但是您会看到`@okta/jwt-verifier`用于验证 JSON Web 令牌并对其进行身份验证。

为了让 TypeScript 工作，创建一个文件`tsconfig.json`并粘贴以下内容。

```
{  "compilerOptions":  {  "target":  "es6",  "module":  "commonjs",  "outDir":  "dist",  "experimentalDecorators":  true,  "emitDecoratorMetadata":  true  },  "include":  [  "src/**/*.ts"  ],  "exclude":  [  "node_modules"  ]  } 
```

创建服务器的第一步是为产品创建一个数据库模型。使用 TypeORM 这很简单。创建一个子目录`src`，并在其中创建一个文件`model.ts`。粘贴以下内容。

```
import {Entity, PrimaryGeneratedColumn, Column, createConnection, Connection, Repository} from 'typeorm';

@Entity()
export class Product {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  sku: string;

  @Column('text')
  description: string;

  @Column()
  price: number;

  @Column()
  stock: number;
} 
```

TypeORM 注释将类定义转化为数据库模型。我喜欢 TypeORM 项目，因为它易于使用，并且支持多种 SQL 和 NoSQL 数据库连接器。我建议你在 https://github.com/typeorm/typeorm 查阅文件。

您还需要访问产品的存储库。同样在`model.ts`文件中添加以下内容。

```
let connection:Connection;

export async function getProductRepository(): Promise<Repository<Product>> {
  if (connection===undefined) {
    connection = await createConnection({
      type: 'sqlite',
      database: 'myangularapp',
      synchronize: true,
      entities: [
        Product
      ],
    });
  }
  return connection.getRepository(Product);
} 
```

注意，为了简单起见，这里使用了 SQLite。在实际场景中，您应该用自己选择的数据库连接器来替换它。

接下来，创建一个名为`product.ts`的文件。该文件将包含产品上 CRUD 操作的所有路径的逻辑。

```
import { NextFunction, Request, Response, Router } from 'express';
import { getProductRepository, Product } from './model';

export const router: Router = Router();

router.get('/product', async function (req: Request, res: Response, next: NextFunction) {
  try {
    const repository = await getProductRepository();
    const allProducts = await repository.find();
    res.send(allProducts);
  }
  catch (err) {
    return next(err);
  }
});

router.get('/product/:id', async function (req: Request, res: Response, next: NextFunction) {
  try {
    const repository = await getProductRepository();
    const product = await repository.find({id: req.params.id});
    res.send(product);
  }
  catch (err) {
    return next(err);
  }
});

router.post('/product', async function (req: Request, res: Response, next: NextFunction) {
  try {
    const repository = await getProductRepository();
    const product = new Product();
    product.name = req.body.name;
    product.sku = req.body.sku;
    product.description = req.body.description;
    product.price = Number.parseFloat(req.body.price);
    product.stock = Number.parseInt(req.body.stock);

    const result = await repository.save(product);
    res.send(result);
  }
  catch (err) {
    return next(err);
  }
});

router.post('/product/:id', async function (req: Request, res: Response, next: NextFunction) {
  try {
    const repository = await getProductRepository();
    const product = await repository.findOne({id: req.params.id});
    product.name = req.body.name;
    product.sku = req.body.sku;
    product.description = req.body.description;
    product.price = Number.parseFloat(req.body.price);
    product.stock = Number.parseInt(req.body.stock);

    const result = await repository.save(product);
    res.send(result);
  }
  catch (err) {
    return next(err);
  }
});

router.delete('/product/:id', async function (req: Request, res: Response, next: NextFunction) {
  try {
    const repository = await getProductRepository();
    await repository.delete({id: req.params.id});
    res.send('OK');
  }
  catch (err) {
    return next(err);
  }
}); 
```

这个文件有点长，但并不包含任何令人惊讶的东西。`Product`创建对象，保存到数据库或从数据库中删除。

让我们再次将注意力转向认证。您需要确保只有经过身份验证的用户才能访问该服务。创建一个名为`auth.ts`的文件，并粘贴以下内容。

```
import { Request, Response, NextFunction} from 'express';

const OktaJwtVerifier = require('@okta/jwt-verifier');

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: '{YourClientId}',
  issuer: 'https://{yourOktaDomain}/oauth2/default'
});

export async function oktaAuth(req:Request, res:Response, next:NextFunction) {
  try {
    const token = (req as any).token;
    if (!token) {
      return res.status(401).send('Not Authorised');
    }
    const jwt = await oktaJwtVerifier.verifyAccessToken(token);
    req.user = {
      uid: jwt.claims.uid,
      email: jwt.claims.sub
    };
    next();
  }
  catch (err) {
    return res.status(401).send(err.message);
  }
} 
```

就像在客户端应用程序中一样,`yourOktaDomain`应该被开发域替换，而`YourClientId`必须被您的应用程序客户端 ID 替换。`oktaJwtVerifier`实例获取一个 JWT 令牌并对其进行认证。如果成功，用户 id 和电子邮件将被存储在`req.user`中。否则，服务器将以 401 状态代码进行响应。完成服务器的最后一部分是主入口点，它实际上启动服务器并注册到目前为止您已经定义的中间件。用以下内容创建一个文件`server.ts`。

```
import * as express from 'express';
import * as cors from 'cors';
import * as bodyParser from 'body-parser';
const bearerToken = require('express-bearer-token');
import {router as productRouter} from './product'
import {oktaAuth} from './auth'

const app = express()
  .use(cors())
  .use(bodyParser.json())
  .use(bearerToken())
  .use(oktaAuth)
  .use(productRouter);

app.listen(4201, (err) => {
  if (err) {
    return console.log(err);
  }

  return console.log('My Node App listening on port 4201');
}); 
```

要编译类型脚本，请运行命令

```
npx tsc 
```

然后，如果您想启动服务器，只需运行:

```
node dist/server.js 
```

## 完成你的棱角客户端

现在服务器已经完成了，让我们来完成客户端。第一步是创建一个包含产品数据的类。这个类类似于服务器应用程序中的`Product`类，但是没有 TypeORM 注释。它将包含在一个名为`product.ts`的文件中。

```
export class Product {
  id?: string;
  name: string;
  sku: string;
  description: string;
  price: number;
  stock: number;
} 
```

将该文件保存在与`products`组件相同的目录中。最好将对 REST API 的访问封装在一个单独的服务中。通过运行下面的命令创建一个`Products`服务。

```
ng generate service products/Products 
```

这将在`src/app/products`目录中创建一个名为`product.service.ts`的文件。用以下内容填充它。

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { OktaAuthService } from '@okta/okta-angular';
import { Product } from './product';

const baseUrl = 'http://localhost:4201';

@Injectable({
  providedIn: 'root'
})
export class ProductsService {

  constructor(public oktaAuth: OktaAuthService, private http: HttpClient) {
  }

  private async request(method: string, url: string, data?: any) {
    const token = await this.oktaAuth.getAccessToken();

    console.log('request ' + JSON.stringify(data));
    const result = this.http.request(method, url, {
      body: data,
      responseType: 'json',
      observe: 'body',
      headers: {
        Authorization: `Bearer ${token}`
      } 
    });
    return new Promise<any>((resolve, reject) => {
      result.subscribe(resolve as any, reject as any);
    });
  }

  getProducts() {
    return this.request('get', `${baseUrl}/product`);
  }

  getProduct(id: string) {
    return this.request('get', `${baseUrl}/product/${id}`);
  }

  createProduct(product: Product) {
    console.log('createProduct ' + JSON.stringify(product));
    return this.request('post', `${baseUrl}/product`, product);
  }

  updateProduct(product: Product) {
    console.log('updateProduct ' + JSON.stringify(product));
    return this.request('post', `${baseUrl}/product/${product.id}`, product);
  }

  deleteProduct(id: string) {
    return this.request('delete', `${baseUrl}/product/${id}`);
  }
} 
```

`ProductsService`包含 REST API 的每个路由的一个公共方法。HTTP 请求封装在一个单独的方法中。注意这个请求总是包含一个从`OktaAuthService`获得的`Bearer`令牌。这是服务器用来验证用户身份的令牌。

现在`ProductsComponent`可以实现了。下面的代码将完成这个任务。

```
import { Component, OnInit } from '@angular/core';
import { MatTableDataSource } from '@angular/material';
import { ProductsService } from './products.service';
import { Product } from './product';

@Component({
  selector: 'app-products',
  templateUrl: './products.component.html',
  styleUrls: ['./products.component.css']
})
export class ProductsComponent implements OnInit {
  displayedColumns: string[] = ['name', 'sku', 'description', 'price', 'stock', 'edit', 'delete'];
  dataSource = new MatTableDataSource<any>();

  selectedProduct: Product = new Product();
  loading = false;

  constructor(public productService: ProductsService) {
  }

  ngOnInit() {
    this.refresh();
  }

  async refresh() {
    this.loading = true;
    const data = await this.productService.getProducts();
    this.dataSource.data = data;
    this.loading = false;
  }

  async updateProduct() {
    if (this.selectedProduct.id !== undefined) {
      await this.productService.updateProduct(this.selectedProduct);
    } else {
      await this.productService.createProduct(this.selectedProduct);
    }
    this.selectedProduct = new Product();
    await this.refresh();
  }

  editProduct(product: Product) {
    this.selectedProduct = product;
  }

  clearProduct() {
    this.selectedProduct = new Product();
  }

  async deleteProduct(product: Product) {
    this.loading = true;
    if (confirm(`Are you sure you want to delete the product ${product.name}. This cannot be undone.`)) {
      this.productService.deleteProduct(product.id);
    }
    await this.refresh();
  }
} 
```

在`products.component.html`中，展示产品的布局由两部分组成。第一部分使用一个`mat-table`组件来显示产品列表。第二部分显示了一个表单，用户可以在其中编辑新的或现有的产品。

```
<h1 class="h1">Product Inventory</h1>
<div fxLayout="row" fxLayout.xs="column" fxLayoutAlign="space-between stretch" class="products">
  <table mat-table fxFlex="100%" fxFlex.gt-sm="66%" [dataSource]="dataSource" class="mat-elevation-z1">
    <ng-container matColumnDef="name">
      <th mat-header-cell *matHeaderCellDef> Name</th>
      <td mat-cell *matCellDef="let product"> {{product.name}}</td>
    </ng-container>
    <ng-container matColumnDef="sku">
      <th mat-header-cell *matHeaderCellDef> SKU</th>
      <td mat-cell *matCellDef="let product"> {{product.sku}}</td>
    </ng-container>
    <ng-container matColumnDef="description">
      <th mat-header-cell *matHeaderCellDef> Description</th>
      <td mat-cell *matCellDef="let product"> {{product.description}}</td>
    </ng-container>
    <ng-container matColumnDef="price">
      <th mat-header-cell *matHeaderCellDef> Price</th>
      <td mat-cell *matCellDef="let product"> {{product.price}}</td>
    </ng-container>
    <ng-container matColumnDef="stock">
      <th mat-header-cell *matHeaderCellDef> Stock Level</th>
      <td mat-cell *matCellDef="let product"> {{product.stock}}</td>
    </ng-container>
    <ng-container matColumnDef="edit">
      <th mat-header-cell *matHeaderCellDef></th>
      <td mat-cell *matCellDef="let product">
        <button mat-icon-button (click)="editProduct(product)">
          <mat-icon>edit</mat-icon>
        </button>
      </td>
    </ng-container>
    <ng-container matColumnDef="delete">
      <th mat-header-cell *matHeaderCellDef></th>
      <td mat-cell *matCellDef="let product">
        <button mat-icon-button (click)="deleteProduct(product)">
          <mat-icon>delete</mat-icon>
        </button>
      </td>
    </ng-container>
    <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
    <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
  </table>
  <mat-divider fxShow="false" fxShow.gt-sm [vertical]="true"></mat-divider>
  <div>
    <h2>Selected Product</h2>
    <label>Name
      <input type="text" [(ngModel)]="selectedProduct.name">
    </label>
    <label>SKU
      <input type="text" [(ngModel)]="selectedProduct.sku">
    </label>
    <label>Description
      <input type="text" [(ngModel)]="selectedProduct.description">
    </label>
    <label>Price
      <input type="text" [(ngModel)]="selectedProduct.price">
    </label>
    <label>Stock Level
      <input type="text" [(ngModel)]="selectedProduct.stock">
    </label>
    <button mat-flat-button color="primary" (click)="updateProduct()">{{(selectedProduct.id!==undefined)?'Update':'Create'}}</button>
    <button mat-flat-button color="primary" (click)="clearProduct()">Clear</button>
  </div>
  <div class="loading" *ngIf="loading">
    <mat-spinner></mat-spinner>
  </div>
</div> 
```

最后，在布局中添加一点样式。

```
.products {
  padding: 2rem;
}

label, input {
  display: block;
}

label {
  margin-bottom: 1rem;
}

.loading {
  position: absolute;
  display: flex;
  justify-content: center;
  align-content: center;
  width: 100%;
  height: 100%;
  background-color: rgba(255, 255, 255, 0.8);
} 
```

完成所有工作后，您可以启动客户机和服务器并测试您的应用程序。重复一遍，在包含服务器的目录中，运行:

```
node dist/server.js 
```

在客户端目录中，运行:

```
ng serve 
```

您的应用程序看起来应该如下所示

[![Product Inventory](img/3b741dad50053a1c19a2e31e5d427593.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f-ihx87O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-node/product-inventory-7d59ff869e57a8f2ccff80171940dc00812918d6dfc526c331110f2a1ebedadc.png)

## 了解更多关于角形、节点和 Express

在本教程中，我已经指导您使用 Angular 和 Node 开发了一个单页面 web 应用程序。仅仅使用几行代码，您就能够实现客户端和服务器的用户认证。Angular 利用了 TypeScript，它是 JavaScript 语言的超集，并添加了类型信息。TypeScript 使得代码更加稳定，这也是我决定使用这种语言实现 Node/Express 服务器的原因。如果您还不熟悉 TypeScript，请查看 Todd mortar 的这篇[精彩介绍。他也有一些关于 Angular 的好文章。](https://toddmotto.com/typescript-introduction)

本教程的完整代码可以在 GitHub 上找到[。](https://github.com/oktadeveloper/okta-angular-node-example)

如果您准备好了解更多关于 Angular 或 Node/Express 的信息，我们还有一些其他资源可供您查阅:

*   [简单节点认证](https://dev.to/blog/2018/04/24/simple-node-authentication)
*   [用 Node 和 React 构建一个基本的 CRUD 应用](https://dev.to/blog/2018/07/10/build-a-basic-crud-app-with-node-and-react)
*   [用 Express 和 GraphQL 构建一个简单的 API 服务](https://dev.to/blog/2018/09/27/build-a-simple-api-service-with-express-and-graphql)
*   [Angular 6 -有什么新功能，为什么要升级？](https://dev.to/blog/2018/05/09/upgrade-to-angular-6)
*   [用 Angular 7 和 Spring Boot 构建一个基本的 CRUD 应用](https://dev.to/blog/2018/08/22/basic-crud-angular-7-and-spring-boot-2)

一如既往，我们希望您关注我们，了解我们团队的更多精彩内容和更新。你可以在推特[@奥克塔德夫](https://twitter.com/OktaDev)，在[脸书](https://www.facebook.com/oktadevelopers/)，以及 [LinkedIn](https://www.linkedin.com/company/oktadev/) 上找到我们。