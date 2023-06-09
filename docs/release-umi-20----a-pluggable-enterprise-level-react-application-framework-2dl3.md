# 发行 umi 2.0 -一个可插入的企业级 react 应用程序框架

> 原文：<https://dev.to/sorrycc/release-umi-20----a-pluggable-enterprise-level-react-application-framework-2dl3>

[![](img/82039c1a1c58e33eb9bc812de6739dee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fp9g1X9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://gw.alipayobjects.com/zos/rmsportal/CZcJsXOXCqIbglBjTrWs.png)

[https://位置. org/](https://umijs.org/)

距离 [umi 1.0 发布](https://github.com/sorrycc/blog/issues/64)已经过去半年了。这段时间 umi 做了很多重构和改进，438 次提交，20 个 beta 版本。而今天，我们很高兴地看到 **it 发布了 2.0 版本**，并重新定位为**一个可插拔的企业级 react 应用框架**。

## umi 是什么？

它基于路由，支持 [next.js 类常规路由](https://umijs.org/guide/router.html)，以及各种高级路由功能，如[路由级按需加载](https://umijs.org/en/plugin/umi-plugin-react.html#dynamicimport)。然后有了一个完整的[插件系统](https://umijs.org/plugin/)，覆盖了从源代码到构建产品的每个生命周期，umi 能够支持各种功能扩展和业务需求，目前 umi 在社区和公司内部有将近 50 多个插件。

umi 是[蚂蚁金服](https://www.antfin.com/)的基础前端框架，已经直接或间接服务了 600+个应用，包括 java、node、移动 app、混合 app、纯前端资产 app、CMS app 等等。umi 已经很好地服务了我们的内部用户，希望他也能很好地服务外部用户。

它具有以下特点:

*   📦**开箱即用**，内置 react、react-router 等
*   🏈 **Next.js like 和[全功能](https://umijs.org/guide/router.html)路由约定**，也支持配置路由
*   🎉**完整的插件系统**，覆盖从源代码到产品的每个生命周期
*   🚀**高性能**，通过插件支持 PWA、路由级代码拆分等
*   💈**支持静态导出**，适应各种环境，如主机 app、手机 app、[彩蛋](https://github.com/eggjs/egg)、支付宝钱包等
*   🚄**快速开发启动**，支持启用 [dll](https://umijs.org/plugin/umi-plugin-react.html#dll) 和[hard-source-web pack-plugin](https://umijs.org/plugin/umi-plugin-react.html#hardSource)带配置
*   🐠**兼容 IE9** ，基于 [umi-plugin-polyfills](https://umijs.org/plugin/umi-plugin-react.html#polyfills)
*   🍁**支持打字稿**，包括 d.ts 定义和`umi test`
*   🌴**与[dva](https://dvajs.com/)T3】深度集成，支持 duck 目录、模型自动加载、代码拆分等**

## 2.0 有什么新功能？

### 轻芯，初学者友好

umi@1 内置了很多优化，比如按需编译、按需加载、eslint、pwa、antd 等。这些解决方案可以改善开发体验和生产代码，还可以提高学习 umi 的成本。

所以 umi@2 默认关闭了很多优化:

*   按需编译
*   按需加载
*   服务人员
*   antd
*   ...

这些特性由插件实现，插件可以根据需要打开，以确保 umi 内核的轻量级。同时，默认构建只生成 index.html、umi.js 和 umi.css，对初学者更友好。

### 新增外挂系统

umi@1 的插件系统有点太强大了，什么都可以做，都可以修改。于是 umi@2 重构了插件机制，**做了很多约束，明确了什么能做，什么不能做，**并提供了更友好的[插件 API](https://umijs.org/plugin/develop.html) 。

同时，这个插件系统已经在我们公司内部得到了验证。由 30 多个插件组成的优秀内部框架 Bigfish 很好地服务了蚂蚁金服，包括 bacon、用 java/node 部署、性能、服务集成、权限等功能。

### umi 插件试剂

umi@1 的插件有点松。使用 umi 时通常需要安装多个插件。升级使用比较麻烦，所以我们提供 umi-plugin-react。umi-plugin-react 是一个插件的集合，类似于巴别塔中预置的概念。

目前，umi-plugin-react 中有 13 个插件，包括:

*   Dva 集成
*   Antd 集成
*   路线修改
*   IE(9/10/11)兼容性
*   常规 i18n 溶液
*   开关对 preact 或其他库作出反应
*   路由级代码拆分，级别可定制
*   使用 dll 提升开发
*   使用[hard-source-web pack-plugin](https://github.com/mzgoddard/hard-source-webpack-plugin)增强开发
*   启用 PWA
*   启用高清解决方案
*   启用快速点击
*   支持标题确认

详情请查看 https://umijs.org/plugin/umi-plugin-react.html。

### [webpack@4](https://medium.com/webpack/webpack-4-released-today-6cdb994702d4) + [巴别塔@7](https://babeljs.io/blog/2018/08/27/7.0.0) +...

我们把主要的依赖项升级到了最新的，比如 webpack@4，babel@7，less@3，postcss@7，typescript@3 等。，以及相关的构建性能改进。

并且，我们使用 [webpack-chain](https://github.com/mozilla-neutrino/webpack-chain) 来扩展 webpack 配置，这是一个比之前的`webpack.config.js`更稳定的解决方案。

例如

```
export default {
  chainWebpack(config, { webpack }) {
    // Set alias
    config.resolve.alias.set('a', 'path/to/a');

    // Remove the default progress bar
    config.plugins.delete('progress');
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

### `umi generate`

umi@2 支持`umi generate`(别名`umi g`)命令快速生成文件。

```
$ umi g page index 
```

Enter fullscreen mode Exit fullscreen mode

并且，`umi generate`是可扩展的。例如，umi-plugin-dva 扩展了键`dva:model`，所以我们可以用`umi g dva:model global`生成 dva 的模型。

### 其他改进

*   通过`.env`和`.env.local`配置环境变量
*   支持通过`yaml`格式的注释扩展传统路由的路由信息
*   改进的 umi 开发 404 页面
*   用 react-loadable 重构`umi/dynamic`
*   改进`umi test`，允许您指定测试文件，并将参数传递给 jest-cli
*   改进`umi test`，支持 webpack 别名
*   改进`umi dev`，支持配置 https 证书
*   改进`umi dev`，进行样式提取，然后使用 css-hot-loader 进行热更新，与`umi build`一致
*   默认启用 [es5ImcompatibleVersions](https://github.com/umijs/es5-imcompatible-versions)
*   删除大性能消费者区分大小写路径 webpack 插件
*   添加指向`src`目录的 webpack 别名`@`
*   如果找到，使用本地 umi 运行

## 入门

入门很容易，

```
# Install deps
$ yarn global add umi # OR npm install -g umi

# Create application
$ mkdir myapp && cd myapp

# Create page
$ umi generate page index

# Start dev server
$ umi dev

# Build and deploy
$ umi build 
```

Enter fullscreen mode Exit fullscreen mode

查看[https://umijs.org/guide/getting-started.html](https://umijs.org/guide/getting-started.html)了解更多细节，或者观看 [10 分钟入境 umi 视频版](https://www.youtube.com/watch?v=vkAUGUlYm24)。

## 从 umi@1 迁移

目前， [antd-pro](https://github.com/ant-design/ant-design-pro) 和 [antd-admin](https://github.com/zuiidea/antd-admin) 已经升级为 [umi@2](mailto:umi@2) 。如果要升级，请查看[文档](https://umijs.org/guide/migration.html)或[视频](https://youtu.be/1mvKzFLLBck)。

## 社区

### 电报组

[https://t.me/joinchat/G0DdHw-44FO7Izt4K1lLFQ](https://t.me/joinchat/G0DdHw-44FO7Izt4K1lLFQ)

## 终于

感谢所有参与贡献 umi 和在项目中使用 umi 的内部和外部人员。umi 才刚刚起步，希望大家一起提高，Github 见！👋