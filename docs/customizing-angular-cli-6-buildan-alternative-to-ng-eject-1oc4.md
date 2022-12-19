# 定制 Angular CLI 6 构建-ng 弹出的替代方案

> 原文：<https://dev.to/jeb/customizing-angular-cli-6-buildan-alternative-to-ng-eject-1oc4>

因此，你的 Angular 6 项目只是超越了 TODO 应用程序，你必须定制你的构建配置。问题是如何做？

## Angular CLI 1.x ng 弹出 VS Angular CLI 6 生成器

在 Angular CLI 1.x (Angular 5)中，您可以使用 [`ng eject`](https://github.com/angular/angular-cli/wiki/1-x-eject) 命令来执行此操作，该命令会弹出整个底层 webpack 配置，并允许您随意修改它。

在 Angular CLI 6 中，该命令已被暂时禁用，我怀疑由于名为 **Builders** 的新概念，它将很快被弃用。
使用新的 Angular CLI，您可以通过定义自己的构建器以及使用社区提供的构建器之一来自定义构建过程。

## 扩展底层 webpack 配置

因此，让我们通过扩展底层 webpack 配置来定制我们的构建:

*   安装[@ angular-builders/custom-web pack](https://github.com/meltedspark/angular-builders/packages/custom-webpack):`npm i -D @angular-builders/custom-webpack`
    *注意它需要版本[@ angular-dev kit/build-angular](https://npmjs.com/package/@angular-devkit/build-angular)>= 0 . 7 . 0 所以你可能需要安装它(如果还没有安装)* :
    `npm i -D @angular-devkit/build-angular`

*   在 *angular.json* 中，将`@angular-devkit/build-angular:browser`构建器改为`@angular-builders/custom-webpack:browser` :

```
 "architect": {
       ...
      "build": {
          "builder": "@angular-builders/custom-webpack:browser"
          "options": {
                ...
          }
      ...
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   如果您构建了一个通用应用程序，并且想要修改您的服务器构建配置，请使用`@angular-builders/custom-webpack:server`而不是`@angular-builders/custom-webpack:browser`

*   将`customWebpackConfig`添加到*构建*目标选项:

```
 "architect": {
       ...
      "build": {
          "builder": "@angular-builders/custom-webpack:browser"
          "options": {
                "customWebpackConfig": {
                   "path": "./extra-webpack.config.js"
                }  
                ...
          }
      ...
    } 
```

Enter fullscreen mode Exit fullscreen mode

点击查看`customWebpackConfig`物体[的完整描述。](https://github.com/meltedspark/angular-builders/tree/master/packages/custom-webpack#custom-webpack-config-object)

*   在应用程序的根目录下创建 *extra-webpack.config.js* (或者你在 *angular.json* 中指定的任何路径)。
*   用您需要的额外配置填充它(普通 webpack 配置)。
*   请注意，与`ng eject`相反，该配置将与默认角度构建配置*合并*，因此您只需配置您想要更改/添加的零件。例如，如果您想要添加另一个 webpack 加载程序，您的 webpack 配置将如下所示:

```
 module.exports = {
      module: {
        rules: [
          {
            test: /\.cool$/,
            use: 'cool-loader'
          }
        ]
      }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

## 而 ng 发球呢？

如果您想使用自定义 webpack 配置运行`ng serve`(假设您执行了上述所有步骤)，您应该执行以下操作:

*   安装[@ angular-builders/dev-server](https://github.com/meltedspark/angular-builders/tree/master/packages/dev-server)
*   将发球目标内的`@angular-devkit/build-angular:dev-server`替换为`@angular-builders/dev-server:generic`
*   运行`ng serve`

## 附加来源

你可以通过 Angular CLI 6 构建的*节点插件*来查看这个[电子角度原生](https://github.com/meltedspark/electron-angular-native)项目，作为电子角度应用的例子。

## 接下来是什么

在下一篇文章中，我们将学习如何创建自己的构建器。