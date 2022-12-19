# 创建一个有角度的购物应用程序，并使用 Rave 集成支付

> 原文：<https://dev.to/ashinzekene/creating-an-angular-shopping-app-and-integrating-payments-using-rave-418l>

## 概述

*   [什么是 Rave](#rave)
*   [设置和文件夹结构](#set-up)
*   [角形部分](#ng-part)
*   [整合锐舞支付](#int-rave)
*   [结论](#bye)

### 

[Rave](https://ravepay.co/) 为企业提供安全、便捷、安全的支付解决方案。它免费且易于上手。我们将介绍如何使用这项技术从您的 Angular 应用程序中接受付款。

我们将使用 Angular 构建一个简单的购物应用程序，用户可以轻松地购买打折商品。

### 

我们将首先从安装节点(如果不存在)开始。可在[下载页面](https://nodejs.org/en/download/)获得。节点附带了它的包管理器，NPM(节点包管理器)。我们现在跑

```
npm install --g @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你有纱线

```
yarn add global @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

这将安装 angular cli，用于创建新的 angular 应用程序、组件、模块、管道等。为此，我们可能需要以系统管理员的身份运行。安装后，我们可以通过运行
来验证一切正常

```
ng version 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以创建我们的角度应用程序。我们将通过运行
来实现这一点

```
ng new shopping-app 
```

Enter fullscreen mode Exit fullscreen mode

这为我们创建了一个 angular 应用程序并安装了依赖项

在这之后，我们运行

```
cd shopping-app
ng serve --open 
```

Enter fullscreen mode Exit fullscreen mode

这服务于我们的应用程序，并在浏览器中打开它。接下来，我们打开我们的`index.html`并插入来自 CDN
的引导程序

```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  Shopping App
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>
... 
```

Enter fullscreen mode Exit fullscreen mode

### 

为了简化我们的应用程序，让我们创建两个组件

*   导航条组件和
*   产品组件

我们会跑

```
ng generate component navbar
ng generate component products 
```

Enter fullscreen mode Exit fullscreen mode

这个命令为我们创建了两个组件。现在打开`navbar.component.html`并插入这个

```
<nav class="navbar navbar-expand-sm navbar-light bg-white py-3">
  <a class="navbar-brand pr-2">Shopping App</a>
  <button class="navbar-toggler navbar-toggler-left" type="button" (click)="navbarCollapsed = !navbarCollapsed" [attr.aria-expanded]="!navbarCollapsed"
    aria-controls="navbarContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

为了添加阴影，我们将把它添加到`navbar.component.css`
的样式中

```
nav {
  box-shadow: 0px 2px 15px 0px rgba(0, 0, 0, 0.2);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还将打开我们的`navbar.component.ts`并添加这个

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-navbar',
  templateUrl: './navbar.component.html',
  styleUrls: ['./navbar.component.css']
})
export class NavbarComponent {
  title = 'app';
  > navbarCollapsed: boolean = true
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 Bootstrap 的模板添加了一个导航栏。在我们的`navbar.component.html`中，我们添加了一个属性；`navbarCollapsed: boolean = true`切换导航条折叠

在我们的`app.component.html`。我们会像这样导入导航栏

```
<app-navbar></app-navbar> 
```

Enter fullscreen mode Exit fullscreen mode

当我们在浏览器上访问该站点时，我们应该看到显示的导航栏。
接下来，我们将添加一个英雄内容到我们的`app.conponent.html`中，就像这样

```
<app-navbar></app-navbar>
<div class="hero d-flex justify-content-center align-items-center bg-primary" style="height:80vh;">
  <div class="flex-child text-white text-center">
    <div class="display-3 p-3">The Shopping App</div>
    <div class="lead font-italic">Quality items at affordable prices...</div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将打开我们的`products.component.ts`文件。我们在[这个文件](https://gist.github.com/ashinzekene/0e05b7ed44dc8f3c3f978b48a7101758)中有一系列我们可用的产品及其价格。我们会像这样把它导入到我们的`products.component.ts`中:

```
import { Component, OnInit } from '@angular/core';
import { products } from '../data/data';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  public allProducts = products

  constructor() { }

  ngOnInit() {
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经在产品组件上提供了产品列表。我们会打开我们的`products.component.html`然后做这个

```
<div class="products row mx-2 p-3 d-flex justify-content-center">
  <div *ngFor="let product of allProducts" class="card m-2" style="width: 18rem;">
    <img class="card-img-top" [src]="product.img_url" [alt]="product.name">
    <div class="card-body">
      <h3>{{ product.name }} <div *ngIf="product.is_new" class="badge badge-success"></div> </h3>
      <p class="card-text">{{ product.description }}</p>
      <button class="btn btn-primary">Buy for &#x20a6;{{ product.price }}</button>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将产品组件导入到`app.component.html`中，如下所示:

```
<app-navbar></app-navbar>
<div class="hero d-flex justify-content-center align-items-center bg-primary" style="height:80vh;">
  <div class="flex-child text-white text-center">
    <div class="display-3 p-3">The Shopping App</div>
    <div class="lead font-italic">Quality items at affordable prices...</div>
  </div>
</div>
<app-products></app-products> 
```

Enter fullscreen mode Exit fullscreen mode

我们应该看到产品整齐地列在主页上。

### 

接下来是在我们的应用程序中接受付款。我们会使用角度锐舞模块。运行

```
npm install --save angular-rave 
```

Enter fullscreen mode Exit fullscreen mode

把`rave script`添加到你的`index.html`

```
<script src="//rave-api-v2.herokuapp.com/flwv3-pug/getpaidx/api/flwpbf-inline.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后打开`app.module.ts`并导入 angular-rave 模块

```
import { AngularRaveModule } from 'angular-rave';

@NgModule({
  imports: [
   AngularRaveModule,
   ...
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

在将 rave 集成到我们的应用程序之前，我们首先需要获得一个公钥。我们可以去 http://rave.frontendpwc.com 的 T2 创建一个账户。在仪表板上，我们将复制出我们的公钥，我们将在我们的应用程序中需要它。现在我们可以在我们的应用程序中使用 angular-rave。我们将把`angular-rave directive`添加到我们的支付按钮和其他导入参数中，比如:

```
<div class="products row mx-2 p-3 d-flex justify-content-center">
  <div *ngFor="let product of allProducts" class="card m-2" style="width: 18rem;">
    <img class="card-img-top" [src]="product.img_url" [alt]="product.name">
    <div class="card-body">
      <h3>{{ product.name }} <div *ngIf="product.is_new" class="badge badge-success"></div> </h3>
      <p class="card-text">{{ product.description }}</p>
      <button
        angular-rave
        [PBFPubKey] = "FLWPUBK-XXXXXXXXXXXX"
        [customer_email] = "'user@example.com'"
        [amount]="product.price"
        [custom_title]="product.name"
        [txref]="product.name + Date.now()"
        (callback)="paymentSuccess($event)"
        (close)="paymentFailure()"
        class="btn btn-primary">Buy for &#x20a6;{{ product.price }}</button>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了两个函数来响应支付成功和失败。所以我们打开`products.component.ts`并编写这些函数

```
 paymentSuccess(res) {
    console.log("Payment successfull")
    // On a real app, we must first perform validation on the server by sending a request to rave to verify the transaction before anything else
    // this.serverService.verifyTransaction(res)
}

paymentFailure(){
    // Handle payment failure
} 
```

Enter fullscreen mode Exit fullscreen mode

### 

我们的应用程序已经准备好了。一旦用户点击支付按钮，锐舞弹出显示和 viola，我们可以在我们的应用程序上收到付款