# 使用 Dropwizard 托管 React 应用程序

> 原文：<https://dev.to/monknomo/host-a-react-app-with-dropwizard-3lik>

*[原本贴在 www.gunnargissel.com 的](https://www.gunnargissel.com/host-a-react-app-with-dropwizard.html)*

Dropwizard 是构建 RESTful 应用程序的一个很好的 Java 框架。它可以帮助你用 RESTful api 快速建立一个服务器，并且有很多现成的有用特性来制作生产级的应用程序。

React 是一个用于构建 web 应用程序的很棒的 Javascript 库。通过 create-react-app，react 提供了运行开发服务器和创建生产版本的优秀工具。create-react-app 生产构建使一个目录充满了静态 html 和 Javascript 文件。您可以用 Apache、nginx 或任何种类的 web 服务器来托管这些。

最大限度地减少应用程序所需的基础设施是很方便的，所以用 api 服务器托管静态文件很有吸引力。用 Dropwizard 托管静态文件的标准方法是将它们包含在 jar 中，这样就可以从类路径中访问它们。没有办法从外部托管现成的静态资产。

幸运的是，一些善良的灵魂创造了 [Dropwizard 可配置资产捆绑包](https://github.com/dropwizard-bundles/dropwizard-configurable-assets-bundle)，它允许你配置一个由 Dropwizard 托管在一个 uri 上的外部目录。

## 设置

[![origami wizard's duel](../Images/54a262ad6437818dc00138ae18a07259.png "origami wizard's duel")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r6p_jlWT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/HqQpL3Tm.jpg)

我已经创建了一个[骨骼 Dropwizard echo 服务器](https://github.com/monknomo/gravina-dropwizard-configurable-assets-bundle-skeleton)和一个[非常小的 React 应用](https://github.com/monknomo/gravina-react-ui)与之配套。Dropwizard 服务器是一个普通的配置，除了在`pom.xml`中增加了这个依赖项

```
<dependency>
    <groupId>io.dropwizard-bundles</groupId>
    <artifactId>dropwizard-configurable-assets-bundle</artifactId>
    <version>1.2.2</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

而`app.yml`中的这个加法

```
assets:
  mappings:
    /assets: /
  overrides:
    /: /teeny-tiny-react-app/build 
```

Enter fullscreen mode Exit fullscreen mode

对配置类的这一更改:

```
... extends Configuration implements AssetsBundleConfiguration {
...
@Override
public AssetsConfiguration getAssetsConfiguration() { return assets; }
... 
```

Enter fullscreen mode Exit fullscreen mode

这是对应用程序初始化的补充:

```
bootstrap.addBundle(new ConfiguredAssetsBundle("/assets/", "/", "index.html")); 
```

Enter fullscreen mode Exit fullscreen mode

最后，添加到应用程序运行命令中的:

```
env.jersey().setUrlPattern("/api/*") 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
但是什么意思呢？

这些配置更改将 drop wizard-configurable-assets-bundle 添加到您的项目中，将`/assets`类路径分配给`/` uri，将`/` uri 分配给`/teeny-tiny-react-app/build`目录。

它们还包括一个设置，如果没有任何内容与传入的 uri 匹配，则让 Dropwizard 查找 index.html。

最后，它们在`/api/*`托管 Dropwizard 资源。

## 我怎么用？

[![bulldozer](../Images/62c32a15f95c9448195eb6e11f66b4b4.png "bulldozer")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mBNkno1x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8ZPzPx1m.jpg)

这种配置允许两种使用模式——开发和生产。

在开发模式下，照常进行。启动 Dropwizard `java -jar muh-sweet-rest-api.jar server app.yml`，然后转移到 React 开发环境，在运行`npm run start`的同时开始编码。

在生产模式下，启动 Dropwizard，然后进行 React 构建`npm run build`。这将创建 React 应用程序的生产优化版本，您可以将它放在 Dropwizard 期望找到它的位置。

你将有两个选项来查看你的 React 应用程序——默认的 [http://localhost:3000](http://localhost:3000) 或者 Dropwizard 服务的 [http://localhost:8085](http://localhost:8085)

默认端口由 React 的内置服务器托管，并且拥有您习惯的所有自动重载功能。

Dropwizard 的端口托管静态构建，所以它不会在您编码时自动重新加载。但是，如果您手动部署它，它将自动提供新代码。

## 我为什么要这样做？

[![girl on slide](../Images/7c8be169b5e2d1b231e7e4ac875fb9bb.png "girl on slide")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AScX8YNB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/iWsnVTrm.jpg)

在以下情况下，您应该这样做:

*   您已经在运行一个 Java 后端
*   您希望将 React 变更与 Java 变更分开部署
*   您有一个允许您接触 war 部署位置的操作团队，但不能接触 html 部署位置(反之亦然)
*   扰乱代理是困难的(特定于环境)

在以下情况下，您不应该这样做:

*   您希望前端和后端步调一致
*   代理很容易
*   您已经有一个或两个 html 服务器
*   你不想用 Java

**如果你喜欢这篇文章，[注册我的邮件列表，每月更新有趣的编程文章](https://www.gunnargissel.com/pages/email-signup-1.html)**

## 学分

*   *谢谢[玛拉基·布朗](https://www.flickr.com/photos/malachus/)提供的[巫师决斗](https://flic.kr/p/483B5i)T5】的图片*
*   *谢谢[大卫·麦格雷戈](https://www.flickr.com/photos/magilla03/)给的[水滴](https://flic.kr/p/c1H69U)T5】的图片*
*   *感谢[比尔·阿博特](https://www.flickr.com/photos/wbaiv/)提供的[推土机](https://flic.kr/p/bz7oMC)T5 的图片*
*   *谢谢[梅尔·强森](https://www.flickr.com/photos/bobandmel/)给我的[孩子在](https://flic.kr/p/7s8bR3)T5】滑梯上的照片*