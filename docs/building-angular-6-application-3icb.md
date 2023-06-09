# 建筑角度 6 应用

> 原文：<https://dev.to/geeksamu/building-angular-6-application-3icb>

[![](img/965e8cc2cb31cdec3a54a29184fe202f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xlfbTJ3i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AByAhY7BtpARVPzkgM6yV1Q.png) 

<figcaption>棱角分明 6 系</figcaption>

这是关于 JavaScript 框架 Angular 6 的 n 部分[系列](https://dev.to/geeksamu/angular-6-article-series-2n26-temp-slug-5101921)的第二部分。

在这一部分，我们将详细介绍如何构建新的 Angular 6 应用程序。

这并不是一个完整的指南，而是一个让你入门和运行的基础概述，这样你就可以了解如何使用 Angular CLI 以及如何使用它构建你的 Angular 6 应用程序。

### 文章系列

1.  [棱角分明 6 篇系列文章](https://dev.to/geeksamu/angular-6-article-series-2n26-temp-slug-5101921)
2.  建筑角 6 应用(**你在这里**
3.  [使用带角 6 的角形材料](https://medium.com/@geeksamu/using-angular-material-with-angular-6-862a1c3d04b)
4.  [将 Angular 6 应用程序部署到 Netlify](https://medium.com/@geeksamu/deploy-angular-6-application-to-netlify-60b39b9df61c)
5.  创建角度为 6 的 PWA(**很快**
6.  动态主题在棱角分明的 6 个素材中(**即将**)
7.  棱角分明 6 与 GSAP ( **不久**)
8.  有角 6 带火基(**即将**)

### 升级 Angular-CLI

我假设您已经安装了 nodejs 和 npm。如果没有，你必须先安装它们。谷歌一下就知道了。

首先，我们需要升级我们的 CLI 或第一次安装它。下一个命令将安装它或升级您的当前版本。

```
npm i -g @angular/cli 
```

#### 创建新的角度 App

现在我们想使用 CLI 创建新的 Angular 6 应用程序。更改您想要创建新应用程序的目录，然后运行

```
ng new angular6-series --routing --style=scss --skip-tests 
```

其中 angular6-series 是我们应用的名称。但是我们有一些争论。

让我们解释一下我们命令的每一个参数

*   -路由:它建立我们的路线，所以我不需要自己建立它们
*   - style=scss:它将我们的样式扩展从 css 更改为 scss，因为我更喜欢 sass。
*   - skip-tests:跳过创建测试文件。但是你可能需要它们，在这种情况下，你必须去掉这个论点。

但是如果您想在没有所有这些选项的情况下正常构建您的应用程序，只需运行:

```
ng new angular6-series 
```

在创建了我们的新应用程序之后。移动应用程序目录并运行服务器。

```
cd angular6-series
ng serve --open 
```

ng serve 将运行我们应用程序的服务器，参数- open 将在默认浏览器中打开它。

现在你应该在浏览器中看到这个

[![](img/5a4aa957ff0e467083072925956140ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2UWHDziz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A2y0SONIf8kaLLPnH.png)

#### 创建组件

让我们创建一个名为 home 的新组件，并把它作为包含一些内容的主页。

```
ng g c home 
```

这将创建组件文件。但是你会发现你的应用程序没有任何变化。让我们修改我们的 src/app/app . component . html

去掉所有代码，留下最后一个标签。它应该看起来像

```
\<router-outlet\>\</router-outlet\> 
```

现在打开/src/app/app-routing . module . ts

我要添加新组件作为主页。

我们需要导入 HomeComponent 并将路由添加到 routes 数组中。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
 {
 path: "",
 component: HomeComponent
 },
];

@NgModule({
 imports: [RouterModule.forRoot(routes)],
 exports: [RouterModule]
})
export class AppRoutingModule { } 
```

现在，您应该可以在主页中看到您的 home 组件内容了。

我们来编辑一下我们的 home component open/src/app/home/home . component . html

```
\<h1\>
 Welcome to our Angular 6 Application
\</h1\>
\<p\>
 Lorem ipsum dolor sit amet consectetur adipisicing elit. Assumenda repudiandae repellat perferendis corrupti, harum provident
 ipsam illo laudantium magni deleniti corporis modi error aperiam iure molestiae eligendi quo voluptatem minus?
\</p\> 
```

让我们创建另一个名为 posts
的组件

```
ng g c posts 
```

首先，我们需要为我们的 posts 组件

open/src/app/app-routing . module . ts 添加新的路由，就像我们对 home 组件

所做的那样，我们的文件应该看起来像

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { PostsComponent } from './posts/posts.component';

const routes: Routes = [
 {
 path: "",
 component: HomeComponent
 },
 {
 path: "posts",
 component: PostsComponent
 },
];

@NgModule({
 imports: [RouterModule.forRoot(routes)],
 exports: [RouterModule]
})
export class AppRoutingModule { } 
```

现在打开/src/app/posts/posts . component . html

添加一些 html

```
\<div class="post"\>
 \<h1\>Post title here\</h1\>
 \<p\>Lorem ipsum dolor sit amet consectetur adipisicing elit. Mollitia minima soluta harum fugit neque accusamus non fuga blanditiis quam enim nemo, quia dolores iure explicabo officia, cumque expedita quis sint.\</p\>
\</div\>
\<div class="post"\>
 \<h1\>Post title here\</h1\>
 \<p\>Lorem ipsum dolor sit amet consectetur adipisicing elit. Mollitia minima soluta harum fugit neque accusamus non fuga blanditiis quam enim nemo, quia dolores iure explicabo officia, cumque expedita quis sint.\</p\>
 \</div\> 
```

您可以在浏览器中看到我们的新组件，方法是访问:[http://localhost:4200/posts](http://localhost:4200/posts)

目前就这些。

下一步:[使用带棱角的材料 6](https://medium.com/@geeksamu/using-angular-material-with-angular-6-862a1c3d04b)

### 如果你喜欢这篇文章，点击下面的按钮👏。查看我写的其他文章[在这里](https://blog.wb.gy)。如果你觉得这个故事有用。因为我不使用中型合作伙伴计划。考虑通过 [PayPal](http://paypal.me/geeksamu) 或[“给我买杯咖啡”](https://ko-fi.com/geeksamu):)进行捐赠

给艾哈迈德·阿卜杜勒萨拉姆买杯咖啡。ko-fi.com/geeksamu

如果你有任何问题，请在下面评论，或者你可以在 Twitter 上找到我

[艾哈迈德·⚯͛(@ geeks amu)|推特](https://twitter.com/geeksamu)