# Flyimg: Dockerized PHP7 应用程序作为微服务运行，可以动态调整图像大小和裁剪图像。

> 原文：<https://dev.to/sadok/flyimg-dockerized-php7-application-runs-as-a-microservice-to-resize-and-crop-images-on-the-fly-h3b>

[![Flyimg](../Images/3843e33452f6c5a89267b41316294063.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--naInGNQY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khusz65mk5uoazuw7mtu.png) 
有了响应式图像，今天我们有了一堆前端工具，可以向浏览器交付最合适的图像。但是你仍然需要生成这些图像。

这是我们(开源)对这个问题的看法:飞行[https://github.com/flyimg/flyimg](https://github.com/flyimg/flyimg)

一个微服务，将调整大小，裁剪，变换和压缩图像的飞行

## 它有什么作用

*   从互联网上的任何地方获取图像，并将其存储在本地以供进一步操作。
*   调整图像大小并提供压缩图像(Moz-Jpeg for Jpeg)
*   将图像裁剪到给定的坐标
*   在某些格式之间转换(Webp、JPEG、PNG、GIF)
*   它可以限制从中提取图像进行转换的域或 IP。
*   它是为了方便起见而打包在一起的一堆众所周知的开源项目。
*   它包括面部检测，裁剪，面部模糊，图像旋转和许多其他选项。
*   简单且易于扩展(我们的主要目标)。

凭借 Docker 的魔力，你可以在 10 分钟内在 AWS 或 Digital Ocean 中以最便宜的服务器运行一个程序。

当然有上百种服务会让你花钱去做。也有一些开源项目可以做很多事情，很多有更多的功能，但是它们很难设置和使用。在许多情况下，你需要以非常特殊的方式编译 C 库...然后处理服务器的配置...

## 基本用法

调整下图大小:[https://www . Mozilla . org/media/img/logos/Firefox/logo-quantum-high-RES . CFD 87 A8 f 62 AE . png](https://www.mozilla.org/media/img/logos/firefox/logo-quantum-high-res.cfd87a8f62ae.png)

您调用 img.service 并在 URL 中给它一些参数，如下所示:

[https://oi . flyimg . io/upload/w _ 333，h_200，q _ 90/https://www . Mozilla . org/media/img/logos/Firefox/logo-quantum-high-RES . CFD 87 A8 f 62 AE . png](https://oi.flyimg.io/upload/w_333,h_200,q_90/https://www.mozilla.org/media/img/logos/firefox/logo-quantum-high-res.cfd87a8f62ae.png)

->这将创建一个适合 333x200 矩形的图像版本:
[![](../Images/c7924ea036e0e94fa13d63f644779614.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SeNayELg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://oi.flyimg.io/upload/w_333%2Ch_200%2Cq_90/https://www.mozilla.org/media/img/logos/firefox/logo-quantum-high-res.cfd87a8f62ae.png)

### 人脸检测裁剪:

[https://oi . fly img . io/upload/fc _ 1/https://oi . fly img . io/faces . jpg](https://oi.flyimg.io/upload/fc_1/https://oi.flyimg.io/faces.jpg)

[![face crop](../Images/b3c0c8da59c5b1e491ed68266a890a7d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7MayC5Vl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://oi.flyimg.io/upload/fc_1/https://oi.flyimg.io/faces.jpg)

### 人脸检测模糊+调整大小:

[https://oi . fly img . io/upload/FB _ 1，w _ 600/https://oi . fly img . io/faces . jpg](https://oi.flyimg.io/upload/fb_1,w_600/https://oi.flyimg.io/faces.jpg)

[![face blur](../Images/72dbf6e503698339a6c4f361d59d033a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qBPmgrzQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://oi.flyimg.io/upload/fb_1%2Cw_600/https://oi.flyimg.io/faces.jpg)