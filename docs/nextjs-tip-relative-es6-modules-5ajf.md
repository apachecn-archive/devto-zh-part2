# NextJS 提示:相对 ES6 模块

> 原文：<https://dev.to/whoisryosuke/nextjs-tip-relative-es6-modules-5ajf>

你的组件在 NextJS 里是这样的吗？

```
import ShopProfile from '../../layouts/ShopProfile/ShopProfile'
import KushyApi from '../../utils/KushyApi'
import Menu from '../../components/Menu/Menu' 
```

Enter fullscreen mode Exit fullscreen mode

这种设置对于较小的项目来说是可以的，但是当你需要在文件夹间移动组件时，很容易迷失在相对目录的海洋中(是两组还是三组`.../`🤔).

## 插件方式

名为 root-import 的 babel 插件可以为我们处理这个问题，并且它非常容易设置:

`npm install babel-plugin-root-import --save-dev`

将一个`.babelrc`添加到您的项目根:

```
{
    "presets": [
        "next/babel"
    ],
    "plugins": [
        [
            "babel-plugin-root-import"
        ]
    ],
    "env": { // For React Native
        "production": {
            "plugins": [
                "babel-plugin-root-import"
            ]
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。您可以通过使用`~`符号作为根路径来包含组件:

```
import ShopProfile from '~/layouts/ShopProfile/ShopProfile'
import KushyApi from '~/utils/KushyApi'
import Menu from '~/components/Menu/Menu' 
```

Enter fullscreen mode Exit fullscreen mode

> 因为 NextJS 使用定制的 babel 设置，所以当我们提供自己的设置时，它会覆盖默认配置。这意味着我们失去了各种伟大的功能，如[对象传播](https://babeljs.io/docs/en/babel-plugin-transform-object-rest-spread/)。这就是为什么我们在配置的开始使用 Next 的巴别塔预置(`"presets": [ "next/babel" ],`)，并在下面放下我们的插件。现在你不应该在编译过程中得到任何错误。

## 更好的方法

我查找了如何在总体上做到这一点，而不是专门针对 NexJS，我发现了一个网站，上面有一个非常简明的指南。它指出，对于 Webpack 2，您将把它添加到您的`webpack.config.js` :

```
resolve: {
  modules: [
    path.resolve('./')
  ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

所以在 NextJS 中，我尝试这样做:

```
const withLess = require('@zeit/next-less')
const withCSS = require('@zeit/next-css')
const path = require('path')

// To add new modules, nest the function (like a HOC in React)
module.exports = withCSS(withLess({
    webpack(config, options) {
        config.module.rules.push({
            test: /\.(png|jpg|gif|svg|eot|ttf|woff|woff2)$/,
            use: {
                loader: 'url-loader',
                options: {
                    limit: 100000
                }
            }
        })

        // Here is the magic
        // We push our config into the resolve.modules array
        config.resolve.modules.push(path.resolve('./'))

        return config
    }
})) 
```

Enter fullscreen mode Exit fullscreen mode

这允许你像这样使用你的组件和模块:

```
import ShopProfile from 'layouts/ShopProfile/ShopProfile'
import KushyApi from 'utils/KushyApi'
import Menu from 'components/Menu/Menu' 
```

Enter fullscreen mode Exit fullscreen mode

比使用`~`符号更简洁，比安装另一个依赖项更容易👍

希望有帮助🍻
良

* * *

**参考文献**:

*   [巴别塔插件根导入](https://github.com/entwicklerstube/babel-plugin-root-import#readme)
*   [ES6 使用 Webpack 导入没有相对路径的语句](https://moduscreate.com/blog/es6-es2015-import-no-relative-path-webpack/)
*   [NextJS 问题-绝对相关模块](https://github.com/zeit/next.js/issues/342)
*   [如何使用。babelrc 结合 next.config.js？](https://github.com/zeit/next.js/issues/4010)