# Vue.js 运行在 Symfony4 上并创建可重用的组件

> 原文：<https://dev.to/_mertsimsek/vuejs-running-on-symfony4-and-creating-reusable-components-5fk5>

正如我在前两篇文章中所说，我正在开发一个词汇表 web 应用程序。我们已经看到在 Docker Compose 上运行 [Symfony4。在这篇文章中，我们将把 Vue.js 加入 Symfony4。为此，我们将安装 Webpack Encore 和 Javascript 的依赖项。我过去曾通过链接分享过我的信息来源。](https://dev.to/_mertsimsek/symfony-4-running-on-docker-compose-2fb3)

首先，为了运行这些命令来安装 **Webpack Encore** 。**纱**以及**作曲**都应该安装你的设备。

```
composer require webpack-encore
yarn install 
```

Enter fullscreen mode Exit fullscreen mode

它会创建 **webpack.config.js** 文件，我是这样编辑的。

```
var Encore = require('@symfony/webpack-encore');

Encore
    // directory where compiled assets will be stored
    .setOutputPath('public/build/')

    // the public path used by the web server to access the previous directory
    .setPublicPath('/build')
    // only needed for CDN's or sub-directory deploy
    //.setManifestKeyPrefix('build/')

    /*
     * ENTRY CONFIG
     *
     * Add 1 entry for each "page" of your app
     * (including one that's included on every page - e.g. "app")
     *
     * Each entry will result in one JavaScript file (e.g. app.js)
     * and one CSS file (e.g. app.css) if you JavaScript imports CSS.
     */
    // will create public/build/app.js and public/build/app.css
    .addEntry('dashboard', './assets/js/dashboard.js')

    // allow legacy applications to use $/jQuery as a global variable
    .autoProvidejQuery()

    // enable source maps during development
    .enableSourceMaps(!Encore.isProduction())

    // empty the outputPath dir before each build
    .cleanupOutputBeforeBuild()

    // show OS notifications when builds finish/fail
    .enableBuildNotifications()

    .enableVueLoader()

    .enableSassLoader()
    .enableLessLoader()

module.exports = Encore.getWebpackConfig(); 
```

Enter fullscreen mode Exit fullscreen mode

然后这个配置，我们应该安装 Javascript 的依赖项。我在展示我的 **package.json** 。

```
{
  "devDependencies": {
    "@symfony/webpack-encore": "^0.19.0",
    "less": "^3.8.1",
    "less-loader": "^4.1.0",
    "node-sass": "^4.9.3",
    "sass-loader": "^7.1.0",
    "vue": "^2.5.17",
    "vue-loader": "^14",
    "vue-template-compiler": "^2.5.17",
    "webpack-notifier": "^1.6.0"
  },
  "license": "UNLICENSED",
  "private": true,
  "scripts": {
    "dev-server": "encore dev-server",
    "dev": "encore dev",
    "watch": "encore dev --watch",
    "build": "encore production"
  },
  "dependencies": {
    "axios": "^0.18.0",
    "bootstrap": "^4.1.3",
    "font-awesome": "^4.7.0",
    "graceful-fs": "^4.1.11",
    "izitoast": "^1.4.0",
    "jquery": "2.2.4",
    "jquery-easing": "^0.0.1",
    "popper.js": "^1.14.4",
    "select2": "^4.0.6-rc.1",
    "vue-router": "^3.0.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您只需运行这个命令。

```
yarn install 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有 Bootstrap 4、Vue.js 2、Vue-cli 和许多我们需要的包。

从现在开始，我们可以开始创建 Vue.js 的结构。我正在创建 **assets/js/layout.js** 文件。这几行表示那个文件？

```
'use strict';

const $ = require('jquery');
import 'bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.min.js';
import 'bootstrap/dist/js/bootstrap.bundle.min.js'
import 'jquery-easing';
import './sb-admin.min.js';
import '../css/sb-admin.min.css' 
```

Enter fullscreen mode Exit fullscreen mode

所以，这些行是我到处需要的库。然后，我将创建 **assets/js/dashboard.js** 文件。像这样。

```
'use strict';

require('./layout');

import Vue from 'vue';

import Setting from '../components/user/Setting';
import Select2 from '../components/form_items/Select2';

new Vue({
    el: '#app-dashboard',
    components: {Select2,Setting}
}).$mount('#app-dashboard'); 
```

Enter fullscreen mode Exit fullscreen mode

这个文件将初始化我们的 Vue.js 项目。现在，我们将创建我们的组件。我需要一个可以在任何地方使用的组件。我已经将一个**选择**组件写入**资产/组件/表单 _ 项目/选择 2.vue** 。

```
<template>
    <select class="select2 col-3">
        <option v-for="(value, key) in option">{{ value + " (" + key + ")" }}</option>
    </select> </template> 
<script>

    import 'select2';
    import 'select2/dist/css/select2.css';

    export default {
        name: "select2",
        props: {
            option: Object
        },
        data() {
            return {}
        },
        mounted: function () {
            $('.select2').select2();
        },
        methods: {},
        created: function () {
        }
    }
</script> 
<style scoped>

</style> 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们可以将**选项的**值从 out 中传递出去。在 **Setting.vue** 文件中会发送一个与 Axios 库的 XHR 请求。然后，它将这些值发送到 Select2 组件。首先，我展示了设置组件。

```
<template>

    <form>
        <div class="form-group">
            <label>Target language: </label>
            <select2 :option="optns"></select2>
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>

</template> 
<script>
    import Select2 from '../form_items/Select2';
    import axios from 'axios';

    export default {
        components: {Select2},
        name: "setting",
        data() {
            return {
                optns: {}
            }
        },
        created() {
            axios.get('/google/translate/api')
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.optns = response.data.languages
                })
                .catch(e => {
                    this.errors.push(e)
                });
        },
        methods: {}
    }
</script> 
<style scoped>

</style> 
```

Enter fullscreen mode Exit fullscreen mode

好吧，从现在开始，我们可以创建一个 Symfony 控制器，以便我们将获得语言。

```
bin/console make:controller 
```

Enter fullscreen mode Exit fullscreen mode

创建控制器后，我的控制器是这样的。

```
<?php

namespace App\Controller;

use Google\Cloud\Translate\TranslateClient;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\Routing\Annotation\Route;

/**
 * Class GoogleTranslateApiController
 * @package App\Controller
 */
class GoogleTranslateApiController extends AbstractController
{
    /**
     * @Route("/google/translate/api", name="google_translate_api")
     */
    public function index()
    {
        $translate = new TranslateClient();
        $targetLanguage = 'en';
        $result = $translate->localizedLanguages([
            'target' => $targetLanguage,
        ]);

        $languages = array();
        foreach ($result as $lang) {
            $languages[$lang['code']] = $lang['name'];
        }

        return new JsonResponse(
            array(
                'status' => true,
                'languages' => $languages
            ), 200);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，我们正在使用这个命令来构建 Webpack。

```
yarn encore dev --watch 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将创建 3 种类型的文件。

*   **public/build/dashboard . CSS**
*   **public/build/dashboard . js**
*   **public/build/manifest . JSON**

好的，我们可以加载我们的小枝文件。我的用户控制器是这样的。

```
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;

/**
 * Class UserController
 * @package App\Controller
 *
 * @Route("/user")
 */
class UserController extends AbstractController
{
    /**
     * @Route("/setting", name="setting")
     */
    public function index()
    {
        return $this->render('user/setting.html.twig');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它只是加载树枝文件，我的树枝文件意味着。

```
{% extends 'base.html.twig' %}

{% block head %}
{% endblock %}

{% block body %}
    <div id="app-dashboard">
        <setting></setting>
    </div>
{% endblock %}

{% block javascripts %}
    <script src="{{ asset('build/dashboard.js') }}" type="text/javascript"></script>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们正在添加 builded **dashbard.js** 。而我的 **base.html.twig** 就是这个意思。

```
<html lang="en">

<head>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    SB Admin - Dashboard

    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.2.0/css/all.css"
          integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ" crossorigin="anonymous">
    <!-- Bootstrap core CSS-->
    <link href="{{ asset('build/dashboard.css') }}" rel="stylesheet">

    {% block head %}{% endblock %}

</head>
<body>
<nav class="navbar navbar-expand navbar-dark bg-dark static-top">

    <a class="navbar-brand mr-1" href="index.html">Start Bootstrap</a>

    <button class="btn btn-link btn-sm text-white order-1 order-sm-0" id="sidebarToggle" href="#">
        <i class="fas fa-bars"></i>
    </button>

    <!-- Navbar -->
    <ul class="navbar-nav ml-auto ml-md-0">
        <li class="nav-item dropdown no-arrow">
        </li>
    </ul>

</nav>

<div id="wrapper">

    <!-- Sidebar -->
    <ul class="sidebar navbar-nav">
        <li class="nav-item active">
            <a class="nav-link" href="index.html">
                <i class="fas fa-fw fa-tachometer-alt"></i>
                <span>Dashboard</span>
            </a>
        </li>
        <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="pagesDropdown" role="button" data-toggle="dropdown"
               aria-haspopup="true" aria-expanded="false">
                <i class="fas fa-fw fa-folder"></i>
                <span>Pages</span>
            </a>
            <div class="dropdown-menu" aria-labelledby="pagesDropdown">
                <h6 class="dropdown-header">Login Screens:</h6>
                <a class="dropdown-item" href="login.html">Login</a>
                <a class="dropdown-item" href="register.html">Register</a>
            </div>
        </li>
        <li class="nav-item">
            <a class="nav-link" href="charts.html">
                <i class="fas fa-fw fa-chart-area"></i>
                <span>Charts</span></a>
        </li>
    </ul>

    <div id="content-wrapper">

        <div class="container-fluid">
            {% block body %}{% endblock %}
        </div>

        <!-- Sticky Footer -->
        <footer class="sticky-footer">
            <div class="container my-auto">
                <div class="copyright text-center my-auto">
                    <span>Copyright © https://mertblog.net 2018</span>
                </div>
            </div>
        </footer>

    </div>
    <!-- /.content-wrapper -->

</div>
{% block javascripts %}{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们再次运行这个命令。

```
yarn run encore dev --watch 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我们已经获得了这个观点。

[![alt text](img/1029fe5afd3dc4d7b2a6bd394e3fae74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OwLDBPTU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lj9k515smn9itaga6pew.png)

如您所见，从现在开始，我们有了一个可以在任何地方使用的 Select 2 组件。我们从根组件传递值。在这里，我们的根组件是**设置。Vue** 同样，我们可以用这种方式创建我们所需要的任何组件。

来源:

[https://symfony . com/doc/current/frontend/encore/simple-example . html](https://symfony.com/doc/current/frontend/encore/simple-example.html)
T3】https://knpuniversity.com/screencast/webpack-encore
[https://vuejs.org/v2/guide/](https://vuejs.org/v2/guide/)