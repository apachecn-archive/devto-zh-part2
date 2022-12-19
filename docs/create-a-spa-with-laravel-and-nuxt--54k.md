# 用 Laravel 和 Nuxt 打造水疗中心

> 原文：<https://dev.to/skyrpex/create-a-spa-with-laravel-and-nuxt--54k>

在本教程中，我们将使用 [Laravel](https://laravel.com/) 作为 API，使用 [Nuxt](https://nuxtjs.org/) 作为单页面应用程序。他们可以一起工作，但一开始并不容易。如果您已经尝试过，让 HMR 无缝工作是一件痛苦的事情！为此，我创建了 [laravel-nuxt](https://github.com/skyrpex/laravel-nuxt) 和 [laravel-nuxt-js](https://github.com/skyrpex/laravel-nuxt-js) 。

有更多的理由使用这些软件包，例如当使用[拉勒维尔护照](https://laravel.com/docs/5.5/passport)和`CreateFreshApiToken`时。中间件将在使用`get` http 动词的`web`路线上创建一个`api_token` cookie，如果您不是从 Laravel 内部提供 SPA，这将是一个问题。

# 入门

## 安装 Laravel

让我们从全新的 Laravel 装置开始:

```
composer create-project laravel/laravel spa 
```

Enter fullscreen mode Exit fullscreen mode

用你的终端进入`spa`目录。

## 安装 laravel-nuxt(针对 PHP)

```
# cd spa
composer require pallares/laravel-nuxt 
```

Enter fullscreen mode Exit fullscreen mode

此包将被自动发现。如果您使用的是 Laravel 的旧版本，只需在`config/app.php`文件中添加服务提供商:

```
<?php
return [
    // ...
    'providers' => [
        // ...
        Pallares\LaravelNuxt\LaravelNuxtServiceProvider::class,
    ],
]; 
```

Enter fullscreen mode Exit fullscreen mode

添加将在`routes/web.php`文件中呈现 SPA 页面的备用路由。一定要删除框架自带的默认路由:

```
<?php
// Route::get('/', function () {
//     return view('welcome');
// });

Route::get(
    '{uri}',
    '\\'.Pallares\LaravelNuxt\Controllers\NuxtController::class
)->where('uri', '.*'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的后端已经准备好提供 Nuxt 将为您生成的已编译资产。现在，每条返回 404 的路线都将服务于我们的 SPA 页面。

## 安装 laravel-nuxt(用于 JS)

是时候安装 JS 包了。用以下内容替换您的`package.json`文件:

```
{  "private":  true,  "scripts":  {  "start":  "laravel-nuxt dev",  "build":  "laravel-nuxt build"  },  "dependencies":  {  "laravel-nuxt":  "^1.0.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

安装依赖项:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

`laravel-nuxt`包会为你安装 Nuxt，还有 [Vue](https://github.com/vuejs/vue) 、 [vue-router](https://github.com/vuejs/vue-router) 、 [@nuxtjs/axios](https://github.com/nuxt-community/axios-module) 等。让我们创建`nuxt.config.js`文件:

```
const laravelNuxt = require("laravel-nuxt");

module.exports = laravelNuxt({
  // Options such as mode, srcDir and generate.dir are already handled for you.
  modules: [],
  plugins: [],
}); 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，Nuxt 将在`resources/nuxt`目录中寻找源文件。

在`resources/nuxt/pages/index.vue` :
中创建 hello world 路线

```
<template>
  <h1>Hello {{ name }}!</h1>
</template>

<script>
export default {
  data: () => {
    return { name: 'world' };
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

最后，运行:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

转到 [http://localhost:8000](http://localhost:8000) 。你应该看到这个: [![hello-world-picture](../Images/2814a1d76d8888669bffabf0a631f098.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JLjSCl0Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pbrd.co/images/H7v0Mzl.jpg)

就是这样！Laravel artisan 的服务器和 Nuxt 的 dev 服务器已经启动，可以透明地协同工作。现在试着编辑你的主页吧，看到实时的重新加载是非常令人愉快的。

在幕后，Nuxt 的 dev 服务器代理每个对 Laravel 服务器的调用，包括 SPA 渲染。由于包含了`@nuxtjs/axios`模块(也有代理)，您可以正常地进行 API 调用。

# 从 SPA 调用 API

SPA 肯定需要调用我们的 API，所以让我们添加一个到`routes/api.php`的路由来检索用户信息:

```
<?php
Route::get('me', function () {
    // Let's return fake information.
    return [
        'name' => 'John Doe',
    ];
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，编辑`resources/nuxt/pages/index.vue` :

```
<template>
  <h1>Hello {{ user.name }}!</h1>
</template>

<script>
export default {
    // https://github.com/nuxt-community/axios-module
    async asyncData({ app }) {
        const user = await app.$axios.$get('api/me');
        return { user };
    },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。您的页面现在应该看起来像这样！

[![api-call-picture](../Images/94eeac1a4f85194ef795e004c0d367a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sLemr8zK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pbrd.co/images/H7v06bS.jpg)

> 为了保持教程的简单，我们在这里没有使用任何类型的认证。集成 Passport 在这里应该是微不足道的。

# 部署

如果您想部署您的应用程序，只需运行`npm run build`。编译后的资产将被放置在`public/_nuxt`目录中。

你可以用众所周知的 artisan 命令`php artisan serve`预览你的最终应用。

> 您可能想要将`.nuxt`和`public/_nuxt`目录添加到您的`.gitignore`中。

# 最后的想法

这是我的第一个教程。希望一切都清晰简洁！请不要犹豫，在这里提问或者在 laravel-nuxt 仓库中制造问题。谢谢！