# 如何为开发和生产设置不同的 Webpack 配置

> 原文：<https://dev.to/wiaio/how-to-set-up-different-webpack-configurations-for-development-and-production-2bk9>

web 应用程序的一个常见需求是开发和生产的不同构建过程。使用任务运行程序，如 Gulp 或 Grunt，这很容易实现，但是使用 Webpack，你需要采取不同的方法。在这里，我将向你展示我发现的最直接的方法。

首先，创建 3 个文件:
`webpack.config.dev.js`:这将是您用于开发的 Webpack 配置
`webpack.config.prod.js`:这将是您用于生产的 Webpack 配置
`webpack.config.js`:这将是您的主 Webpack 配置

`webpack.config.js`的内容应该就是这个:

```
module.exports = (env) => {
  return require(`./webpack.config.${env}.js`)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，`webpack`命令将接受一个参数`env`，该参数告诉它使用哪个配置文件。

接下来，像平常填充`webpack.config.js`一样填充`dev`和`prod`配置文件。在`webpack.config.dev.js`中，你可能想要运行一个开发服务器并内联你所有的样式，而在`webpack.config.prod.js`中，你可能想要将你的样式提取到一个 CSS 文件中，修改你的 JS，然后[将你的构建目录部署到一个服务器](https://community.wia.io/d/9-how-to-gzip-and-deploy-your-front-end-assets-to-amazon-s3-with-webpack)。

现在您可以运行`webpack --env=dev`或`webpack --env=prod`来运行各自的 webpack 配置。

如果您发现您的许多配置在这两个文件之间是重复的，您可以创建第四个名为`webpack.config.base.js`或类似的文件。在这个文件中，用适用于开发和生产的所有规则来设置您的 webpack 配置。在您的`dev`和`prod`配置文件中，只包含适用于该环境的规则。
为了将`base`配置合并到其他配置中，你需要 [webpack-merge](https://github.com/survivejs/webpack-merge) 。

`npm install webpack-merge`

在`webpack.config.dev.js`和`webpack.config.prod.js`中，在文件顶部包含以下内容:

```
const merge = require('webpack-merge');
const baseConfig = require('./webpack.config.base.js'); 
```

Enter fullscreen mode Exit fullscreen mode

...并改变

```
module.exports = {
  ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

至

```
module.exports = merge(baseConfig, {
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以运行与上面相同的命令，`base`配置将被合并到其他配置中。