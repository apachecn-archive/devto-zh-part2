# NextJS 提示——在 CSS 中使用媒体(图像和字体)

> 原文：<https://dev.to/whoisryosuke/nextjs-tip---using-media-images--fonts-in-css-1ip5>

最近我用 [LESS](http://lesscss.org) 和 CSS 建立了一个 [NextJS](http://nextjs.org) 项目，我遇到了一个问题，我的相关媒体文件无法加载。我从 webpack 得到这个奇怪的错误，说有一个意外的字符？：

浏览服务器控制台日志中的第一段错误代码，显示我的 PNG 和 SVG 文件无法识别:

```
These relative modules were not found:

* ../../asseimg/Brand/kushy-logo-short-white.svg in ./components/Header/Header.less
* ../../asseimg/Brand/Kushy API Logo.png in ./components/Header/Header.less 
```

Enter fullscreen mode Exit fullscreen mode

## 第一个方案

我在[next js Github issues](https://github.com/zeit/next.js/issues/3852)上发现了这个解决方案，其中有人添加了一个定制的 Webpack 配置来使用`url-loader`解析媒体文件:

next.config.js:

```
module.exports = withCSS(withSass({
  webpack (config, options) {
    config.module.rules.push({
      test: /\.(png|jpg|gif|svg|eot|ttf|woff|woff2)$/,
      use: {
        loader: 'url-loader',
        options: {
          limit: 100000
        }
      }
    })

    return config
  }
})) 
```

Enter fullscreen mode Exit fullscreen mode

这对我不起作用，我开始得到一个关于`url-loader`没有被加载的错误(讽刺的是)。我安装了，得到的错误是`file-loader`没有安装？我安装了那个....成功了！

`npm install url-loader file-loader --save-dev`

## 别解

将您的图像复制到应用程序根目录下的`static`文件夹中。这允许您在 CSS ( `background:url('/static/image.png')`)中访问它们。这么简单👌

你不能用 NextJS 这样做的原因是他们的 webpack 设置。[他们不在服务器级别运行 webpack](https://github.com/zeit/next.js/issues/1935)，这不允许像其他 web pack 设置那样同步文件。您必须用自己的配置选项扩展他们的 Webpack 配置(如上)，或者使用他们设置的简单系统(从单个文件夹提供静态文件)。

## 不是有插件吗？

有一个名为 [next-images](https://github.com/arefaslani/next-images) 的库，它试图通过在任何你想要动态导入的媒体上调用`require()`函数来解决这个问题。这样做的问题是将固执己见的 API 代码添加到 CSS 中。它*需要*(看到我在那里做了什么吗？)在每个相对图像 URL 上添加`require()`函数。如果我要从 NPM 导入我的 CSS 以保持它与我的团队一致，我会遇到用必要的导入函数覆盖任何图像定义的问题。

## 真实世界的例子

我在使用语义 UI 设计系统时遇到了一个问题，它对图标使用了相对字体文件，我被迫要么设置一个自定义的 webpack 配置，要么手动将它们复制到我的静态文件夹中并覆盖 CSS 定义(`@fontPath : '/static/assets/fonts';`)。两者都运行良好，我坚持使用定制的 webpack 设置。

> 我在导入文件时确实遇到了问题，因为 LESS 变量中有错误的 URL(它是从`../../themes/themes/`导入的，而不仅仅是从`../../themes/`)。这个问题通过一个快速变量覆盖解决了:`@fontPath : '../../default/assets/fonts';`。不是 NextJS 的问题，但我想我应该记录下来。

希望对你有所帮助

* * *

**参考文献:**

*   [nexts](https://github.com/zeit/next.js/)的缩写形式
*   [下一张图片](https://github.com/arefaslani/next-images)
*   [NextJS 问题#1935 -在 CSS 中导入图像](https://github.com/zeit/next.js/issues/1935)
*   [NextJS 问题#3852 -在 CSS 中导入图像](https://github.com/zeit/next.js/issues/3852)
*   [文件加载器](https://github.com/webpack-contrib/file-loader)
*   [url 加载器](https://github.com/webpack-contrib/url-loader)