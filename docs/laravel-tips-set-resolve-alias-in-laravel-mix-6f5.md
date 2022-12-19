# Laravel 提示:在 Laravel 混合中设置解析别名

> 原文：<https://dev.to/acro5piano/laravel-tips-set-resolve-alias-in-laravel-mix-6f5>

这不完全是关于 Laravel，而是 Webpack，尽管如此，我认为我应该在我的项目开始时设置它。

# 先决条件

与...一起发展

*   Laravel 5.4
*   网络包(Laravel mix)

# 问题

我习惯在任何`.js`文件中相对指定一个文件的路径:

```
// resources/assets/vue/components/Deep/Path/To/Component.vue

import FooComponent from '../../FooComponent.vue' 
```

Enter fullscreen mode Exit fullscreen mode

这很难理解，如果我改变目录结构，我必须改变每个路径。

...而且看起来很丑。

# 解

在`webpack.mix.js`的开头添加以下配置。

```
// webpack.mix.js

const { mix } = require('laravel-mix')

mix.webpackConfig({
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': __dirname + '/resources/assets/js'
    },
  },
})

// ...config follows 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以这样导入:

```
// resources/assets/vue/components/Deep/Path/To/Component.vue

import FooComponent from '@/components/FooComponent' 
```

Enter fullscreen mode Exit fullscreen mode

我发现`vue-cli`使用这个配置。默认情况下，它将解析别名`@`设置为`src`。

[https://github . com/vuej-templates/web package/blob/f 21376d/template/build/web package . base . conf . js # l40](https://github.com/vuejs-templates/webpack/blob/f21376d/template/build/webpack.base.conf.js#L40)

希望这个配置成为 Laravel Mix 的默认配置。我想我会发一份公关...

# 参

*   [https://readouble.com/laravel/5.4/ja/mix.html](https://readouble.com/laravel/5.4/ja/mix.html)
*   [https://github . com/Jeffrey way/laravel-mix/blob/master/setup/web pack . config . js # L209](https://github.com/JeffreyWay/laravel-mix/blob/master/setup/webpack.config.js#L209)