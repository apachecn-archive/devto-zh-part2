# 用于前端 Web 开发的带注释的 webpack 4 配置

> 原文：<https://dev.to/gaijinity/an-annotated-webpack-4-config-for-frontend-web-development-1p36>

# 用于前端 Web 开发的带注释的 webpack 4 配置

### 随着 web 开发变得越来越复杂，我们需要工具来帮助我们构建现代网站。这是一个完整的 sophis ti ted web pack 4 配置的真实生产示例

安德鲁·韦尔奇

[![Webpack 4 Annotated Config](../Images/24b71841c7d99a735a3954dbc0139757.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FZ2NfTb---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/2176/webpack-4-annotated-config.jpg)

建立一个现代化的网站已经成为应用程序开发的重点。网站被期望做的不仅仅是标记网站，因为它们承担了传统应用的功能。

每当一个过程变得复杂时，我们将它分解成可管理的组件，并自动将构建过程与工具匹配。无论我们是在修车、起草法律文件，还是在建网站，情况都是如此。

<aside>Use the right tool for the job</aside>

像 [web pack](http://webpack.js.org/) 这样的工具一直处于现代 web 开发的前沿，因为它们帮助我们构建复杂的东西。

web pack 4 拥有一些令人惊叹的改进，最吸引我的是它的构建速度有多快。所以我决定收养它。

[![Buckle Up I Want To Try Something](../Images/f6ba996547d0cb7dad63015f8ba8d9ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ecvRhUnH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_100_line/buckle-up-i-want-to-try-something.jpg)

振作起来，因为这是一篇充满大量信息的长篇文章。

## 采用网页包

一年多以前，我发表了一篇关于前端开发自动化的 Gulp 工作流的文章，展示了如何使用 Gulp 来完成同样的事情。然而，在这段时间里，我越来越多地使用前端框架作品，如 [Vue JS](http://vuejs.org/) 和 [GraphQL](http://graphql.org/) ，正如在[文章中讨论的使用 Vue JS + GraphQL 制作实用 Mag](https://dev.to/gaijinity/using-vuejs-graphql-to-make-practical-magic-1520-temp-slug-9813575)。

我发现 web pack 使我更容易构建我现在正在制作的网站和应用程序，它还允许我使用最现代的工具链。

还有其他选择:

*   **[Lar avel Mix](http://laravel.com/docs/5.7/mix)** 是 web pack 上面的一层。它的吸引力在于它的简单性:你可以很快起床跑步，它会在 90%的时间里做你想做的事情。但剩下 10%意味着网络包装的份额会下降。
*   如果你除了想做什么都不做，vue-cli 很有吸引力。它又是 web pack 之上的一层，大多数时候都工作得很好，并且为您做了一些令人惊奇的事情。但是，同样，当你的需求与 web pack 所提供的不一致时，你需要使用 web pack。而且我也不是一直只用 Vue JS。
*   **[Neu tri no](http://neutrinojs.org/)** 是我们在 [Neu tri no:我如何学会停止工作并爱上网络包](http://devmode.fm/episodes/neutrino-how-i-learned-to-stop-worrying-and-love-webpack) pod cast 中探索的一个有趣的网络包。前提是令人惊讶的，建立一个网络包配置，由 snap ping 一起预制乐高积木组件。但是学习它是如何工作的似乎和学习 web pack 本身一样多。

如果你选择了上面的任何一个工具(或者其他什么)，我不会责怪你，但是请注意，它们都有一个共同的主题:它们位于 webpack 之上。

<aside>Under­stand­ing how the lay­ers in your devel­op­ment sys­tem work pays div­i­dends in the end</aside>

最后，你只需要决定你想站在前沿技术的金字塔的中间。

在某种程度上，我认为理解像 web pack 这样重要的工具是如何工作的是有意义的。不久前，我向肖恩·拉金(web pack 核心团队成员之一)解释说，web pack 就像一个“黑匣子”。他的回答简明扼要，但相当尖锐:

<aside>It’s only black if you haven’t opened it</aside>

他是对的。是时候打开盒子了。

本文不会教你所有关于 web pack 的知识，甚至不会教你如何安装它。有大量的资源可供选择——选择你最擅长的资源:

*   **[web pack —组成部分](https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9)** —对 web pack 如何工作有一个准确的概述
*   ——如果你通过阅读学得最好
*   **[【网包趣事】](http://frontendmasters.com/courses/webpack-fundamentals/)** —如果你看着学效果最好
*   **[如何从吞咽切换到网络包](http://www.valentinog.com/blog/from-gulp-to-webpack-4-tutorial/)**——如果你是从那里来的话

…还有很多很多。取而代之的是，本文将给出一个完整的工作示例，展示一个相当不错的 sophis ti-cated web pack 4 的设置。你可以全部使用；你可以用它的一点一滴。但是希望你能从中学到一些东西。

在我不断学习 web pack 的过程中，我发现了许多完整的视频、一堆展示如何安装它的文章和一个基本配置，但没有太多 web pack 配置的实际生产示例。所以我们来了。

## 我们从箱子里拿出什么

当我打开盒子开始学习 web pack 时，我有一个我所依赖的技术列表，我希望 ed 成为构建过程的一部分。我还花时间四处看看，看看在这个过程中还有什么我可以采纳的。

正如在[文章](https://dev.to/gaijinity/a-pretty-website-isn-8217-t-enough-1nbj-temp-slug-3932740)中所讨论的，网站性能是不够的，网站性能一直是我关心的一个重要问题，所以在本 web pack 配置中也关注这一点并不令人惊讶。

[![Webpack Black Box](../Images/5e0804d82fb302f91d8acd091d3c2d08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---G-B5b9A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/webpack-black-box.jpg)

下面是我的观点，我希望 ed web pack 为我做的事情，以及我希望 ed 在我的构建过程中包含的技术:

*   **[开发/生产](http://webpack.js.org/guides/production/)** —在本地开发中，我希望通过内存 [web pack-dev-serv er](http://webpack.js.org/configuration/dev-server/) 快速构建，对于生产构建(通常通过 [buddy.works](http://buddy.works/) 在 Dock 容器中完成)，我希望每个位置都优化。因此，我们有单独的速率`dev`和`prod`配置&构建。
*   —当我修改我的 JavaScript、CSS 或 tem 模板时，我希望网页不要刷新。这极大地加快了开发速度:只需对重载按钮说不。
*   **[动态代码分割](http://webpack.js.org/guides/code-splitting/#dynamic-imports)** —我不想总是在配置文件中定义 JavaScript 块，我想让 web pack 帮我解决这个问题。
*   —又名异步动态模块加载。只在需要的时候加载需要的代码/资源，不要阻塞。
*   **[modern&Lega cy JS bundles](http://philipwalton.com/articles/deploying-es2015-code-in-production-today/)**—我希望 ed 将 mod ern ES2015+ JavaScript 模块部署到支持它的 [75%+的全球浏览器](https://caniuse.com/#search=javascript%20modules)，同时 grace ful ly 为合法浏览器提供一个后备的 Lega bundle(带有所有的转换代码和 polyfills)。
*   **[通过 manifest.json](http://webpack.js.org/guides/caching/)** 缓存溢出——这允许我们为我们的静态资产设置一个长期的到期数据，同时也确保它们在发生变化时自动缓存溢出。
*   **[Crit I call CSS](http://developers.google.com/speed/docs/insights/OptimizeCSSDelivery)**—根据[在你的网站上实现 Crit I call CSS 的文章](https://dev.to/gaijinity/implementing-critical-css-on-your-website-37d5-temp-slug-6012917)，这是一些使初始页面加载信号不能更快的东西。
*   **[Work box 服务 er](http://developers.google.com/web/tools/workbox/)** —我们可以利用谷歌的 Work`kbox 项目为我们生成一个[服务 er](http://developers.google.com/web/fundamentals/primers/service-workers/) ，它将了解我们项目的所有资产。我们来了！`
*   **[PostC SS](http://postcss.org/)** —我认为它是“C SS 的巴别塔”，像萨斯和 SCSS 之类的东西都建立在它的基础上，它让你现在就可以使用升级的 CSS 特性。
*   **[图片优化](http://images.guide/)** —图片是目前大多数网页上最大的东西，所以通过`mozjpeg`、`optipng`、`svgo`等自动调整工具来优化它们是有意义的。
*   **[汽车维修站。webp cretion](http://developers.google.com/speed/webp/)**——Chrome、Edge 和 Fire fox 都支持`.webp`，这是一种比 JPEG 更有效的格式。
*   **[Vue JS](http://vuejs.org/)**——Vue JS 是我首选的 fron tend 框架。我希望能够使用单个文件组件作为我开发过程的无缝部分。
*   **[Tail wind CSS](http://tailwindcss.com/docs/what-is-tailwind/)**—Tail wind 是我在本地开发中使用的第一个用于快速打印的 CSS，然后通过[purge SS](http://github.com/FullHuman/purgecss)进行打印，以大幅缩减大小。
*   **[离线压缩统计资源](https://medium.com/@selvaganesh93/how-to-serve-webpack-gzipped-file-in-production-using-nginx-692eadbb9f1c)** —我们可以预压缩我们的统计资源。我们的 web 服务器可以自动将 gz 文件提供给接受它们的客户机

唷，真是一份雄心勃勃的清单！

[![Ambitious Feature List Webpack](../Images/2fe0fa62a67faa73de9582d933208c59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kptslgfR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/ambitious-feature-list-webpack.jpg)

还有更多的东西，比如 JavaScript 的自动变形，CSS 的迷你变形，以及我们期望从前端构建系统中得到的其他标准东西。

我还希望它能与一个开发团队合作，该团队可能会为他们的本地开发环境使用独立的工具，并使配置易于维护和在项目间重用。

<aside>The impor­tance of main­tain­abil­i­ty and reusabil­i­ty can’t be understated</aside>

你的前端框架/技术可能看起来和我的不同，但是应用的原则是一样的。所以继续读下去，不要太在意你所使用的东西！

## 项目树&组织

为了让您对设置有一个大致的了解，这里有一个基本的项目树:

```
 ├── example.env
├── package.json
├── postcss.config.js
├── src
│   ├── css
│   │   ├── app.pcss
│   │   ├── components
│   │   │   ├── global.pcss
│   │   │   ├── typography.pcss
│   │   │   └── webfonts.pcss
│   │   ├── pages
│   │   │   └── homepage.pcss
│   │   └── vendor.pcss
│   ├── fonts
│   ├── img
│   │   └── favicon-src.png
│   ├── js
│   │   ├── app.js
│   │   └── workbox-catch-handler.js
│   └── vue
│   └── Confetti.vue
├── tailwind.config.js
├── templates
├── webpack.common.js
├── webpack.dev.js
├── webpack.prod.js
├── webpack.settings.js
└── yarn.lock 
```

对于这里提交的所有内容的完整源代码，请查看 **[公告-web pack-4-con 图](http://github.com/nystudio107/annotated-webpack-4-config)** github 报告。

就核心配置文件而言，我们有:

*   `**.env**`—envi Ron men tal-SPE cif IC settings for`webpack-dev-server`；这是*从来没有*检入 git
*   `**webpack.settings.js**` —一个 JSON-ish 设置文件，我们需要在项目间编辑的唯一文件
*   `**webpack.common.js**` —两种类型构建的通用设置
*   `**webpack.dev.js**` —设置本地开发版本
*   `**webpack.prod.js**` —为产品构建进行设置

这里有一张图表，展示了所有这些是如何组合在一起的:

[![Webpack Config Text Solid Sm](../Images/681a1f73cd3875b2d8e20cdcfc33cc44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VEzKY6nF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x1315_crop_center-center_100_line/webpack-config-text-solid-sm.png)

这样做的目的是，你只需要编辑项目之间的黄金色标(`.env` & `webpack.settings.js`)中的内容。

以这种方式区分事物使得配置文件的工作变得更加容易。即使您最终改变了我在这里提交的各种 web pack 配置文件，继续使用这种方法将有助于您长期维护它们。

不要说话

## 彦俊 tat ed package.json

让我们开始分解我们的`package.json` :

```
 {
    "name": "example-project",
    "version": "1.1.0",
    "description": "Example Project brand website",
    "keywords": [
        "Example",
        "Keywords"
    ],
    "homepage": "https://github.com/example-developer/example-project",
    "bugs": {
        "email": "someone@example-developer.com",
        "url": "https://github.com/example-developer/example-project/issues"
    },
    "license": "SEE LICENSE IN LICENSE.md",
    "author": {
        "name": "Example Developer",
        "email": "someone@example-developer.com",
        "url": "https://example-developer.com"
    },
    "browser": "/web/index.php",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/example-developer/example-project.git"
    },
    "private": true, 
```

这里没有什么特别感兴趣的，只是我们网站的元信息，如 [package.json 规范说明](http://docs.npmjs.com/files/package.json)中所列。

```
 "scripts": {
    "debug": "webpack-dev-server --config webpack.dev.js",
    "dev": "webpack-dashboard -- webpack-dev-server --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js --progress --hide-modules"
}, 
```

这些脚本代表了我们项目的两个主要构建步骤:

*   **debug** —当你需要调试 web pack 构建本身时使用；这使得`webpack-dashboard`(见下文)更容易到达终点
*   **dev** —在我们进行项目时使用，它加速`webpack-dev-server`以允许热模块替换(HMR)、内存编译和其他细节。
*   **build** —在我们进行产品部署时使用，它完成所有的频率和时间，包括关键的 CSS、JavaScript 的恶意代码等。这是生产部署需要做的。

要运行它们，我们只需使用我们的开发环境中的客户端*来执行`yarn dev`或`yarn build`(如果我们使用的是[线程](http://yarnpkg.com/)，以及`npm run dev`或`npm run build`(如果我们使用的是 [npm](http://www.npmjs.com/) )。这是您需要使用的唯一两个命令。*

注意，通过`--config`标志，我们还传入了 [sep 的一个价格配置文件](http://webpack.js.org/guides/production/)。这使我们能够将 web pack 配置分解成单独的日志调用文件，因为我们将为开发版本做与生产版本非常不同的事情。

接下来我们有我们的**浏览器列表** :

```
 "browserslist": {
        "production": [
            "> 1%",
            "last 2 versions",
            "Firefox ESR"
        ],
        "legacyBrowsers": [
            "> 1%",
            "last 2 versions",
            "Firefox ESR"
        ],
        "modernBrowsers": [
            "last 2 Chrome versions",
            "not Chrome < 60",
            "last 2 Safari versions",
            "not Safari < 10.1",
            "last 2 iOS versions",
            "not iOS < 10.3",
            "last 2 Firefox versions",
            "not Firefox < 54",
            "last 2 Edge versions",
            "not Edge < 15"
        ]
    }, 
```

这是一个[浏览器列表](http://github.com/browserslist/browserslist)，tar 根据人类可读的配置获得特定的 cif ic 浏览器。[post cs 自动修复程序](http://github.com/postcss/autoprefixer)默认使用我们的`production`设置。我们将`legacyBrowsers`和`modernBrowsers`传递给巴别塔，以处理构建合法的和现代的 JavaScript bundle。稍后将详细介绍这一点！

接下来是我们的**开发包**，这是我们构建系统所需的所有 npm 包:

```
 "devDependencies": {
    "@babel/core": "^7.1.0",
    "@babel/plugin-syntax-dynamic-import": "^7.0.0",
    "@babel/plugin-transform-runtime": "^7.1.0",
    "@babel/preset-env": "^7.1.0",
    "@babel/register": "^7.0.0",
    "@babel/runtime": "^7.0.0",
    "@gfx/zopfli": "^1.0.11",
    "babel-loader": "^8.0.2",
    "clean-webpack-plugin": "^3.0.0",
    "compression-webpack-plugin": "^2.0.0",
    "copy-webpack-plugin": "^4.5.2",
    "create-symlink-webpack-plugin": "^1.0.0",
    "critical": "^1.3.4",
    "critical-css-webpack-plugin": "^0.2.0",
    "css-loader": "^2.1.0",
    "cssnano": "^4.1.0",
    "dotenv": "^6.1.0",
    "file-loader": "^2.0.0",
    "git-rev-sync": "^1.12.0",
    "glob-all": "^3.1.0",
    "html-webpack-plugin": "^3.2.0",
    "ignore-loader": "^0.1.2",
    "imagemin": "^6.0.0",
    "imagemin-gifsicle": "^6.0.0",
    "imagemin-mozjpeg": "^8.0.0",
    "imagemin-optipng": "^6.0.0",
    "imagemin-svgo": "^7.0.0",
    "imagemin-webp": "^5.0.0",
    "imagemin-webp-webpack-plugin": "^3.1.0",
    "img-loader": "^3.0.1",
    "mini-css-extract-plugin": "^0.4.3",
    "moment": "^2.22.2",
    "optimize-css-assets-webpack-plugin": "^5.0.1",
    "postcss": "^7.0.2",
    "postcss-import": "^12.0.0",
    "postcss-loader": "^3.0.0",
    "postcss-preset-env": "^6.4.0",
    "purgecss-webpack-plugin": "^1.3.0",
    "purgecss-whitelister": "^2.2.0",
    "resolve-url-loader": "^3.0.0",
    "save-remote-file-webpack-plugin": "^1.0.0",
    "stylelint": "^9.9.0",
    "stylelint-config-recommended": "^2.1.0",
    "style-loader": "^0.23.0",
    "symlink-webpack-plugin": "^0.0.4",
    "terser-webpack-plugin": "^1.1.0",
    "vue-loader": "^15.4.2",
    "vue-style-loader": "^4.1.2",
    "vue-template-compiler": "^2.5.17",
    "webapp-webpack-plugin": "https://github.com/brunocodutra/webapp-webpack-plugin.git",
    "webpack": "^4.19.1",
    "webpack-bundle-analyzer": "^3.0.2",
    "webpack-cli": "^3.1.1",
    "webpack-dashboard": "^3.0.0",
    "webpack-dev-server": "^3.3.0",
    "webpack-manifest-plugin": "^2.0.4",
    "webpack-merge": "^4.1.4",
    "webpack-notifier": "^1.6.0",
    "workbox-webpack-plugin": "^3.6.2"
}, 
```

是的，那是相当长的一段时间。但是我们的构建过程*做了*很多。

最后，我们在我们网站的前端使用**依赖项**作为包装材料:

```
 "dependencies": {
    "axios": "^0.18.0",
    "core-js": "^3.0.0",
    "regenerator-runtime": "^0.13.2",
    "tailwindcss": "^1.0.0",
    "vue": "^2.5.17",
    "vue-confetti": "^0.4.2"
} 
```

显然对于一个真实的网站/应用程序，依赖于的**会有更多的包；但是我们关注的是构建过程。**

## 年份统计数据和 webpack.settings.js

我还使用了一种类似的方法，在[一篇关于](https://dev.to/gaijinity/a-better-package-json-for-the-frontend-1gal-temp-slug-5184040)的文章中讨论过，这种方法可以将不同项目之间变化的配置转换成单独的速率`webpack.settings.js`，并保持 web pack 本身的配置不变。

<aside>The key con­cept is that the only file we need to edit from project to project is the webpack.settings.js</aside>

由于大多数项目需要做的事情非常简单，所以我们可以创建一个适用于各种项目的 web pack 配置。我们只需要改变它所操作的数据。

因此，我们的`webpack.settings.js`文件中的内容(项目与项目之间变化的数据)和我们的 web pack 配置中的内容(如何操纵数据以产生最终结果)之间的区别是相关的。

```
 // webpack.settings.js - webpack settings config

// node modules
require('dotenv').config();

// Webpack settings exports
// noinspection WebpackConfigHighlighting
module.exports = {
    name: "Example Project",
    copyright: "Example Company, Inc.",
    paths: {
        src: {
            base: "./src/",
            css: "./src/css/",
            js: "./src/js/"
        },
        dist: {
            base: "./web/dist/",
            clean: [
                '**/*',
            ]
        },
        templates: "./templates/"
    },
    urls: {
        live: "https://example.com/",
        local: "http://example.test/",
        critical: "http://example.test/",
        publicPath: () => process.env.PUBLIC_PATH || "/dist/",
    },
    vars: {
        cssName: "styles"
    },
    entries: {
        "app": "app.js"
    },
    babelLoaderConfig: {
        exclude: [
            /(node_modules|bower_components)/
        ],
    },
    copyWebpackConfig: [
        {
            from: "./src/js/workbox-catch-handler.js",
            to: "js/[name].[ext]"
        }
    ],
    criticalCssConfig: {
        base: "./web/dist/criticalcss/",
        suffix: "_critical.min.css",
        criticalHeight: 1200,
        criticalWidth: 1200,
        ampPrefix: "amp_",
        ampCriticalHeight: 19200,
        ampCriticalWidth: 600,
        pages: [
            {
                url: "",
                template: "index"
            }
        ]
    },
    devServerConfig: {
        public: () => process.env.DEVSERVER_PUBLIC || "http://localhost:8080",
        host: () => process.env.DEVSERVER_HOST || "localhost",
        poll: () => process.env.DEVSERVER_POLL || false,
        port: () => process.env.DEVSERVER_PORT || 8080,
        https: () => process.env.DEVSERVER_HTTPS || false,
    },
    manifestConfig: {
        basePath: ""
    },
    purgeCssConfig: {
        paths: [
            "./templates/**/*.{twig,html}",
            "./src/vue/**/*.{vue,html}"
        ],
        whitelist: [
            "./src/css/components/**/*.{css}"
        ],
        whitelistPatterns: [],
        extensions: [
            "html",
            "js",
            "twig",
            "vue"
        ]
    },
    saveRemoteFileConfig: [
        {
            url: "https://www.google-analytics.com/analytics.js",
            filepath: "js/analytics.js"
        }
    ],
    createSymlinkConfig: [
        {
            origin: "img/favicons/favicon.ico",
            symlink: "../favicon.ico"
        }
    ],
    webappConfig: {
        logo: "./src/img/favicon-src.png",
        prefix: "img/favicons/"
    },
    workboxConfig: {
        swDest: "../sw.js",
        precacheManifestFilename: "js/precache-manifest.[manifestHash].js",
        importScripts: [
            "/dist/js/workbox-catch-handler.js"
        ],
        exclude: [
            /\.(png|jpe?g|gif|svg|webp)$/i,
            /\.map$/,
            /^manifest.*\\.js(?:on)?$/,
        ],
        globDirectory: "./web/",
        globPatterns: [
            "offline.html",
            "offline.svg"
        ],
        offlineGoogleAnalytics: true,
        runtimeCaching: [
            {
                urlPattern: /\.(?:png|jpg|jpeg|svg|webp)$/,
                handler: "CacheFirst",
                options: {
                    cacheName: "images",
                    expiration: {
                        maxEntries: 20
                    }
                }
            }
        ]
    }
}; 
```

我们将在 web pack 配置部分介绍所有这些内容。这里需要注意的重要一点是，我们已经从我们的 web pack 配置中提取了不同项目之间会发生变化的内容，并将其保存到一个单独的文件中。

这意味着我们可以在我们的`webpack.settings.js`文件中定义每个项目的不同之处，而不必纠结于 web pack 配置本身。

尽管`webpack.settings.js`文件只是 JavaScript，我还是试图让它尽可能的 JSON 化，所以我们只是改变了其中的简单设置。我没有使用 JSON 作为 mat for flex bility 的文件，也没有允许添加注释。

## web pack 配置的常见配置

我为网络包配置文件`webpack.common.js` & `webpack.prod.js`采纳了一些建议，以使事情更加一致。

每个配置文件有两个内部配置:

*   **合法配置** —适用于合法版本 5 的配置
*   **现代配置** —适用于现代 ES2015+版本的配置

我们这样做是因为我们有单独的比率配置来创建传统和现代版本。这使他们以很快的速度记录。在图的基础上`webpack.common.js`也有一个**；这纯粹是组织性的。**

你可以把它想象成一个面向对象的程序，其中各种不同的配置包含在其中，基于图的**是根对象。**

该配置没有合法&现代版本的概念；如果我们用`webpack-dev-server`在本地开发中工作，我们可以假设一个现代版本。

我采用的保持配置整洁和可读的另一个建议是为各种 web pack 插件和其他需要配置的 web pack 组件提供`configure()`功能，而不是把它们都放在内联中。

我这样做是因为来自`webpack.settings.js`的一些数据在被 web pack 使用之前需要被转换，并且因为双重的传统/现代版本，我们需要根据版本类型返回不同的配置。

这也使配置文件更容易阅读。

作为一个通用的 web pack 概念，web pack 本身只知道如何加载 JavaScript 和 JSON。要加载任何其他东西，我们需要使用一个[加载器](http://webpack.js.org/concepts/#loaders)。我们将在 web pack 配置中使用许多不同的加载器。

## 年份统计和网页包. common.js

现在让我们来看看我们的`webpack.common.js`配置文件，它包含了所有由`dev`和`prod`构建类型共享的设置。

```
 // webpack.common.js - common webpack config
const LEGACY_CONFIG = 'legacy';
const MODERN_CONFIG = 'modern';

// node modules
const path = require('path');
const merge = require('webpack-merge');

// webpack plugins
const CopyWebpackPlugin = require('copy-webpack-plugin');
const ManifestPlugin = require('webpack-manifest-plugin');
const VueLoaderPlugin = require('vue-loader/lib/plugin');
const WebpackNotifierPlugin = require('webpack-notifier');

// config files
const pkg = require('./package.json');
const settings = require('./webpack.settings.js'); 
```

在这个过程中，我们引入了我们需要的节点包和我们使用的 web 包插件。然后，我们将我们的`webpack.settings.js`作为`settings`导入，这样我们就可以访问那里的设置，也可以将我们的`package.json`作为`pkg`导入，以访问那里的一些设置。

**配置功能**

下面是`configureBabelLoader()`的样子:

```
 // Configure Babel loader
const configureBabelLoader = (browserList) => {
    return {
        test: /\.js$/,
        exclude: settings.babelLoaderConfig.exclude,
        cacheDirectory: true,
        use: {
            loader: 'babel-loader',
            options: {
                cacheDirectory: true,
                sourceType: 'unambiguous',
                presets: [
                    [
                        '@babel/preset-env', {
                            modules: false,
                            corejs: {
                                version: 2,
                                proposals: true
                            },
                            useBuiltIns: 'usage',
                            targets: {
                                browsers: browserList,
                            },
                        }
                    ],
                ],
                plugins: [
                    '@babel/plugin-syntax-dynamic-import',
                    '@babel/plugin-transform-runtime',
                ],
            },
        },
    };
}; 
```

`configureBabelLoader()`功能配置`babel-loader`来处理所有以`.js`结尾的文件的加载。它使用了 [@babel/preset-env](http://babeljs.io/docs/en/babel-preset-env) 而不是`.babelrc`文件，所以我们可以在我们的 web pack 配置中保持所有的组件。

Babel 可以将现代的 ES2015+ JavaScript(以及许多其他语言，如 Type Script 或 Cof fee Script)编译成 JavaScript，以便 tar 获得一组特定的浏览器或标准。我们将`browserList`作为一个参数传入，这样我们就可以用多边形填充为合法浏览器构建[现代 ES2015+模块和合法 ES5 JavaScript](http://philipwalton.com/articles/deploying-es2015-code-in-production-today/) 。

通过将 ting `useBuiltIns`设置为`'usage'`，我们还告诉 babel 在每个文件的基础上应用单独的策略填充。这可以允许更小的尺寸，因为它只包括我们所使用的。关于这方面的更多信息，请查看[使用巴别塔 7 和 Web pack](https://www.thebasement.be/working-with-babel-7-and-webpack/#a-cleaner-approach) 的文章。

在我们的 HTML 中，我们只是做一些这样的事情:

```
 <!-- Browsers with ES module support load this file. -->
<script type="module" src="main.js"></script>

<!-- Older browsers load this file (and module-supporting -->
<!-- browsers know *not* to load this file). -->
<script nomodule src="main-legacy.js"></script> 
```

没有聚填充，没有大惊小怪。旧的浏览器会忽略`type="module"`脚本，并获取`main-legacy.js`。现代浏览器加载`main.js`，忽略`nomodule`。是 bril liant 我希望是我想出的主意！以免你认为这是边缘，vue-cli 在第 3 版中采用了这个策略。

甚至在网络浏览器实现 [ECMAScript 动态导入 pro pos](http://github.com/tc39/proposal-dynamic-import)之前，[@ babel/plugin-syntax-dynamic-import](http://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import)插件就允许我们进行动态导入。这让我们可以根据需要异步加载我们的 JavaScript 模块[和动态模块](http://webpack.js.org/guides/code-splitting/#dynamic-imports)。

那么这意味着什么呢？这意味着我们可以这样做

```
 // App main
const main = async () => {
    // Async load the vue module
    const { default: Vue } = await import(/* webpackChunkName: "vue" */ 'vue');
    // Create our vue instance
    const vm = new Vue({
        el: "#app",
        components: {
            'confetti': () => import(/* webpackChunkName: "confetti" */ '../vue/Confetti.vue'),
        },
    });

    return vm;
};
// Execute async function
main().then( (vm) => {
});
// Accept HMR as per: https://webpack.js.org/api/hot-module-replacement#accept
if (module.hot) {
    module.hot.accept();
} 
```

这主要做两件事:

1.  通过`/* webpackChunkName: "vue" */`注释，我们已经告诉 web pack 我们希望这个动态代码分割块被命名为什么
2.  因为我们在一个`async`函数(“`main`”)中使用了`import()`，所以该函数`await`是我们动态加载 JavaScript 导入的结果，而我们的其余代码继续以它的方式运行

我们已经有效地告诉 web pack 我们希望通过代码而不是通过配置和通过`@babel/plugin-syntax-dynamic-import`的 mag ic 来拆分我们的块，这个 JavaScript 块可以按需异步加载。

注意，我们对单个文件组件也做了同样的事情。很好。

除了使用`await`，我们还可以在`import()` [承诺](http://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)返回后执行我们的代码:

```
 // Async load the vue module
import(/* webpackChunkName: "vue" */ 'vue').then(Vue => {
    // Vue has loaded, do something with it
    // Create our vue instance
    const vm = new Vue.default({
        el: "#app",
        components: {
            'confetti': () => import(/* webpackChunkName: "confetti" */ '../vue/Confetti.vue'),
        },
    });
}); 
```

在这里，我们没有将`await`与 [import()](http://webpack.js.org/api/module-methods/#import-) 一起使用，而是使用了 Promise，这样我们就知道动态导入已经开始，并且可以使用`Vue`。

如果你注意的话，你会发现我们已经通过 Promis es 有效地解决了 JavaScript 依赖问题。不错！

我们甚至可以做一些有趣的事情，比如只有在用户点击了某个东西，滚动到某个位置，或者满足了其他条件之后，才加载某些 JavaScript 块。查看[模块方法导入()](http://webpack.js.org/api/module-methods/#import-)了解更多信息。

如果你有兴趣了解更多关于巴别塔的知识，请查看文章[使用巴别塔 7 和网络包](http://www.thebasement.be/working-with-babel-7-and-webpack/)。

接下来我们有`configureEntries()` :

```
 // Configure Entries
const configureEntries = () => {
    let entries = {};
    for (const [key, value] of Object.entries(settings.entries)) {
        entries[key] = path.resolve(__dirname, settings.paths.src.js + value);
    }

    return entries;
}; 
```

在这里，我们通过`settings.entries`从`webpack.settings.js`拉入 web 包[入口点](http://webpack.js.org/concepts/entry-points/)。对于一个[单页面应用](https://medium.com/@pshrmn/demystifying-single-page-applications-3068d0555d46) (SPA)来说，你只有一个入口。对于一个更传统的网站，你可能有几个入口点(每个页面模板一个)。

不管怎样，因为我们已经在`webpack.settings.js`中定义了入口点，所以很容易在那里配置它们。入口点实际上只是一个`<script src="app.js"></script>`标签，您将把它包含在 HTML 中以引导 JavaScript。

因为我们使用动态导入模块，我们通常在一个页面上只有一个`<script></script>`标签；我们 JavaScript 的其余部分根据需要动态加载。

接下来我们有`configureFontLoader()`函数:

```
 // Configure Font loader
const configureFontLoader = () => {
    return {
        test: /\.(ttf|eot|woff2?)$/i,
        use: [
            {
                loader: 'file-loader',
                options: {
                    name: 'fonts/[name].[ext]'
                }
            }
        ]
    };
}; 
```

字体加载对于`dev`和`prod`版本是相同的，所以我们在这里包括它。对于我们正在使用的任何本地字体，我们可以告诉 web pack 将它们加载到我们的 JavaScript:

```
 import comicsans from '../fonts/ComicSans.woff2'; 
```

接下来我们有`configureManifest()`函数:

```
 // Configure Manifest
const configureManifest = (fileName) => {
    return {
        fileName: fileName,
        basePath: settings.manifestConfig.basePath,
        map: (file) => {
            file.name = file.name.replace(/(\.[a-f0-9]{32})(\..*)$/, '$2');
            return file;
        },
    };
}; 
```

这配置了中的 [web pack-man，用于基于文件名的缓存溢出。简而言之，web pack 知道所有的 JavaScript、CSS 和我们需要的其他资源，所以它可以生成一个指向资源的内容散列名称的文件，例如:](http://github.com/danethurber/webpack-manifest-plugin) 

```
 {
  "vendors~confetti~vue.js": "/dist/js/vendors~confetti~vue.03b9213ce186db5518ea.js",
  "vendors~confetti~vue.js.map": "/dist/js/vendors~confetti~vue.03b9213ce186db5518ea.js.map",
  "app.js": "/dist/js/app.30334b5124fa6e221464.js",
  "app.js.map": "/dist/js/app.30334b5124fa6e221464.js.map",
  "confetti.js": "/dist/js/confetti.1152197f8c58a1b40b34.js",
  "confetti.js.map": "/dist/js/confetti.1152197f8c58a1b40b34.js.map",
  "js/precache-manifest.js": "/dist/js/precache-manifest.f774c437974257fc8026ca1bc693655c.js",
  "../sw.js": "/dist/../sw.js"
} 
```

我们传入一个文件名，因为我们创建了一个 moden`manifest.json`和一个 lega cy `manifest-legacy.json`，它们分别具有我们的 moden es 2015+模块和 lega cy ES5 模块的入口点。这两个活动中的关键字都是为现代和传统版本构建的资源所标识的。

接下来，我们将看到一个典型的标准

```
 // Configure Vue loader
const configureVueLoader = () => {
    return {
        test: /\.vue$/,
        loader: 'vue-loader'
    };
}; 
```

这让我们可以轻松地加载单个文件组件。web pack 负责为您提取合适的 HTML、CSS 和 JavaScript。

**基础图标**

`baseConfig`与`modernConfig`和`legacyConfig` :
合并

```
 // The base webpack config
const baseConfig = {
    name: pkg.name,
    entry: configureEntries(),
    output: {
        path: path.resolve(__dirname, settings.paths.dist.base),
        publicPath: settings.urls.publicPath()
    },
    resolve: {
        alias: {
            'vue$': 'vue/dist/vue.esm.js'
        }
    },
    module: {
        rules: [
            configureVueLoader(),
        ],
    },
    plugins: [
        new WebpackNotifierPlugin({title: 'Webpack', excludeWarnings: true, alwaysNotify: true}),
        new VueLoaderPlugin(),
    ]
}; 
```

这里的每样东西都是 pret ty stan dard web pack fare，但是请注意，我们将`vue$`别名为`vue/dist/vue.esm.js`，以便我们可以获得 Vue 的 ES2015 模块版本。

我们使用中的[Web pack kNo tifier plug 让我们以一种友好的方式了解我们构建的状态。](http://www.npmjs.com/package/webpack-notifier)

**合金 CY CONFIG**

`legacyConfig`用于使用适当的聚合填充:
构建 ES5 合法 JavaScript

```
 // Legacy webpack config
const legacyConfig = {
    module: {
        rules: [
            configureBabelLoader(Object.values(pkg.browserslist.legacyBrowsers)),
        ],
    },
    plugins: [
        new CopyWebpackPlugin(
            settings.copyWebpackConfig
        ),
        new ManifestPlugin(
            configureManifest('manifest-legacy.json')
        ),
    ]
}; 
```

注意，我们将`pkg.browserslist.legacyBrowsers`传递给`configureBabelLoader()`，将 `'manifest-legacy.json'`传递给`configureManifest()`。

我们还在这个版本中包含了中的[复制 Web pack Plu g，这样我们只复制一次`settings.copyWebpackConfig`中定义的文件。](http://github.com/webpack-contrib/copy-webpack-plugin)

**修改核心配置**

`modernConfig`用于构建没有 cruft:
的现代 ES2015 JavaScript 模块

```
 // Modern webpack config
const modernConfig = {
    module: {
        rules: [
            configureBabelLoader(Object.values(pkg.browserslist.modernBrowsers)),
        ],
    },
    plugins: [
        new ManifestPlugin(
            configureManifest('manifest.json')
        ),
    ]
}; 
```

注意，我们将`pkg.browserslist.modernBrowsers`传递给`configureBabelLoader()`，将`'manifest.json'`传递给`configureManifest()`。

**模块。出口**

最后，`module.exports`使用 [web pack-merge](http://www.npmjs.com/package/webpack-merge) 包将配置合并在一起，并返回一个对象供`webpack.dev.js`和`webpack.prod.js`使用。

```
 // Common module exports
// noinspection WebpackConfigHighlighting
module.exports = {
    'legacyConfig': merge.strategy({
        module: 'prepend',
        plugins: 'prepend',
    })(
        baseConfig,
        legacyConfig,
    ),
    'modernConfig': merge.strategy({
        module: 'prepend',
        plugins: 'prepend',
    })(
        baseConfig,
        modernConfig,
    ),
}; 
```

## 年完成和 webpack.dev.js

现在让我们来看看我们的`webpack.dev.js`配置文件，它包含了我们在项目工作时用于开发人员构建的所有设置。它与`webpack.common.js`中的设置合并，形成一个完整的网络包配置。

```
 // webpack.dev.js - developmental builds

// node modules
const merge = require('webpack-merge');
const path = require('path');
const webpack = require('webpack');

// webpack plugins
const DashboardPlugin = require('webpack-dashboard/plugin');

// config files
const common = require('./webpack.common.js');
const pkg = require('./package.json');
const settings = require('./webpack.settings.js'); 
```

在`webpack.dev.js`配置中，没有现代&合法版本的概念，因为在本地开发中，当我们使用`webpack-dev-server`时，我们可以假设一个现代版本。

在这个过程中，我们再次引入我们需要的节点包和我们使用的 web 包插件。然后，我们将我们的`webpack.settings.js`作为`settings`导入，这样我们就可以访问那里的设置，也可以将我们的`package.json`作为`pkg`导入，以访问那里的一些设置。

我们还导入我们的`webpack.common.js` com mon web pack 配置，我们将合并我们的开发设置。

**配置功能**

下面是`configureDevServer()`的样子:

```
 // Configure the webpack-dev-server
const configureDevServer = () => {
    return {
        public: settings.devServerConfig.public(),
        contentBase: path.resolve(__dirname, settings.paths.templates),
        host: settings.devServerConfig.host(),
        port: settings.devServerConfig.port(),
        https: !!parseInt(settings.devServerConfig.https()),
        disableHostCheck: true,
        hot: true,
        overlay: true,
        watchContentBase: true,
        watchOptions: {
            poll: !!parseInt(settings.devServerConfig.poll()),
            ignored: /node_modules/,
        },
        headers: {
            'Access-Control-Allow-Origin': '*'
        },
    };
}; 
```

当我们进行产品构建时，web pack 会打包我们所有的各种资产，并将它们保存到文件系统中。相比之下，当我们在本地开发中进行项目时，我们使用通过 [web pack-dev-serv er](http://github.com/webpack/webpack-dev-server) 构建的开发版本:

*   建立一个本地的 [Express](http://expressjs.com/) 网络服务器来服务我们的资产
*   为了提高速度，在内存中而不是在文件系统中构建我们的资产
*   将重建 JavaScript、CSS、Vue 组件等资产。当我们改变它们并通过[热模块替换(HMR)](http://webpack.js.org/concepts/hot-module-replacement/) 将它们注入网页时，无需重新加载页面
*   当我们更改模板时，将重新加载页面

这类似于一个更为复杂的浏览器同步版本，大大加快了开发速度。

注意，`webpack-dev-server`的配置同样来自我们的`webpack.settings.js`文件。对于许多人来说，默认设置可能没问题，但是我使用 [Lar avel Home stead](http://laravel.com/docs/5.7/homestead) 进行本地开发，正如在[Local development with vagger/Home stead](https://dev.to/gaijinity/local-development-with-vagrant-homestead-5gb0-temp-slug-2094211)文章中所讨论的。这意味着我在我的 Home stead 虚拟机中运行所有的开发工具。

因此，`webpack.settings.js`可以从一个可选的`.env`文件中读取您自己的部分`devServer`配置:
，而不是将本地开发环境硬编码到我的`webpack.settings.js`文件中(因为它会因团队中工作的每个人而异)

```
 # webpack example settings for Homestead/Vagrant
PUBLIC_PATH="/dist/"
DEVSERVER_PUBLIC="http://192.168.10.10:8080"
DEVSERVER_HOST="0.0.0.0"
DEVSERVER_POLL=1
DEVSERVER_PORT=8080
DEVSERVER_HTTPS=0 
```

您可能会使用一些不同的东西，所以根据需要适当地更改您的`.env`文件中的设置。在 [dotenv](http://www.npmjs.com/package/dotenv) 背后的想法是，我们把任何东西放到环境的`.env`文件中，并且我们**不**把它签入我们的 git repo。如果`.env`文件不存在，也没关系，它只是使用默认值:

```
 devServerConfig: {
    public: () => process.env.DEVSERVER_PUBLIC || "http://localhost:8080",
    host: () => process.env.DEVSERVER_HOST || "localhost",
    poll: () => process.env.DEVSERVER_POLL || false,
    port: () => process.env.DEVSERVER_PORT || 8080,
    https: () => process.env.DEVSERVER_HTTPS || false,
},
urls: {
    live: "https://example.com/",
    local: "http://example.test/",
    critical: "http://example.test/",
    publicPath: () => process.env.PUBLIC_PATH || "/dist/",
}, 
```

我们也使用`PUBLIC_PATH`。环境变量(如果有)允许生产构建的环境构建。这样我们就可以进行本地产品构建，或者在 Dock container 中进行分发产品构建，该 Dock container 构建的 URL 已准备好通过 CDN 进行分发。

接下来是`configureImageLoader()` :

```
 // Configure Image loader
const configureImageLoader = () => {
    return {
        test: /\.(png|jpe?g|gif|svg|webp)$/i,
        use: [
            {
                loader: 'file-loader',
                options: {
                    name: 'img/[name].[hash].[ext]'
                }
            }
        ]
    };
}; 
```

值得注意的是，这只适用于包含在我们的 web pack 构建中的图像；许多其他图像将来自其他地方(CMS 系统、资产管理系统等)。).

为了让 web pack 知道一个图像，您将它导入到您的 JavaScript:

```
 import Icon from './icon.png'; 
```

查看 web pack 文档的[加载图像](http://webpack.js.org/guides/asset-management/#loading-images)部分，了解更多详细信息。

接下来是我们的`configurePostcssLoader()` :

```
 // Configure the Postcss loader
const configurePostcssLoader = () => {
    return {
        test: /\.(pcss|css)$/,
        use: [
            {
                loader: 'style-loader',
            },
            {
                loader: 'vue-style-loader',
            },
            {
                loader: 'css-loader',
                options: {
                    url: false,
                    importLoaders: 2,
                    sourceMap: true
                }
            },
            {
                loader: 'resolve-url-loader'
            },
            {
                loader: 'postcss-loader',
                options: {
                    sourceMap: true
                }
            }
        ]
    };
}; 
```

我们使用 [PostC SS](http://postcss.org/) 来处理我们所有的 C SS，包括[尾翼 CSS](http://tailwindcss.com/docs/what-is-tailwind/) 。我认为它是 CSS 的通天塔，因为它把各种高级的 CSS 功能都堆到了你的浏览器能理解的普通 CSS 中。

需要注意的是，对于 web pack 加载器，它们的处理顺序与列出的顺序相反:

*   **[postc ss-loader](http://github.com/postcss/postcss-loader)** —加载并处理 es 文件作为 postc ss
*   **[解析 url 加载器](http://www.npmjs.com/package/resolve-url-loader)** —将我们的 CSS 中的任何`url()`重写为相对的公共路径
*   **[css-loader](http://github.com/webpack-contrib/css-loader)** —解析我们所有的 CSS `@import`和`url()`
*   **[vue 风格加载器](http://github.com/vuejs/vue-style-loader)** —从`.vue`单个文件组件注入我们所有的 CSS
*   **[样式加载器](http://github.com/webpack-contrib/style-loader)** —将我们所有的 CSS 注入到文档内联的`<style></style>`标签中

记住，因为这是我们在本地开发中所做的，我们不需要做任何事情来将我们所有的 CSS 提取到一个最小化的文件中。相反，我们只是让`style-loader`在我们的文档中内联它。

`webpack-dev-server`将对我们的 CSS 使用热模块替换(HMR ),所以任何时候我们改变任何东西，它都会自动重建我们的 CSS 并重新注入它。这真是太神奇了。

我们通过包含它来告诉 web pack 我们的 CSS:

```
 import styles from '../css/app.pcss'; 
```

这将在 web pack 文档的[加载 CSS](http://webpack.js.org/guides/asset-management/#loading-css) 部分详细讨论。

我们从我们的`App.js`入口点开始做这件事；请将此视为 PostC SS 入口点。文件`@import`是我们的项目使用的所有 CSS 这将在后面详细介绍。

**MODULE。出口**

最后，`module.exports`使用 [web pack-merge](http://www.npmjs.com/package/webpack-merge) pack age 来合并`common.modernConfig`和我们的开发配置:

```
 // Development module exports
module.exports = merge(
    common.modernConfig,
    {
        output: {
            filename: path.join('./js', '[name].[hash].js'),
            publicPath: settings.devServerConfig.public() + '/',
        },
        mode: 'development',
        devtool: 'inline-source-map',
        devServer: configureDevServer(),
        module: {
            rules: [
                configurePostcssLoader(),
                configureImageLoader(),
            ],
        },
        plugins: [
            new webpack.HotModuleReplacementPlugin(),
            new DashboardPlugin(),
        ],
    }
); 
```

通过将`mode`设置为`'development'`，我们告诉 web pack 这是一个[开发版本](http://webpack.js.org/concepts/mode/)。

通过将 ting `devtool`设置为`'inline-source-map'`，我们要求我们的`.map`将 CSS/ JavaScript 的[内联到它们自己的](http://webpack.js.org/configuration/devtool/)文件中。这使得文件变得很大，但是对于调试来说很不方便。

网络包。HotModuleReplacementPlugin 支持 web pack 端的热模块替换(HMR)。

仪表板插件让我们感觉像一名宇航员，带着 fan cy web pack build HUD:

[![Webpack Dashboard Plugin](../Images/0cdba79a5a19e1ff3d2edd52efee7645.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xZNBKvsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x699_crop_center-center_100_line/webpack-dashboard-plugin.png)

我发现仪表板开发界面比默认的 web pack 进度滚动更有用。

如果您发现您需要调试 web pack 配置本身，您可以使用`yarn run debug`或`npm run debug`来运行本地开发构建，但是绕过`webpack-dashboard`。

就这样，我们的项目现在有了一个很好的开发架构；查看[热模块更换视频](http://www.youtube.com/watch?v=9-n2Ah0aowo)中的实例:

## 年 tat 和 webpack.prod.js

现在让我们来看看我们的`webpack.prod.js`配置文件，它包含了我们在项目工作时用于产品构建的所有设置。它与`webpack.common.js`中的设置合并，形成一个完整的网络包配置。

```
 // webpack.prod.js - production builds
const LEGACY_CONFIG = 'legacy';
const MODERN_CONFIG = 'modern';

// node modules
const git = require('git-rev-sync');
const glob = require('glob-all');
const merge = require('webpack-merge');
const moment = require('moment');
const path = require('path');
const webpack = require('webpack');

// webpack plugins
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const CreateSymlinkPlugin = require('create-symlink-webpack-plugin');
const CriticalCssPlugin = require('critical-css-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ImageminWebpWebpackPlugin = require('imagemin-webp-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const PurgecssPlugin = require('purgecss-webpack-plugin');
const SaveRemoteFilePlugin = require('save-remote-file-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');
const WebappWebpackPlugin = require('webapp-webpack-plugin');
const WhitelisterPlugin = require('purgecss-whitelister');
const WorkboxPlugin = require('workbox-webpack-plugin');

// config files
const common = require('./webpack.common.js');
const pkg = require('./package.json');
const settings = require('./webpack.settings.js'); 
```

在这个过程中，我们再次引入我们需要的节点包和我们使用的 web 包插件。然后，我们将我们的`webpack.settings.js`作为`settings`导入，这样我们就可以访问那里的设置，也可以将我们的`package.json`作为`pkg`导入，以访问那里的一些设置。

我们还导入我们的`webpack.common.js` com mon web pack 配置，我们将合并我们的开发设置。

**尾部取风器**

这个类是[尾风 CSS](http://tailwindcss.com/docs/what-is-tailwind/) 的自定义提取器，允许类名中有特殊字符。

```
 // Custom PurgeCSS extractor for Tailwind that allows special characters in
// class names.
//
// https://github.com/FullHuman/purgecss#extractor
class TailwindExtractor {
    static extract(content) {
        return content.match(/[A-Za-z0-9-_:\/]+/g) || [];
    }
} 
```

这是从顺风 C SS 文件的[清除未使用的 CSS 和采购](http://tailwindcss.com/docs/controlling-file-size/#removing-unused-css-with-purgecss)部分取得的。下面详细介绍了 extrac tor 如何与 PurgeC SS 配合使用，使您的 C SS 变得苗条整洁。

**配置功能**

下面是`configureBanner()`的样子:

```
 // Configure file banner
const configureBanner = () => {
    return {
        banner: [
            '/*!',
            ' * @project ' + settings.name,
            ' * @name ' + '[filebase]',
            ' * @author ' + pkg.author.name,
            ' * @build ' + moment().format('llll') + ' ET',
            ' * @release ' + git.long() + ' [' + git.branch() + ']',
            ' * @copyright Copyright (c) ' + moment().format('YYYY') + ' ' + settings.copyright,
            ' *',
            ' */',
            ''
        ].join('\n'),
        raw: true
    };
}; 
```

这个程序为我们构建的每个文件添加了一个带有项目名、文件名、作者和 git 信息的 ban ner。

接下来是`configureBundleAnalyzer()` :

```
 // Configure Bundle Analyzer
const configureBundleAnalyzer = (buildType) => {
    if (buildType === LEGACY_CONFIG) {
        return {
            analyzerMode: 'static',
            reportFilename: 'report-legacy.html',
        };
    }
    if (buildType === MODERN_CONFIG) {
        return {
            analyzerMode: 'static',
            reportFilename: 'report-modern.html',
        };
    }
}; 
```

这使用了 [Web pack 捆绑包和一个分析器](http://www.npmjs.com/package/webpack-bundle-analyzer)插件来为我们的现代和合法捆绑包生成一个报告，该报告产生一个自包含的交互式 HTML 页面，允许您探索 Web pack 生成的捆绑包中的确切内容。

[![Webpack Bundle Analyzer](../Images/53aacefbdad2eb2b64b8038a94e5f3f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OcsUcXER--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/webpack-bundle-analyzer.jpg)

我发现它非常有用，可以帮助我缩小尺寸，并确切地了解 web pack 是如何构建的，所以我将它作为我产品构建过程的一部分。

接下来是`configureCriticalCss()` :

```
 // Configure Critical CSS
const configureCriticalCss = () => {
    return (settings.criticalCssConfig.pages.map((row) => {
            const criticalSrc = settings.urls.critical + row.url;
            const criticalDest = settings.criticalCssConfig.base + row.template + settings.criticalCssConfig.suffix;
            let criticalWidth = settings.criticalCssConfig.criticalWidth;
            let criticalHeight = settings.criticalCssConfig.criticalHeight;
            // Handle Google AMP templates
            if (row.template.indexOf(settings.criticalCssConfig.ampPrefix) !== -1) {
                criticalWidth = settings.criticalCssConfig.ampCriticalWidth;
                criticalHeight = settings.criticalCssConfig.ampCriticalHeight;
            }
            console.log("source: " + criticalSrc + " dest: " + criticalDest);
            return new CriticalCssPlugin({
                base: './',
                src: criticalSrc,
                dest: criticalDest,
                extract: false,
                inline: false,
                minify: true,
                width: criticalWidth,
                height: criticalHeight,
            })
        })
    );
}; 
```

它使用中的[Crit i calC ss plug g，通过从我们的`webpack.settings.js`分块通过`settings.criticalCssConfig.pages`为我们的网站生成 Crit I calC SS。](http://www.npmjs.com/package/critical-css-webpack-plugin)

请注意，如果传入的页面名称中有任何 where，它会通过传入一个非常大的高度来为整个网页(不仅仅是折叠内容上方)生成 Crit i calC SS。

我不会在这里对我计算的暴击进行过多的描述；查看您网站上的[实施关键 CSS 文章](https://dev.to/gaijinity/implementing-critical-css-on-your-website-37d5-temp-slug-6012917)以了解更多关于关键的信息。

接下来是`configureCleanWebpack()` :

```
 // Configure Clean webpack
const configureCleanWebpack = () => {
    return {
        cleanOnceBeforeBuildPatterns: settings.paths.dist.clean,
        verbose: true,
        dry: false
    };
}; 
```

这只是使用了中的[干净的 Web pack Plu g 来从我们的`webpack.settings.js`中删除`settings.paths.dist.base`中的构建目录。](http://github.com/johnagan/clean-webpack-plugin)

接下来是`configureCompression()` :

```
 // Configure Compression webpack plugin
const configureCompression = () => {
    return {
        filename: '[path].gz[query]',
        test: /\.(js|css|html|svg)$/,
        threshold: 10240,
        minRatio: 0.8,
        deleteOriginalAssets: false,
        compressionOptions: {
            numiterations: 15,
            level: 9
        },
        algorithm(input, compressionOptions, callback) {
            return zopfli.gzip(input, compressionOptions, callback);
        }
    };
}; 
```

这使用中的[Com pression plug g 将我们的静态资源预压缩到`.gz`文件中，这样我们就可以](http://github.com/webpack-contrib/compression-webpack-plugin)[通过一个简单的](https://medium.com/@selvaganesh93/how-to-serve-webpack-gzipped-file-in-production-using-nginx-692eadbb9f1c) [web 服务器配置](http://theartofmachinery.com/2016/06/06/nginx_gzip_static.html)将它们预压缩到中。

接下来是`configureHtml()` :

```
 // Configure Html webpack
const configureHtml = () => {
    return {
        templateContent: '',
        filename: 'webapp.html',
        inject: false,
    };
}; 
```

它使用的 [Html Web pack Plu g 和](http://webpack.js.org/plugins/html-webpack-plugin/)的 [Webap p Web pack Plu g(见下文)来为我们的收藏夹生成 Html。注意，我们在`templateContent`中传递了一个 emp ty 字符串，因此输出只是来自 WebappWebpackPlugin 的原始输出。](http://github.com/brunocodutra/webapp-webpack-plugin)

接下来是`configureImageLoader()` :

```
 // Configure Image loader
const configureImageLoader = (buildType) => {
    if (buildType === LEGACY_CONFIG) {
        return {
            test: /\.(png|jpe?g|gif|svg|webp)$/i,
            use: [
                {
                    loader: 'file-loader',
                    options: {
                        name: 'img/[name].[hash].[ext]'
                    }
                }
            ]
        };
    }
    if (buildType === MODERN_CONFIG) {
        return {
            test: /\.(png|jpe?g|gif|svg|webp)$/i,
            use: [
                {
                    loader: 'file-loader',
                    options: {
                        name: 'img/[name].[hash].[ext]'
                    }
                },
                {
                    loader: 'img-loader',
                    options: {
                        plugins: [
                            require('imagemin-gifsicle')({
                                interlaced: true,
                            }),
                            require('imagemin-mozjpeg')({
                                progressive: true,
                                arithmetic: false,
                            }),
                            require('imagemin-optipng')({
                                optimizationLevel: 5,
                            }),
                            require('imagemin-svgo')({
                                plugins: [
                                    {convertPathData: false},
                                ]
                            }),
                        ]
                    }
                }
            ]
        };
    }
}; 
```

我们传入`buildType`,以便根据它是合法版本还是现代版本返回不同的结果。在这种情况下，我们通过 [img-loader](http://www.npmjs.com/package/img-loader) 为现代构建运行各种图像优化。

我们只为现代版本这样做，因为花时间优化现代和传统版本的映像是没有意义的(两者的映像是相同的)。

值得注意的是，这只适用于包含在我们的 web pack 构建中的图像；许多其他图像将来自其他地方(CMS 系统、资产管理系统等)。).

为了让 web pack 知道一个图像，您将它导入到您的 JavaScript:

```
 import Icon from './icon.png'; 
```

查看 web pack 文档的[加载图像](http://webpack.js.org/guides/asset-management/#loading-images)部分，了解更多详细信息。

接下来是我们的`configureOptimization()` :

```
 // Configure optimization
const configureOptimization = (buildType) => {
    if (buildType === LEGACY_CONFIG) {
        return {
            splitChunks: {
                cacheGroups: {
                    default: false,
                    common: false,
                    styles: {
                        name: settings.vars.cssName,
                        test: /\.(pcss|css|vue)$/,
                        chunks: 'all',
                        enforce: true
                    }
                }
            },
            minimizer: [
                new TerserPlugin(
                    configureTerser()
                ),
                new OptimizeCSSAssetsPlugin({
                    cssProcessorOptions: {
                        map: {
                            inline: false,
                            annotation: true,
                        },
                        safe: true,
                        discardComments: true
                    },
                })
            ]
        };
    }
    if (buildType === MODERN_CONFIG) {
        return {
            minimizer: [
                new TerserPlugin(
                    configureTerser()
                ),
            ]
        };
    }
}; 
```

这是我们配置 [web pack 生产优化](http://webpack.js.org/configuration/optimization/)的地方。仅对于合法构建(做两次没有意义)，我们使用 [MiniC 的 sEx tract Plu g in](http://github.com/webpack-contrib/mini-css-extract-plugin) 将项目范围内使用的所有 CSS 提取到一个文件中。如果您以前使用过 web pack，那么您可能以前使用过 Extract TextPlu g in 来完成这项工作；不会了。

然后，我们还使用中的[优化 SSAs 集合 sPlu g，通过移除重复规则来优化 CSS 结果，并通过`cssnano`来优化 CSS。](http://github.com/NMFR/optimize-css-assets-webpack-plugin)

最后，我们将 JavaScript 最小化器设置为中的[Terser plug；这是因为](http://github.com/webpack-contrib/terser-webpack-plugin) [Ugli fyJs Plu g in 不再支持](http://github.com/webpack-contrib/uglifyjs-webpack-plugin/releases/tag/v2.0.0)min mizing es 2015+JavaScript。因为我们是 ing mod ern ES2015+ bun dles 的一代，所以我们需要它。

接下来是`configurePostcssLoader()` :

```
 // Configure Postcss loader
const configurePostcssLoader = (buildType) => {
    if (buildType === LEGACY_CONFIG) {
        return {
            test: /\.(pcss|css)$/,
            use: [
                MiniCssExtractPlugin.loader,
                {
                    loader: 'css-loader',
                    options: {
                        importLoaders: 2,
                        sourceMap: true
                    }
                },
                {
                    loader: 'resolve-url-loader'
                },
                {
                    loader: 'postcss-loader',
                    options: {
                        sourceMap: true
                    }
                }
            ]
        };
    }
    // Don't generate CSS for the modern config in production
    if (buildType === MODERN_CONFIG) {
        return {
            test: /\.(pcss|css)$/,
            loader: 'ignore-loader'
        };
    }
}; 
```

这看起来非常类似于`configurePostcssLoader()`的版本，除了对于我们的最终加载器，我们使用[minicsextractplugin . loader](http://github.com/webpack-contrib/mini-css-extract-plugin)将我们所有的 CSS 提取到一个文件中。

我们这样做只是为了合法的构建，因为对每个构建都这样做是没有意义的(CSS 是一样的)。我们使用 [ignore-loader](http://www.npmjs.com/package/ignore-loader) 进行现代构建，因此加载器存在于我们的。css &。pcss 文件，但它什么也不做。

正如前面提到的李尔，我们使用 [PostC SS](http://postcss.org/) 来处理我们所有的 C SS，包括[尾翼 CSS](http://tailwindcss.com/docs/what-is-tailwind/) 。我认为它是 CSS 的通天塔，因为它把各种高级的 CSS 功能都堆到了你的浏览器能理解的普通 CSS 中。

同样，需要注意的是，对于 web pack 加载器，它们的处理顺序与列出的顺序相反:

*   **[postc ss-loader](http://github.com/postcss/postcss-loader)** —加载并处理 es 文件作为 postc ss
*   **[解析 url 加载器](http://www.npmjs.com/package/resolve-url-loader)** —将我们的 CSS 中的任何`url()`重写为相对的公共路径
*   **[css-loader](http://github.com/webpack-contrib/css-loader)** —解析我们所有的 CSS `@import`和`url()`
*   **[minicsextractplugin . loader](http://github.com/webpack-contrib/mini-css-extract-plugin)**—将我们所有的产品 CSS 提取到一个文件中

由于这是一个产品构建，我们取出所有使用`MiniCssExtractPlugin.loader`的 CSS，并保存到一个单独的`.css`文件中。CSS 也变得最小化，并为生产而优化。

我们通过包含它来告诉 web pack 我们的 CSS:

```
 import styles from '../css/app.pcss'; 
```

这将在 web pack 文档的[加载 CSS](http://webpack.js.org/guides/asset-management/#loading-css) 部分详细讨论。

我们从我们的`App.js`入口点开始做这件事；请将此视为 PostC SS 入口点。文件`@import`是我们的项目使用的所有 CSS 这将在后面详细介绍。

接下来是`configurePurgeCss()` :

```
 // Configure PurgeCSS
const configurePurgeCss = () => {
    let paths = [];
    // Configure whitelist paths
    for (const [key, value] of Object.entries(settings.purgeCssConfig.paths)) {
        paths.push(path.join(__dirname, value));
    }

    return {
        paths: glob.sync(paths),
        whitelist: WhitelisterPlugin(settings.purgeCssConfig.whitelist),
        whitelistPatterns: settings.purgeCssConfig.whitelistPatterns,
        extractors: [
            {
                extractor: TailwindExtractor,
                extensions: settings.purgeCssConfig.extensions
            }
        ]
    };
}; 
```

[Tail wind CSS](http://tailwindcss.com/docs/what-is-tailwind/) 是一个 fan tas tic util i ty-first CSS 框架，它允许快速 pro-to-typing，因为在本地开发中，您很少需要实际编写任何 CSS。相反，您只需使用提供的实用 CSS 类。

不利的一面是产生的 CSS 可能有点大。这就是不锈钢的用武之地。它将解析你所有的 HTML/ template/ Vue/ whatever 文件，并去掉任何未使用的 CSS。

储蓄可以是 dramatic；顺风 CSS 和 purge cs 是天造地设的一对。我们在与 Adam Wathan 的播客中深入讨论了这个问题。

它遍历`settings.purgeCssConfig.paths`中的所有路径组，寻找要保留的 CSS 规则；任何没有找到的 CSS 规则都将从我们的 CSS 构建结果中去除。

我们还使用中的[白名单工具，当我们知道我们不想删除某些 CSS 时，可以很容易地将整个文件或者甚至是文件加入白名单。匹配我们的`settings.purgeCssConfig.whitelist`的所有文件中的 CSS 规则都被列入白名单，并且永远不会从结果构建中删除。](http://www.npmjs.com/package/purgecss-whitelister)

接下来是`configureTerser()` :

```
 // Configure terser
const configureTerser = () => {
    return {
        cache: true,
        parallel: true,
        sourceMap: true
    };
}; 
```

这只是配置了一些由中的 [Terser Plu g 使用的设置，它们最小化了我们的合法和现代 JavaScript 代码。](http://github.com/webpack-contrib/terser-webpack-plugin)

接下来是`configureWebApp()` :

```
 // Configure Webapp webpack
const configureWebapp = () => {
    return {
        logo: settings.webappConfig.logo,
        prefix: settings.webappConfig.prefix,
        cache: false,
        inject: 'force',
        favicons: {
            appName: pkg.name,
            appDescription: pkg.description,
            developerName: pkg.author.name,
            developerURL: pkg.author.url,
            path: settings.paths.dist.base,
        }
    };
}; 
```

这使用了的 [Webap p Web pack Plu g 来生成我们网站上所有的收藏夹，以及我们的 webapp `manifest.json`和其他 PWA 细节。](http://www.npmjs.com/package/webapp-webpack-plugin)

它与 [Html Web pack Plu g in](http://webpack.js.org/plugins/html-webpack-plugin/) 一起工作，还输出一个`webapp.html`文件，该文件包含所有编辑收藏夹和关联编辑文件的链接，以便包含在我们的 Html 页面的`<head></head>`中。

接下来是`configureWorkbox()` :

```
 // Configure Workbox service worker
const configureWorkbox = () => {
    let config = settings.workboxConfig;

    return config;
}; 
```

我们使用谷歌的[工具箱 Web pack Plu g in](http://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin) 为我们的网站生成一个[服务程序](http://developers.google.com/web/fundamentals/primers/service-workers/)。解释什么是服务工作者超出了本文的范围，但是你可以看看[离线:服务工作者与 Jere my Kei th](http://devmode.fm/episodes/going-offline-service-workers-with-jeremy-keith) pod 作为入门。

配置都来自我们的`webpack.settings.js`中的`settings.workboxConfig`对象。除了预缓存我们的现代版本`manifest.json`中的所有资产，我们还包括一个`workbox-catch-handler.js`来配置它使用一个[回退响应捕获路由](http://developers.google.com/web/tools/workbox/guides/advanced-recipes#provide_a_fallback_response_to_a_route)。

```
 // fallback URLs
const FALLBACK_HTML_URL = '/offline.html';
const FALLBACK_IMAGE_URL = '/offline.svg';

// This "catch" handler is triggered when any of the other routes fail to
// generate a response.
// https://developers.google.com/web/tools/workbox/guides/advanced-recipes#provide_a_fallback_response_to_a_route
workbox.routing.setCatchHandler(({event, request, url}) => {
    // Use event, request, and url to figure out how to respond.
    // One approach would be to use request.destination, see
    // https://medium.com/dev-channel/service-worker-caching-strategies-based-on-request-types-57411dd7652c
    switch (request.destination) {
        case 'document':
            return caches.match(FALLBACK_HTML_URL);
            break;

        case 'image':
            return caches.match(FALLBACK_IMAGE_URL);
            break;

        default:
            // If we don't have a fallback, just return an error response.
            return Response.error();
    }
});

// Use a stale-while-revalidate strategy for all other requests.
workbox.routing.setDefaultHandler(
    workbox.strategies.staleWhileRevalidate()
); 
```

**模块。出口**

最后，`module.exports`使用 [web pack-merge](http://www.npmjs.com/package/webpack-merge) 合并来自`webpack.common.js`的`common.legacyConfig`和我们的产品合法配置，以及`common.modernConfig`和我们的产品现代配置:

```
 // Production module exports
module.exports = [
    merge(
        common.legacyConfig,
        {
            output: {
                filename: path.join('./js', '[name]-legacy.[chunkhash].js'),
            },
            mode: 'production',
            devtool: 'source-map',
            optimization: configureOptimization(LEGACY_CONFIG),
            module: {
                rules: [
                    configurePostcssLoader(LEGACY_CONFIG),
                    configureImageLoader(LEGACY_CONFIG),
                ],
            },
            plugins: [
                new MiniCssExtractPlugin({
                    path: path.resolve(__dirname, settings.paths.dist.base),
                    filename: path.join('./css', '[name].[chunkhash].css'),
                }),
                new PurgecssPlugin(
                    configurePurgeCss()
                ),
                new webpack.BannerPlugin(
                    configureBanner()
                ),
                new HtmlWebpackPlugin(
                    configureHtml()
                ),
                new WebappWebpackPlugin(
                    configureWebapp()
                ),
                new CreateSymlinkPlugin(
                    settings.createSymlinkConfig,
                    true
                ),
                new SaveRemoteFilePlugin(
                    settings.saveRemoteFileConfig
                ),
                new BundleAnalyzerPlugin(
                    configureBundleAnalyzer(LEGACY_CONFIG),
                ),
            ].concat(
                configureCriticalCss()
            )
        }
    ),
    merge(
        common.modernConfig,
        {
            output: {
                filename: path.join('./js', '[name].[chunkhash].js'),
            },
            mode: 'production',
            devtool: 'source-map',
            optimization: configureOptimization(MODERN_CONFIG),
            module: {
                rules: [
                    configurePostcssLoader(MODERN_CONFIG),
                    configureImageLoader(MODERN_CONFIG),
                ],
            },
            plugins: [
                new CleanWebpackPlugin(
                    configureCleanWebpack()
                ),
                new webpack.BannerPlugin(
                    configureBanner()
                ),
                new ImageminWebpWebpackPlugin(),
                new WorkboxPlugin.GenerateSW(
                    configureWorkbox()
                ),
                new BundleAnalyzerPlugin(
                    configureBundleAnalyzer(MODERN_CONFIG),
                ),
            ]
        }
    ),
]; 
```

通过在我们的`module.exports`中返回一个数组，我们告诉 web pack 我们有不止一个 com 堆需要完成:一个用于我们的合法构建，另一个用于我们的现代构建。

注意，对于合法版本，我们将处理过的 JavaScript 作为`[name]-legacy.[hash].js`输出，而现代版本将其作为`[name].[hash].js`输出。

通过将`mode`设置为`'production'`，我们告诉 web pack 这是一个[产品版本](http://webpack.js.org/concepts/mode/)。这使得大量的设置适用于生产制造。

通过将 ting `devtool`设置为`'source-map'`,我们要求我们的 CSS/ JavaScript 的`.map`以九月的速率生成。地图文件。这使得我们可以更容易地调试现场制作网站，而无需增加我们资产的文件大小。

这里使用了一些我们还没有涉及到的 web pack 插件:

*   创建符号链接——这是一个在构建过程中允许符号链接创建的插件。我用它来表示从第`favicon.ico`页到第`/favicon.ico`页的链接，因为许多网络浏览器在网络根目录中寻找。
*   保存远程文件插件——这是一个插件，用于下载远程文件，并在 web pack 构建过程中发送它们。我用这个在本地下载和服务谷歌的`analytics.js`。
*   **[Imagem in webp Web pack Plu g in](http://www.npmjs.com/package/imagemin-webp-webpack-plugin)**—这个 plug in 为您的项目导入的所有 JPEG 和 PNG 文件创建`.webp`变量

就这样，我们现在有了一个很好的产品构建，为我们的项目提供了所有的附加功能。

## 顺风 CSS &后置 SS 配置

为了让 web pack 构建 Tail wind CSS 和我们 CSS 的其他部分，我们需要做一些设置。这要归功于我的搭档乔纳森·梅尔维尔在这方面的工作。首先我们需要一个`postcss.config.js`文件:

```
 module.exports = {
    plugins: [
        require('postcss-import')({
            plugins: [
                require('stylelint')
            ]
        }),
        require('tailwindcss')('./tailwind.config.js'),
        require('postcss-preset-env')({
            autoprefixer: { grid: true },
            features: {
                'nesting-rules': true
            }
        })
    ]
}; 
```

这可以存储在项目根目录中；作为构建过程的一部分，PostC SS 将自动寻找它，并应用我们指定的 PostC SS 插件。注意这里我们包含了`tailwind.config.js`文件，使其成为构建过程的一部分。

最后，我们的 CSS 入口点`app.pcss`看起来像这样:

```
 /**
 * app.css
 *
 * The entry point for the css.
 *
 */

/**
 * This injects Tailwind's base styles, which is a combination of
 * Normalize.css and some additional base styles.
 *
 * You can see the styles here:
 * https://github.com/tailwindcss/tailwindcss/blob/master/css/preflight.css
 */
 @import "tailwindcss/preflight";

/**
 * This injects any component classes registered by plugins.
 *
 */
@import 'tailwindcss/components';

/**
 * Here we add custom component classes; stuff we want loaded
 * *before* the utilities so that the utilities can still
 * override them.
 *
 */
@import './components/global.pcss';
@import './components/typography.pcss';
@import './components/webfonts.pcss';

/**
 * This injects all of Tailwind's utility classes, generated based on your
 * config file.
 *
 */
@import 'tailwindcss/utilities';

/**
 * Include styles for individual pages
 *
 */
@import './pages/homepage.pcss';

/**
 * Include vendor css.
 *
 */
 @import 'vendor.pcss'; 
```

很明显，它包括了你在定制 CSS 中使用的组件/页面。

## 后期构建项目树

下面是我们的项目树构建后的样子:

```
 ├── example.env
├── package.json
├── postcss.config.js
├── src
│   ├── css
│   │   ├── app.pcss
│   │   ├── components
│   │   │   ├── global.pcss
│   │   │   ├── typography.pcss
│   │   │   └── webfonts.pcss
│   │   ├── pages
│   │   │   └── homepage.pcss
│   │   └── vendor.pcss
│   ├── fonts
│   ├── img
│   │   └── favicon-src.png
│   ├── js
│   │   ├── app.js
│   │   └── workbox-catch-handler.js
│   └── vue
│   └── Confetti.vue
├── tailwind.config.js
├── templates
├── web
│   ├── dist
│   │   ├── criticalcss
│   │   │   └── index_critical.min.css
│   │   ├── css
│   │   │   ├── styles.d833997e3e3f91af64e7.css
│   │   │   └── styles.d833997e3e3f91af64e7.css.map
│   │   ├── img
│   │   │   └── favicons
│   │   │   ├── android-chrome-144x144.png
│   │   │   ├── android-chrome-192x192.png
│   │   │   ├── android-chrome-256x256.png
│   │   │   ├── android-chrome-36x36.png
│   │   │   ├── android-chrome-384x384.png
│   │   │   ├── android-chrome-48x48.png
│   │   │   ├── android-chrome-512x512.png
│   │   │   ├── android-chrome-72x72.png
│   │   │   ├── android-chrome-96x96.png
│   │   │   ├── apple-touch-icon-114x114.png
│   │   │   ├── apple-touch-icon-120x120.png
│   │   │   ├── apple-touch-icon-144x144.png
│   │   │   ├── apple-touch-icon-152x152.png
│   │   │   ├── apple-touch-icon-167x167.png
│   │   │   ├── apple-touch-icon-180x180.png
│   │   │   ├── apple-touch-icon-57x57.png
│   │   │   ├── apple-touch-icon-60x60.png
│   │   │   ├── apple-touch-icon-72x72.png
│   │   │   ├── apple-touch-icon-76x76.png
│   │   │   ├── apple-touch-icon.png
│   │   │   ├── apple-touch-icon-precomposed.png
│   │   │   ├── apple-touch-startup-image-1182x2208.png
│   │   │   ├── apple-touch-startup-image-1242x2148.png
│   │   │   ├── apple-touch-startup-image-1496x2048.png
│   │   │   ├── apple-touch-startup-image-1536x2008.png
│   │   │   ├── apple-touch-startup-image-320x460.png
│   │   │   ├── apple-touch-startup-image-640x1096.png
│   │   │   ├── apple-touch-startup-image-640x920.png
│   │   │   ├── apple-touch-startup-image-748x1024.png
│   │   │   ├── apple-touch-startup-image-750x1294.png
│   │   │   ├── apple-touch-startup-image-768x1004.png
│   │   │   ├── browserconfig.xml
│   │   │   ├── coast-228x228.png
│   │   │   ├── favicon-16x16.png
│   │   │   ├── favicon-32x32.png
│   │   │   ├── favicon.ico
│   │   │   ├── firefox_app_128x128.png
│   │   │   ├── firefox_app_512x512.png
│   │   │   ├── firefox_app_60x60.png
│   │   │   ├── manifest.json
│   │   │   ├── manifest.webapp
│   │   │   ├── mstile-144x144.png
│   │   │   ├── mstile-150x150.png
│   │   │   ├── mstile-310x150.png
│   │   │   ├── mstile-310x310.png
│   │   │   ├── mstile-70x70.png
│   │   │   ├── yandex-browser-50x50.png
│   │   │   └── yandex-browser-manifest.json
│   │   ├── js
│   │   │   ├── analytics.45eff9ff7d6c7c1e3c3d4184fdbbed90.js
│   │   │   ├── app.30334b5124fa6e221464.js
│   │   │   ├── app.30334b5124fa6e221464.js.map
│   │   │   ├── app-legacy.560ef247e6649c0c24d0.js
│   │   │   ├── app-legacy.560ef247e6649c0c24d0.js.map
│   │   │   ├── confetti.1152197f8c58a1b40b34.js
│   │   │   ├── confetti.1152197f8c58a1b40b34.js.map
│   │   │   ├── confetti-legacy.8e9093b414ea8aed46e5.js
│   │   │   ├── confetti-legacy.8e9093b414ea8aed46e5.js.map
│   │   │   ├── precache-manifest.f774c437974257fc8026ca1bc693655c.js
│   │   │   ├── styles-legacy.d833997e3e3f91af64e7.js
│   │   │   ├── styles-legacy.d833997e3e3f91af64e7.js.map
│   │   │   ├── vendors~confetti~vue.03b9213ce186db5518ea.js
│   │   │   ├── vendors~confetti~vue.03b9213ce186db5518ea.js.map
│   │   │   ├── vendors~confetti~vue-legacy.e31223849ab7fea17bb8.js
│   │   │   ├── vendors~confetti~vue-legacy.e31223849ab7fea17bb8.js.map
│   │   │   └── workbox-catch-handler.js
│   │   ├── manifest.json
│   │   ├── manifest-legacy.json
│   │   ├── report-legacy.html
│   │   ├── report-modern.html
│   │   ├── webapp.html
│   │   └── workbox-catch-handler.js
│   ├── favicon.ico -> dist/img/favicons/favicon.ico
│   ├── index.php
│   ├── offline.html
│   ├── offline.svg
│   └── sw.js
├── webpack.common.js
├── webpack.dev.js
├── webpack.prod.js
├── webpack.settings.js
└── yarn.lock 
```

## 在你的 HTML 中注入 ing 脚本& CSS 标签

使用这里显示的 web pack 配置，`<script>`和`<style>`标签不会作为产品构建的一部分注入到您的 HTML 中。设置使用 [Craft CMS](http://craftcms.com/) ，它有一个模板系统，我们在中使用[树枝包插入标签。](http://github.com/nystudio107/craft-twigpack)

如果你没有使用 Craft CMS 或者一个有模板引擎的系统，并且想把这些标签注入到你的 HTML 中，你可以使用中的 [Html Web pack Plu g 来帮你做这件事。这个插件已经包含在内了，您只需要添加一个小配置，告诉它将标签注入到您的 HTML 中。](http://github.com/jantimon/html-webpack-plugin)

## 用 Twig pack 插件制作 CMS 3 集成

如果你没有使用 [Craft CMS 3，](http://craftcms.com/)你可以安全地跳过这一节。它只是提供了一些有用的集成信息。

[![Twigpack Plugin Logo Lg](../Images/66e206d265200904fa45f015bead09c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---NwxxMZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/twigpack-plugin-logo-lg.jpg)

我写了一个名为 [Twig pack](http://github.com/nystudio107/craft-twigpack) 的免费插件，可以很容易地将我们的 fan cy web pack 构建与 Craft CMS 3 集成。

它处理对`manifest.json`文件的访问，将入口点注入到你的分支模板中，它甚至处理遗留/现代模块注入的模式，异步 CSS 加载，等等。

这将使这里介绍的 web pack 4 的工作变得非常简单。

为了包含 CSS，我做了:

```
 <!--# if expr="$HTTP_COOKIE=/critical\-css\=1/" -->
    {{ craft.twigpack.includeCssModule("styles.css", false) }}
<!--# else -->
    <script>
        Cookie.set("critical-css", '1', { expires: "7D", secure: true });
    </script>
    {{ craft.twigpack.includeCriticalCssTags() }}

    {{ craft.twigpack.includeCssModule("styles.css", true) }}
    {{ craft.twigpack.includeCssRelPreloadPolyfill() }}
<!--# endif --> 
```

`<!--# -->` HTML 注释是 [Nginx 服务器端包含](http://nginx.org/en/docs/http/ngx_http_ssi_module.html)目录。模式是，如果设置了`critical-css` cook ie，用户已经在过去 7 天访问过我们的网站，所以他们的浏览器应该缓存站点 CSS，我们只是正常地提供站点 CSS。

如果没有设置`critical-css` cook ie，我们通过 [Tiny cook ie](http://www.npmjs.com/package/tiny-cookie) 设置 Cook ie，包含我们的 Crit i cal CSS，并异步加载站点 CSS。请参见[在您的网站上实现关键调用 CSS](https://dev.to/gaijinity/implementing-critical-css-on-your-website-37d5-temp-slug-6012917)文章，了解关键调用 CSS 的详细信息。

为了提供我们的 JavaScript，我们只需要:

```
 {{ craft.twigpack.includeSafariNomoduleFix() }}
{{ craft.twigpack.includeJsModule("app.js", true) }} 
```

第二个真参数告诉它将 JavaScript async 作为一个模块加载，所以 HTML 的结果如下:

```
 <script>
!function(){var e=document,t=e.createElement("script");if(!("noModule"in t)&&"onbeforeload"in t){var n=!1;e.addEventListener("beforeload",function(e){if(e.target===t)n=!0;else if(!e.target.hasAttribute("nomodule")||!n)return;e.preventDefault()},!0),t.type="module",t.src=".",e.head.appendChild(t),t.remove()}}();
</script>
<script type="module" src="http://example.test/dist/js/app.273e88e73566fecf20de.js"></script>
<script nomodule src="http://example.test/dist/js/app-legacy.95d36ead9190c0571578.js"></script> 
```

详见[小树枝包装文档](http://github.com/nystudio107/craft-twigpack)

这是我使用的全部`config/twigpack.php file`;请注意，它在我的 Home stead VM 中有本地运行设置。您的设置可能有所不同:

```
 return [
    // Global settings
    '*' => [
        // If `devMode` is on, use webpack-dev-server to all for HMR (hot module reloading)
        'useDevServer' => false,
        // The JavaScript entry from the manifest.json to inject on Twig error pages
        'errorEntry' => '',
        // Manifest file names
        'manifest' => [
            'legacy' => 'manifest-legacy.json',
            'modern' => 'manifest.json',
        ],
        // Public server config
        'server' => [
            'manifestPath' => '/dist/',
            'publicPath' => '/',
        ],
        // webpack-dev-server config
        'devServer' => [
            'manifestPath' => 'http://localhost:8080/',
            'publicPath' => 'http://localhost:8080/',
        ],
        // Local files config
        'localFiles' => [
            'basePath' => '@webroot/',
            'criticalPrefix' => 'dist/criticalcss/',
            'criticalSuffix' => '_critical.min.css',
        ],
    ],
    // Live (production) environment
    'live' => [
    ],
    // Staging (pre-production) environment
    'staging' => [
    ],
    // Local (development) environment
    'local' => [
        // If `devMode` is on, use webpack-dev-server to all for HMR (hot module reloading)
        'useDevServer' => true,
        // The JavaScript entry from the manifest.json to inject on Twig error pages
        'errorEntry' => 'app.js',
        // webpack-dev-server config
        'devServer' => [
            'manifestPath' => 'http://localhost:8080/',
            'publicPath' => 'http://192.168.10.10:8080/',
        ],
    ],
]; 
```

## 包 ping 起来！

啊，那是相当深的潜水！当我第一次在 web pack 中使用吕德时，我很快意识到它是一个非常强大的工具，具有非常强大的功能。你潜多深取决于你想潜多远。

[![Webpack Deep Dive](../Images/0ad175780e21dc8dc4daf023f49fe4e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tgxx2wEj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/webpack-deep-dive.jpg)

对于这里提交的所有内容的完整源代码，请查看 **[公告-web pack-4-con 图](http://github.com/nystudio107/annotated-webpack-4-config)** github 报告。

希望这能对你有所帮助，享受你的旅程，去建造一些令人敬畏的东西吧！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计