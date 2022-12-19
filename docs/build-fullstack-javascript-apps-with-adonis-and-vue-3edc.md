# 用 Adonis 和 Vue 构建全栈 Javascript 应用

> 原文：<https://dev.to/michi/build-fullstack-javascript-apps-with-adonis-and-vue-3edc>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/build-fullstack-js-apps-with-adonis-and-vue)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

今天，我们想把两个惊人的框架结合起来，让我们只用 Javascript 就能构建干净的应用程序。Adonis 是一个受 Laravel 启发的用于 Node 的 web 框架，它继承了 Laravel 的许多特性，如 SQL ORM、认证、迁移、mvc 结构等。
[Vue](https://vuejs.org/) 是一个前端 web 框架，用于构建单页面应用程序(SPA)或一般来说需要交互性的应用程序。就像 React 一样，它改变了你思考和设计前端的方式。

您可以在这里找到本教程的代码。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【mz anggl】](https://github.com/MZanggl)/[【Adonis-vista 演示】](https://github.com/MZanggl/adonis-vue-demo)

### Adonis / Vue 项目的演示和蓝图

<article class="markdown-body entry-content container-lg" itemprop="text">

# Adonis 视图演示

这是 AdonisJs 和 Vue 的完整样板/蓝图/演示。看看[的博客文章](https://dev.to/mzanggl/build-fullstack-javascript-apps-with-adonis-and-vue-3edc)，看看它是如何设置的。

### 迁移

运行以下命令来运行启动迁移。

```
adonis migration:run
```

Enter fullscreen mode Exit fullscreen mode

### 启动应用程序

```
npm run dev
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/MZanggl/adonis-vue-demo)

## 项目设置

#### 安装 Adonis CLI

```
npm install -g @adonisjs/cli 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建 Adonis 项目

```
adonis new fullstack-app
cd fullstack-app 
```

Enter fullscreen mode Exit fullscreen mode

## Webpack

#### 文件结构

我们希望在`resources/assets/js`中创建所有的前端 JavaScript 和 Vue 文件。Webpack 将传输这些文件，并将它们放入`public/js`中。
让我们创建必要的目录和文件

```
mkdir resources/assets/js -p
touch resources/assets/js/main.js 
```

Enter fullscreen mode Exit fullscreen mode

```
// resources/assets/js/main.js

const test = 1
console.log(test) 
```

Enter fullscreen mode Exit fullscreen mode

## 获取 Webpack 滚动

来自富裕家庭的人可能对`Laravel-Mix`很熟悉。好的一面是我们也可以在 Adonis 项目中使用 Laravel Mix。它消除了 webpack 的许多配置问题，非常适合 80/20 用例。
从安装依赖项开始，将`webpack.mix.js`复制到项目的根目录。

```
npm install laravel-mix --save
cp node_modules/laravel-mix/setup/webpack.mix.js . 
```

Enter fullscreen mode Exit fullscreen mode

是我们所有配置发生的地方。我们来配置一下

```
// webpack.mix.js

let mix = require('laravel-mix');

// setting the public directory to public (this is where the mix-manifest.json gets created)
mix.setPublicPath('public')
// transpiling, babelling, minifying and creating the public/js/main.js out of our assets
    .js('resources/assets/js/main.js', 'public/js')

// aliases so instead of e.g. '../../components/test' we can import files like '@/components/test'
mix.webpackConfig({
    resolve: {
        alias: {
            "@": path.resolve(
                __dirname,
                "resources/assets/js"
            ),
            "@sass": path.resolve(
                __dirname,
                "resources/assets/sass"
            ),
        }
    }
 }); 
```

Enter fullscreen mode Exit fullscreen mode

此外，一定要删除现有的例子，以避免崩溃

```
mix.js('src/app.js', 'dist/').sass('src/app.scss', 'dist/'); 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加必要的脚本

让我们给我们的`package.json`添加一些脚本，让我们传输我们的资产。在`scripts`内添加以下几行。

```
// package.json

"assets-dev": "node node_modules/cross-env/dist/bin/cross-env.js NODE_ENV=development webpack --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
"assets-watch": "node node_modules/cross-env/dist/bin/cross-env.js NODE_ENV=development webpack --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
"assets-hot": "node node_modules/cross-env/dist/bin/cross-env.js NODE_ENV=development webpack-dev-server --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
"assets-production": "node node_modules/cross-env/dist/bin/cross-env.js NODE_ENV=production webpack --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在开发过程中执行`npm run assets-watch`来监视我们的文件。运行该命令应该会创建两个文件:`public/mix-manifest.json`和`public/js/main.js`。最好忽略这些生成的文件，因为在团队中工作时，它们会导致很多合并冲突...

## 路由

由于我们正在构建一个 SPA，Adonis 应该只处理以`/api`为前缀的路由。所有其他路由将被转发到 vue，vue 将负责客户端的路由。
进入`start/routes.js`并将下面的片段添加到其中

```
// start/routes.js

// all api routes (for real endpoints make sure to use controllers)
Route.get("hello", () => {
    return { greeting: "Hello from the backend" };
}).prefix("api")
Route.post("post-example", () => {
    return { greeting: "Nice post!" };
}).prefix("api")

// This has to be the last route
Route.any('*', ({view}) =>  view.render('app')) 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这一行:`Route.any('*', ({view}) => view.render('app'))`

星号表示`everything that has not been declared before`。因此，至关重要的是，这是最后宣布的路线。

`view.render` `app`中的参数是我们 SPA 的起点，我们将在这里加载之前创建的`main.js`文件。Adonis 使用与 blade 非常相似的 Edge 模板引擎。让我们创建我们的视图

```
touch resources/views/app.edge 
```

Enter fullscreen mode Exit fullscreen mode

```
// resources/views/app.edge

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Adonis & Vue App
</head>
<body>
    <div id="app"></div>
    {{ script('/js/main.js') }}
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

全局`script`函数在`resources/assets`中寻找文件，并自动为我们创建脚本标签。

## 视图设置

让我们安装 vue 和 vue 路由器

```
npm install vue vue-router --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

并在`resources/assets/js/main.js`中初始化 vue

```
// resources/assets/js/main.js

import Vue from 'vue'
import router from './router'
import App from '@/components/layout/App'

Vue.config.productionTip = false

new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

为了实现这一点，我们必须创建`App.vue`。所有布局相关的东西都放在这里，我们只是保持它超级简单，只包括路由器。

```
mkdir resources/assets/js/components/layout -p
touch resources/assets/js/components/layout/App.vue 
```

Enter fullscreen mode Exit fullscreen mode

```
// /resources/assets/js/components/layout/App.vue

<template>
    <router-view></router-view>
</template>

<script>
export default {
  name: 'App'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我们还必须创建客户端路由器配置

```
mkdir resources/assets/js/router
touch resources/assets/js/router/index.js 
```

Enter fullscreen mode Exit fullscreen mode

```
// resources/assets/js/router/index.js

import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

export default new Router({
    mode: 'history', // use HTML5 history instead of hashes
    routes: [
        // all routes
    ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们在`resources/assets/js/components`中创建两个测试组件来测试路由器。

```
touch resources/assets/js/components/Index.vue
touch resources/assets/js/components/About.vue 
```

Enter fullscreen mode Exit fullscreen mode

```
// resources/assets/js/components/Index.vue

<template>
    <div>
        <h2>Index</h2>
        <router-link to="/about">To About page</router-link>
    </div>
</template>

<script>
export default {
    name: 'Index',
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

而第二个

```
// /resources/assets/js/components/About.vue

<template>
    <div>
        <h2>About</h2>
        <router-link to="/">back To index page</router-link>
    </div>
</template>

<script>
export default {
    name: 'About',
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

索引组件有一个重定向到“关于”页面的链接，反之亦然。
让我们回到路由器配置，将这两个组件添加到路由中。

```
// resources/assets/js/router/index.js

// ... other imports
import Index from '@/components/Index'
import About from '@/components/About'

export default new Router({
    // ... other settings
    routes: [
        {
            path: '/',
            name: 'Index',
            component: Index
        },
        {
            path: '/about',
            name: 'About',
            component: About
        },
    ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 发射

让我们启动我们的应用程序，看看我们得到了什么。确保运行`npm run assets-watch`，然后使用
启动 Adonis 服务器

```
adonis serve --dev 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Adonis 使用端口 3333，所以转到`localhost:3333`，您应该能够在索引和 about 页面之间导航。
尝试进入`localhost:3333/api/hello`，你应该在 JSON 中得到如下响应:`{ greeting: "Nice post!" }`。

## 奖金

我们就要完成了，为了让一切顺利进行，我们还需要做一些小事情:

*   CSRF 保护
*   缓存破坏
*   部署(Heroku)

#### CSRF 保护

由于我们没有使用无状态(JWT)认证，我们必须使用 [CSRF 保护](https://adonisjs.com/docs/4.1/csrf)来保护我们的 *POST* 、 *PUT* 和 *DELETE* 请求。让我们尝试获取我们之前创建的 POST 路由。您可以从 devtools 中完成此操作。

```
fetch('/api/post-example', { method: 'post' }) 
```

Enter fullscreen mode Exit fullscreen mode

因为我们还没有添加 CSRF 令牌，所以得到的响应会类似于`POST http://127.0.0.1:3333/api/post-example 403 (Forbidden)`这样。Adonis 将这个令牌保存在 cookies 中，所以让我们安装一个 npm 模块来帮助我们检索它。

```
npm install browser-cookies --save 
```

Enter fullscreen mode Exit fullscreen mode

要安装 npm 模块，我建议首先关闭 Adonis 服务器。

接下来，将以下代码添加到`main.js`

```
// resources/assets/js/main.js

// ... other code

import cookies from 'browser-cookies';

(async () => {
    const csrf = cookies.get('XSRF-TOKEN')
    const response = await fetch('/api/post-example', {
        method: 'post',
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json',
            'x-xsrf-token': csrf,
        },
    });

    const body = await response.json()

    console.log(body)
})() 
```

Enter fullscreen mode Exit fullscreen mode

这应该会在控制台中给出我们想要的结果！我建议将它提取到一个模块中。当然你也可以用 axios 这样的库来代替。

#### 缓存破坏

[缓存破坏](https://www.keycdn.com/support/what-is-cache-busting)是确保我们的访问者总是获得我们提供的最新资产的一种方式。
要启用它，首先将下面的代码添加到`webpack.mix.js`

```
// webpack.mix.js

mix.version() 
```

Enter fullscreen mode Exit fullscreen mode

如果你*重启* `npm run assets-watch`，你应该看到`mix-manifest.json`
里面有变化

```
//  public/mix-manifest.json  {  "/js/main.js":  "/js/main.js?id=e8f10cde10741ed1abfc"  } 
```

Enter fullscreen mode Exit fullscreen mode

每当我们对`main.js`进行修改时，散列就会改变。现在我们必须创建一个钩子，这样我们就可以在视图中读取这个 JSON 文件。

```
touch start/hooks.js 
```

Enter fullscreen mode Exit fullscreen mode

```
const { hooks } = require('@adonisjs/ignitor')
const Helpers = use('Helpers')

const mixManifest = require(Helpers.publicPath('mix-manifest.json'))

hooks.after.providersBooted(async () => {
    const View = use('View')
    View.global('versionjs', (filename) => {
        filename = `/js/${filename}.js`
        if (!mixManifest.hasOwnProperty(filename)) {
            throw new Error('Could not find asset for versioning' + filename)
        }

        return mixManifest[filename]
    })

    View.global('versioncss', (filename) => {
        filename = `/css/${filename}.css`
        if (!mixManifest.hasOwnProperty(filename)) {
            throw new Error('Could not find asset for versioning' + filename)
        }

        return mixManifest[filename]
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这将创建两个我们可以在视图中使用的全局方法。转到
`resources/assets/views/app.edge`并替换

```
{{ script('/js/main.js') }} 
```

Enter fullscreen mode Exit fullscreen mode

用

```
{{ script(versionjs('main')) }} 
```

Enter fullscreen mode Exit fullscreen mode

这就是破解缓存的全部内容。

#### 部署

已经有一篇关于在 Heroku 上部署 Adonis 应用的文章[。因为我们将资产放在同一个项目上，所以我们必须添加一两个东西来使部署顺利进行。在`package.json`
内的`scripts`下添加以下代码](https://scotch.io/tutorials/deploying-adonisjs-apps-to-heroku)

```
//  package.json  "heroku-postbuild":  "npm run assets-production" 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 Heroku 在部署过程中转移我们的资产。如果你没有使用 Heroku，其他服务可能会提供类似的解决方案。

***以防部署失败...*T3】**

您可能需要配置 Heroku 应用程序来安装开发依赖项。您可以通过执行以下命令
对其进行配置

```
heroku config:set NPM_CONFIG_PRODUCTION=false YARN_PRODUCTION=false 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以直接在 Heroku 网站上设置配置。

这就是全部了。

要跳过所有设置，您可以简单地用
克隆演示回购

```
adonis new application-name --blueprint=MZanggl/adonis-vue-demo 
```

Enter fullscreen mode Exit fullscreen mode

让我知道，如果你对已经包括注册路线和控制器，vue 化布局，vue 商店等蓝图感兴趣。

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。