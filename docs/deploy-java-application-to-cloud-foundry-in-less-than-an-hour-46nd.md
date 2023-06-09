# 在不到一小时的时间内将 Java 应用部署到 Cloud Foundry

> 原文：<https://dev.to/skandpurohit/deploy-java-application-to-cloud-foundry-in-less-than-an-hour-46nd>

# 简介:

本文讨论了在本地工作站上使用 PCF Dev 设置 PCF，并部署一个 Java 应用程序。

时间:45 分钟

## 先决条件

虚拟盒子 5.0 或更高版本
Java JDK 7 或更高版本
4GB 内存
10GB 空间
在 PCF 上创建帐户

安装命令行界面

下载并安装 Cloud Foundry 命令行界面(cf cli):

*麦克 https://packages.cloudfoundry.org/stable?*
OSX[release = ma cosx 64-binary&source = github](https://packages.cloudfoundry.org/stable?release=macosx64-binary&source=github)

*Windows*
T3】https://packages.cloudfoundry.org/stable?release = windows 64-exe&source = github

*Linux*https://packages.cloudfoundry.org/stable?
T3】release = Linux 64-binary&source = github

**安装 PCF Dev**

下载最新版本的 PCF 开发，导航到[https://network.pivotal.io/products/pcfdev](https://network.pivotal.io/products/pcfdev)

在这里，我对 PCF 1.11.0 使用了 v0.28.0

[![Alt text of image](img/fb0b68d8247d939952bf6dfcdf9ff127.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8zrJnvG_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6i48cbpfmxsvbp705g1j.png)

**解压下载的 zip 文件**

[![Alt text of image](img/3b9a2a7ac769234b9113b9619f1b764e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uYICwaiW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1fqn0b9shvul8fiawplp.png)

**安装 PCF 开发插件**

[![Alt text of image](img/953f9cbb7e214f7da177e2c88103dc51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3z1PtIoY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8xtnnc78tqb4wrwsrgrh.png)

启动 PCF Dev

[![Alt text of image](img/59644643136ab1be606ad91204dd3dd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nl4ZQSoW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hnbj1l7eu9d6brdldcf6.png)

根据工作站的不同，此过程大约需要 10-20 分钟。

**部署 Java 应用程序**

现在，您已经在工作站上运行了 PCF Dev 虚拟机，下一步是部署样例 Java 应用程序。

**GIT 克隆**

$ git 克隆[https://github.com/cloudfoundry-samples/spring-music](https://github.com/cloudfoundry-samples/spring-music)

$ CD spring-音乐

[![Alt text of image](img/18b7e10975c9a74add0e4b80e2524f59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--trG4y87b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q66uyzzqn514vb73bqkr.png)

**登录 PCF 开发**

[![Alt text of image](img/a29853ff4d6d69c3275529fd72b98e00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--plgN6ZRO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zi3qfky6m740h1h2nngx.png)

**使用 Gradle 在本地组装应用**

$ ./gradle 汇编

[![Alt text of image](img/22f4ae09175aeb478606213251b91acc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ljz2GXj3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z7lebir6gwoxhpbe0f5e.png)

**推送 App**

$ cf 推送-主机名 spring-音乐

[![Alt text of image](img/eb5e871cd5ef9810dbcfa45725a50aeb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cY_gMjTN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aouivl5fxwhgmnr7t2zk.png)

部署应用程序时，您将看到从本地浏览器访问应用程序的路径。

[![Alt text of image](img/42799c2e5fa61e31d91b615831e79e92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U0JSXlpP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nno5dmzfk8be1aqsnkwd.png)

请求状态:已启动
实例:1/1
使用量:512M x 1 实例
路线:spring-music.local.pcfdev.io

[![Alt text of image](img/0fdcc323cff5aea9c913d3777fa3a96a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IMJ2cb0N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bgbg76yaw8yq48lgbycx.png)

**查看日志**

$ cf 日志春季音乐-最近

**查看正在运行的应用程序详情**

[![Alt text of image](img/354a13c9893c0c07b338aeb416512d90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1WOMCM3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5j95pzhs1uqbbgl22ufj.png)