# 升级到 webpack 4

> 原文：<https://dev.to/teej/upgrading-to-webpack-4-4f>

我喜欢 Laravel Mix 并不是什么大秘密。它足够方便地投入到大多数项目中，我已经在 WordPress 网站上使用它很长时间了，因为它使新开发人员的加入变得更加容易。巴别塔和萨斯？完成了。

```
mix.js('src/app.js', 'dist/').sass('src/app.scss', 'dist/'); 
```

Enter fullscreen mode Exit fullscreen mode

它抽象出所有的 webpack 魔法，这样你可以花更少的时间来设置。这是一个神奇的工具，我毫不犹豫地向人们推荐它。如果需要扩展的话，您可以注入自己的配置，这样您就不会被任何东西锁定。

另一方面，我是一个修补程序的爱好者，所以一行程序不利于我的恶作剧。在看到 webpack 团队在版本 4 上取得的胜利后，我渴望探索它，再加上 Laravel Mix 在 webpack 3 上([看起来很快就是版本 4](https://github.com/JeffreyWay/laravel-mix/pull/1495))。

以下是我需要做的事情清单:

*   传输我的 JS
*   处理用较少字体书写的样式
*   对顺风使用 PostCSS
*   将样式输出到单独的文件
*   生成服务人员
*   缩小生产资产

## 设置

我的配置开始加载我需要的包，我捕获我们所处的模式(开发或生产)。稍后我将使用这种模式来用任何特定于生产的操作更新配置。对于上下文来说，`webpack.config.js`位于我的项目的根，源文件和最终资产位于`web`文件夹中。

```
const path = require('path')
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const CleanWebpackPlugin = require('clean-webpack-plugin')
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
const workboxPlugin = require('workbox-webpack-plugin')

let env = process.env.NODE_ENV
let isDev = env === 'development' 
```

Enter fullscreen mode Exit fullscreen mode

## 一般配置

### JavaScript

这部分花了一点时间来调整我的路径，以正确地进行代码分割，并正确地从正确的 url 加载块，但最终我决定:

```
const WEBPACK_CONFIG = {
  mode: env, // development or production
  entry: {
    main: './web/src/js/main.js'
  },
  output: {
    publicPath: '/',
    path: path.resolve(__dirname, 'web'),
    filename: 'assets/js/[name].js',
    chunkFilename: 'assets/js/chunks/[name].js'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要将`publicPath`设置为`/`,这样数据块就可以正确加载，但是除此之外，还有足够的数据来处理其他的事情。

### 样式

Styles 花了一点时间，结果发现我是个傻瓜，没有阅读关于在哪里放置`less-loader`插件的说明。不过我最终还是到达了那里，所以更新后的配置如下所示:

```
const WEBPACK_CONFIG = {
  mode: env,
  entry: {
    main: './web/src/js/main.js',
    styles: './web/src/less/app.less'
  },
  output: {
    publicPath: '/',
    path: path.resolve(__dirname, 'web'),
    filename: 'assets/js/[name].js',
    chunkFilename: 'assets/js/chunks/[name].js'
  },
  module: {
    rules: [
      {
        test: /\.less$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
          'less-loader'
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'assets/css/app.css'
    })
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我为我的样式更新了`entry`对象，并添加了处理`less`文件的规则。最后，我添加了`MiniCssExtractPlugin`来将输出指向我的资产文件夹。

#### 顺风

为了让 Tailwind 工作，我在我的项目中添加了一个`postcss.config.js`文件，包含:

```
module.exports = {
  plugins: [require('tailwindcss')('./tailwind.js')]
} 
```

Enter fullscreen mode Exit fullscreen mode

`tailwind.js`引用是我的配置文件。

### 杂项

我想做的另一件事是在每次运行时清空 assets 文件夹，以防我添加一些额外的文件，比如未命名的块，这样我就不会有一个装满`1..n.js`文件的文件夹。

为此，我在插件数组中添加了以下内容:

```
new CleanWebpackPlugin(['web/assets']) 
```

Enter fullscreen mode Exit fullscreen mode

## 仅限生产

### 缩小

我只想在生产中缩小，因此，我添加了一个条件，如果它不在开发模式中，就追加到 webpack 中:

```
// `isDev` is set up earlier to check if process.env.NODE_ENV === 'development'
if (!isDev) {
  WEBPACK_CONFIG.optimization = {
    minimizer: [
      new UglifyJsPlugin({
        cache: true,
        parallel: true,
        sourceMap: true // set to true if you want JS source maps
      }),
      new OptimizeCSSAssetsPlugin({})
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 服务人员

老实说，如果我想在 PWA 上全力以赴，这是我仍然需要努力的事情。我正在使用[工具箱](https://github.com/GoogleChrome/workbox)来帮助解决这个问题。

所以还是在`if (!isDev)`块里面我加了:

```
WEBPACK_CONFIG.plugins.push(
  new workboxPlugin.InjectManifest({
    swSrc: './web/src/js/sw.js',
    swDest: 'sw.js'
  })
) 
```

Enter fullscreen mode Exit fullscreen mode

这将从文件:
中编译服务工作者

```
workbox.skipWaiting()
workbox.clientsClaim()

workbox.routing.registerRoute(
  /\.(?:png|gif|jpg|jpeg|svg)$/,
  workbox.strategies.cacheFirst({
    cacheName: 'images',
    plugins: [
      new workbox.expiration.Plugin({
        maxEntries: 60,
        maxAgeSeconds: 30 * 24 * 60 * 60 // 30 Days
      })
    ]
  })
)

workbox.routing.registerRoute(
  /\.(?:js|css)$/,
  workbox.strategies.staleWhileRevalidate({
    cacheName: 'static-resources'
  })
)

workbox.precaching.precacheAndRoute(self.__precacheManifest) 
```

Enter fullscreen mode Exit fullscreen mode

这将缓存图像、JavaScript 和 CSS。我真的需要多读点这方面的书。

## 发生了什么事？

最终，跑步`npm run dev`从耗时 6 秒变成了 2.5 秒，`npm run production`从 14 秒降到了 4 秒。这绝对是惊人的收获，证明了那些聪明人在 webpack 上所做的出色工作。这更像是一次学习的经历，让我更接近金属，我会继续调整和打破一些东西，因为这是我的网站。我在这里做比在工作时做好。我只对自己负责，我是我自己最严厉的批评家。

你可以在这个网站的[我的回购中找到最终文件。](https://github.com/tjFogarty/personal-site/blob/d78ab16e826a36dcd8fee28bf81f33acc417e529/webpack.config.js)

这最初是[发布在我的博客](https://tj.ie/upgrading-to-webpack-4)上。