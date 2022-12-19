# 使用 Django 2 创建 React App 2

> 原文：<https://dev.to/josefrousek/using-create-react-app-2-with-django-2-3h1>

我终于有机会在一个项目中使用 Django 和 React 应用程序。有一个帮助整合 CRA 和姜戈的软件包组合。

### 网络包-捆绑包-跟踪器+django-网络包-加载器

`django-webpack-loader`和`webpack-bundle-tracker`以如下方式协同工作。webpack-bundle-tracker 插件发出有关 webpack 编译过程和结果的信息，因此 django-webpack-loader 可以使用这些信息。要将`webpack-bundle-tracker`添加到 CRA，我们需要在 webpack 上挂一个插件。

### CRA 配置

生成您的 CRA 应用程序后，我们从`react-app-rewired`配置开始。Rewired 使用`config-overrides.js`文件连接到 webpack 配置。除了增加`BundleTracker`之外，我们还需要做一个改动。不完全支持新的 [splitChunks](https://medium.com/webpack/webpack-4-code-splitting-chunk-graph-and-the-splitchunks-optimization-be739a861366) 配置，所以我们只能使用命名的 Chunks。

```
var BundleTracker = require('webpack-bundle-tracker');

module.exports = {
  webpack: (config, env) => {

    config.plugins.push(
      new BundleTracker({
        path: __dirname,
        filename: './build/webpack-stats.json',
      }),
    );

    config.optimization.splitChunks.name = 'vendors';

    return config;
  },
}; 
```

在运行`npm start`之后，我们应该在构建文件夹中看到包含我们需要的所有数据的附加文件`webpack-stats.json`。

### Django 配置

使用[管道](https://packaging.python.org/tutorials/managing-dependencies/)或管道安装`django-webpack-loader`。然后去你的`settings.py`。将 webpack_loader 添加到 INSTALLED_APPS。这将使我们能够使用模板标签。

```
INSTALLED_APPS = [
    ...
    'webpack_loader',
] 
```

#### 项目结构

我的项目目录结构如下所示。我决定把我的 CRA 应用程序放到`frontend`文件夹中。

```
├── Pipfile
├── Pipfile.lock
├── README.md
├── my_django_app
│   ├── __init__.py
│   ├── context_processors.py
│   ├── settings.py
│   ├── urls.py
│   ├── utils.py
│   └── wsgi.py
├── frontend
│   ├── build
│   ├── config-overrides.js
│   ├── node_modules
│   ├── package-lock.json
│   ├── package.json
│   ├── public
│   └── src
└── manage.py 
```

使用以下结构，我们可以按以下方式配置 webpack 加载。

```
STATICFILES_DIRS = [
    # So Django knows about webpack files
    os.path.join(BASE_DIR, "frontend", "build", "static"),
]

WEBPACK_LOADER = {
    "DEFAULT": {
        "CACHE": not DEBUG,
        "BUNDLE_DIR_NAME": "frontend/build/static/",  # must end with slash
        "STATS_FILE": os.path.join(BASE_DIR, "frontend", "build", "webpack-stats.json"),
    }
} 
```

### 用法

在 Django 模板中，我们可以使用`render_bundle`来加载我们的 CRA 应用程序。

```
{% raw %}
{%  load  render_bundle  from  webpack_loader  %}
{%  render_bundle  'vendors'  %}
{%  render_bundle  'main'  %}
{%  render_bundle  'runtime~main'  %}
{% endraw %} 
```

当构建过程失败时，它还会显示 webpack 错误。

[![Webpack error in Django](../Images/95777009a321bec9a2561c7437ce5d99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K65Dqh9L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rousek.name/public/django-webpack-error.png)

我很高兴让姜戈和 CRA 以这种方式合作，我只能推荐这种方式。

#### 项目

*   django-webpack-loader
*   [网络包-捆绑包-跟踪器](https://github.com/owais/webpack-bundle-tracker)