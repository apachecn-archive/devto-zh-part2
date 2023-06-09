# Vue CLI 3、TailwindCSS 和 PurgeCSS

> 原文：<https://dev.to/_mikhailbot/vue-cli-3-tailwindcss-and-purgecss-1d1k>

新的[Vue CLI](https://cli.vuejs.org/)刚刚出来，我很想试一试。我们的婚礼网站需要一层新的油漆，所以决定将其从 Nuxt 转移到一个普通的 Vue 应用程序。这是两页纸，所以真的没有必要把 Nuxt 带进来。

新的 Vue CLI 非常棒，可以立即启动并运行。再加上我最喜欢的 CSS 框架， [TailwindCSS](https://tailwindcss.com/) ，一切都在飞速发展。直到我决定减少我的包大小。

由于 TailwindCSS 的工作方式，默认情况下整个框架都包含在内。您可以添加一些配置选项来添加或删除颜色、响应状态等。然而，一种更简单的处理方式是使用 [PurgeCSS](https://github.com/FullHuman/purgecss) 。它会检查你的 HTML(在我的例子中是 Vue)文件，看看你使用的 CSS 选择器，并从你的 CSS 中删除所有其他的选择器。这应该会导致你的 TailwindCSS 输出很小，只是你使用的类！

Vue CLI 3 的一个新项目使用 [postcss-load-config](https://github.com/michael-ciniawsky/postcss-load-config) 来处理 postcss 插件。然而，使用你的`package.json`来配置插件的默认方式似乎并不适用于 PostCSS PurgeCSS [插件](https://github.com/FullHuman/postcss-purgecss)。幸运的是，postcss-load-config 允许您使用许多不同类型的配置文件。切换到 JavaScript 版本(`.postcssrc.js`)给了我下面的配置，它允许你在你的 Vue 文件中使用 TailwindCSS，但是也从生成的包中删除了未使用的 CSS。

```
const tailwindcss = require('tailwindcss')
const purgecss = require('@fullhuman/postcss-purgecss')
const autoprefixer = require('autoprefixer')
const postcssImport = require('postcss-import')

module.exports = {
  plugins: [
    postcssImport,
    tailwindcss('./tailwind.js'),
    purgecss({
      content: ['./src/**/*.vue'],
      extractors: [
        {
            extractor: class TailwindExtractor {
                static extract(content) {
                    return content.match(/[A-z0-9-:\/]+/g) || [];
                }
            },
            extensions: ['vue']
        }
      ]
    }),
    autoprefixer
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

这花了我很长时间才弄明白，但好处是深远的！在添加 PurgeCSS 之前，我的 CSS 包是 50.12 kb 的 Gzipped，在 PurgeCSS 之后，它只有 2.33 kb——大小减少了 20 倍！

**更新:**定制提取器是 Tailwind 的响应类所必需的，默认情况下会被去掉！

-

最初发布在我的[网站](https://delport.ca/2018/vue-tailwindcss-purgecss)