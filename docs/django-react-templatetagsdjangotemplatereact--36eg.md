# 在 django-react-templalaters 中通过 djangotemplate 呈现 react 组件

> 原文：<https://dev.to/ozwsei/django-react-templatetagsdjangotemplatereact--36eg>

## 前言

这篇文章是 django advent calendar 12 第 12 天的文章。

这次请允许我介绍 django-teact-templatetags。

[https://github.com/Frojd/django-react-templatetags](https://github.com/Frojd/django-react-templatetags)

## Special

*   从服务器端对 django template 进行响应
*   可将 python 模型转换为传递给 components 的数据的 Mixin
*   支持 SSR (未验证)

虽然功能只有这么多，但如果您想在非 spa APP 应用程序上部署 react 或进行 SSR，它将非常有用。

## 使用方法

基本的にREADMEの通りにすればok
[https://github . com/frojd/django-react-template tags/blob/develop/readme . MD](https://github.com/Frojd/django-react-templatetags/blob/develop/README.md)

*   已安装的应用程序に追加

```
INSTALLED_APPS = (
    # ...
    'django_react_templatetags',
) 
```

*   上下文处理器に追加

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            'templates...',
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'debug': True,
            'context_processors': [
                ...
                'django_react_templatetags.context_processors.react_context_processor',
            ],
        },
    },
] 
```

*   姜戈 templateに负载反应と `{% react_render component="Component" props=my_data %}` を追加

```
 {% load react %}

<body>
<p>...something</p>
{% react_render component="Component" props=my_data %}
{% react_print %}
</body> 
```

*   modelからreact-componentに変換

READMEを参考に驻を代表处実装するだけです。
[https://github . com/frojd/django-react-template tags #与模型一起工作](https://github.com/Frojd/django-react-templatetags#working-with-models)

## 试着用了一下的为难之处

我想如果把前端做成现在的样子的话，会从 webpack 和 gulp 编译，用 minify 读取 javascript。

此时，js 的顶级经常成为本地封闭，react-component 不在服务器上绘制的 html 范围内。

最终，我们在构建的 javascript 中为浏览器的 window 变量赋值了顶层 ReactComponent (虽然我们想尽量避免)。

```
// こんなかんじのコードをrootに置く
const RootComponent = require("./Root")

window.RootComponent= RootComponent 
```

## 总结

库本身很薄，只要在设计上下功夫就很容易舍弃，从 python 的模型到 javascript 的数据传递都可以得到支持，所以我个人认为这是非常蚂蚁的选择。

我认为特别适合在传统的 WebApp 上部分导入 React 的情况等。

另外，虽然这次没有尝试，但是想进行 SSR 的情况也可以进入选项。