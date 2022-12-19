# ★在刀片视图中使用定制的 html 组件

> 原文：<https://dev.to/freekmurze/use-custom-html-components-in-your-blade-views-2c3l>

今天，我们发布了最新的软件包 BladeX。简而言之，这个包为你提供了一个简单的类似 html 的方法来在你的 Blade 视图中呈现定制的 html 组件。在这篇博文中，我想向你介绍这个包。

## 第一个例子

当构建一个服务器渲染的应用程序时，你可能会得到各种你想要重用的 html。想想表单组件、警告和整个布局。有多种方法可以使这样的 html 可重用。你可以简单地创建自己的自定义函数，或者使用[的 html 构建器](https://github.com/spatie/laravel-html)，或者使用 Blade 的 [`@include`](https://laravel.com/docs/5.7/blade#including-sub-views) 或者 [`@component`](https://laravel.com/docs/5.7/blade#components-and-slots) 指令。

尽管它有时被视为一门精确的科学，但似乎每个程序员在如何处理 html 方面都有自己的个人偏好。在我看来，上述所有选项都是有效的。在我们自己的大多数项目中，我们都使用了它们的组合。

我们尤其喜欢 Blade 的`@component`指令。受 Vue 的启发，Blade 组件是重用 html 的绝佳方式。这里有一个如何使用它的简单例子。让我们创建一个警报组件！

```
{{-- resources/views/components/myAlert.blade.php --}}
<div :class="$type">
   {{ $message }}
</div> 
```

使用`@component`指令，你可以像这样使用组件。

```
{{-- in a view --}}
@component('components/myAlert', [
   'type' => 'error',
   'message' => $message
])
@endcomponent 
```

这个指令并不难使用，但是它非常像 PHP。如果我们可以像在 Vue 中一样使用 html 一样的语法来使用那个组件，那不是很好吗？

随着[我们的 BladeX 包](https://github.com/spatie/laravel-blade-x)的安装，你可以像这样使用`myAlert`组件:

```
<my-alert type="error" :message="$message" /> 
```

使用类似 html 的语法，你将需要更少的字符，并且(在我看来)模板变得更具可读性。

### 使用变量

使用 BladeX 组件时，所有属性都将作为变量传递给底层的 Blade 视图。

```
{{-- the `myAlert` view will receive a variable named `type` with a value of `error` --}}

<my-alert type="error"> 
```

如果你想传递一个 php 变量或者一些需要计算的东西，你必须在属性名前加上前缀`:`。我们借用了 Vue 的语法。

```
{{-- the `myAlert` view will receive the contents of `$message` --}}
<my-alert type="error" :message="$message">

{{-- the `myAlert` view will receive the uppercased contents of `$message` --}}
<my-alert type="error" :message="strtoupper($message)"> 
```

## 使用插槽

BladeX 也支持插槽。该布局组件

```
{{-- resources/views/components/layout.blade.php --}}

<div>
    <h1>{{ $title }}</h1>
    <div class="flex">
        <div class="w-1/3">
            {{ $sidebar }}
        </div>
        <div class="w-2/3">
            {{ $slot }}
        </div>
    </div>
    <footer>
        {{ $footer }}
    </footer>
</div> 
```

可以这样用在你的观点里:

```
<layout title="Zed's chopper">
    <slot name="sidebar">
        <ul>
            <li>Home</li>
            <li>Contact</li>
        </ul>
    </slot>

    <main class="content">Whose motorcycle is this?</main>

    <slot name="footer">It's not a motorcycle honey, it's a chopper.</slot>
</layout> 
```

## 前缀成分

少数[开发者](https://twitter.com/tnorthcutt/status/1046023164029878273)表达了他们的担忧，如果你使用 Vue 或 React，可能很难区分 BladeX 组件和 JavaScript 组件。如果您也有这种担心，那么您会很高兴地了解到这个包允许您为 BladeX 组件全局设置前缀。

设置全局前缀很容易。

```
BladeX::component('my-alert', 'components.myAlert');

BladeX::prefix('x'); 
```

您所有注册的组件现在都可以这样使用:

```
<x-my-alert message="Notice the prefix!" /> 
```

## 这一切是如何进行的

要真正使用 BladeX 组件，首先必须注册它。您可以在`AppServiceProvider`或您自己的服务提供商那里完成这项工作。

```
BladeX::component('my-alert', 'components.myAlert') 
```

在安装软件包时自动加载的`Spatie\BladeX\BladeXServiceProvider`中，我们[用我们自己的](https://github.com/spatie/laravel-blade-x/blob/50a14ad128ba1e5f09885bc115570c564be60aaf/src/BladeXServiceProvider.php#L15-L17) [BladeXCompiler](https://github.com/spatie/laravel-blade-x/blob/50a14ad128ba1e5f09885bc115570c564be60aaf/src/BladeXCompiler.php) 扩展了 Blade 的默认编译器。`BladeXCompiler` [循环遍历](https://github.com/spatie/laravel-blade-x/blob/50a14ad128ba1e5f09885bc115570c564be60aaf/src/BladeXCompiler.php#L20)所有注册的组件。该类包含[两个](https://github.com/spatie/laravel-blade-x/blob/master/src/BladeXCompiler.php#L30) [正则表达式](https://github.com/spatie/laravel-blade-x/blob/master/src/BladeXCompiler.php#L61)，由 [Alex](https://twitter.com/alexvanderbist) 和 [Seb](https://twitter.com/sebdedeyne) 精心制作，用来替换 BladeX 组件的用法，比如这个

```
<my-alert type="error" :message="$message" /> 
```

使用 Blade 的`@component`指令转换成等价代码。

```
@component('components/myAlert', [
   'type' => 'error',
   'message' => $message
])
@endcomponent 
```

之后，Blade 会像往常一样开始解析 html。缓存视图就可以了，所以根本不会有性能损失。因为所有这些都发生在任何 html 被发送到浏览器之前，所以像 Vue.js 这样的客户端框架不存在任何互操作问题。

## 我们是如何做到的

我为我们的软件包带来的功能感到自豪，但我同样为我们如何制作这个软件包感到自豪。html 生成的问题在周五的一次午餐中被讨论过。在同一次讨论中，产生了构建 BladeX 的想法。在[我们公司](https://spatie.be)每个人都被允许有一些时间从事开源工作。碰巧的是，每个人在那一周都还有一些开源时间。

几乎整个开发团队整个下午都坐在我们的会议室里构建这个包。尽管大多数包都是团队工作，但大多数包都有一个主要作者，团队的其他成员润色生成的代码。BladeX 是一个真正的团队成果，所有的作者在创建包的时候都做出了贡献。这真的是一次有趣的经历，我希望我们能再次以这种方式创建未来的软件包。

通常我们给我们的包起一个干巴巴的、描述性的名字，比如 [laravel-permission](https://github.com/spatie/laravel-permission) 、 [laravel-medialibrary](https://github.com/spatie/laravel-medialibrary) 或者 [laravel-backup](https://github.com/spatie/laravel-backup) 。如果我们按照这种命名方式，BladeX 应该叫做`laravel-html-components`或者类似的名字。在开发过程中，Alex 想出了 BladeX 这个名字。这听起来很糟糕，所以我们决定坚持下去。

## 关闭思绪

1.0.0 已被标记，但我们不会在此停止开发。在最新的[北南相遇播客](https://www.northmeetssouth.audio/48) [中，雅各布](https://twitter.com/jacobbennett)和[迈克尔](https://twitter.com/michaeldyrynda)讨论了 BladeX 并分享了一些有趣的想法。我们将研究与我们的 [laravel-view-components](https://github.com/spatie/laravel-view-components) 和/或 [Laravel 的本地视图组合器](https://laravel.com/docs/5.7/views#view-composers)的一些集成，以便在将数据传递到组件之前轻松地转换和获取数据。

如果你喜欢 [BladeX](https://github.com/spatie/laravel-blade-x) 去看看[我们团队之前做的所有其他开源包](https://spatie.be/open-source/packages)。如果你喜欢我们的工作，考虑通过 Patreon 支持我们[。](https://www.patreon.com/spatie)

我计划很快写一篇关于如何以现代方式构建公共 crud 接口的博文。那个帖子肯定会有 [BladeX](https://github.com/spatie/laravel-blade-x) 、 [laravel-query-builder](https://github.com/spatie/laravel-query-builder) 、 [laravel-view-models](https://github.com/spatie/laravel-view-models) 和我们的其他几个包。

如果你对 BladeX 有任何问题，欢迎使用下面的评论，或者在 GitHub 的回购上提出问题。