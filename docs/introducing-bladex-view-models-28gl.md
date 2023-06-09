# BladeX 视图模型介绍

> 原文：<https://dev.to/freekmurze/introducing-bladex-view-models-28gl>

今天早些时候，我们发布了 [BladeX](https://github.com/spatie/laravel-blade-x) ，这是一个允许你使用 Vue 启发的语法来使用[刀片组件](https://laravel.com/docs/master/blade#components-and-slots)的包。在[我之前的博客](https://freek.dev/use-custom-html-components-in-your-blade-views)中读到了所有关于它的内容。

在最新的[北南相遇播客](https://www.northmeetssouth.audio/48) [中，雅各布](https://twitter.com/jacobbennett)和[迈克尔](https://twitter.com/michaeldyrynda)讨论了 BladeX，并想知道该包是否可以与[视图模型](https://github.com/spatie/laravel-view-models)或 [Laravel 的视图作曲家](https://laravel.com/docs/master/views#view-composers)结婚。我们的团队上周五也讨论了这个问题，但我们决定先发布 BladeX 的核心特性。在今天的发布之后，我们很快发现我们的项目中也需要某种视图模型支持。所以我们要工作了！

我很高兴地分享，BladeX 的 v1.1 现在已经支持视图模型。在这篇博文中，我想带你了解一下这个特性。

### 使用视图模式

视图模型用于在呈现 BladeX 组件之前转换数据。让我们用一个例子来说明用法。我们将呈现一个`select`元素来呈现一个带有一些国家的`select`元素。

要让 BladeX 组件使用视图模型，您需要在注册组件时添加对`viewModel`的调用。视图模型的类名被传递给该方法。

```
BladeX::component('select-field')->viewModel(SelectViewModel::class); 
```

在回顾组件的内容和视图模型本身之前，让我们先看看我们将如何使用组件。

```
@php
// in a real app this data would most likely come from a controller
$countries = [
    'be' => 'Belgium',
    'fr' => 'France',
    'nl' => 'The Netherlands',
];
@endphp

<select-field name="countries" :options="$countries" selected="fr" /> 
```

接下来，我们来看看`SelectViewModel::class`长什么样:

```
class SelectViewModel extends ViewModel
{
    /** @var string */
    public $name;

    /** @var array */
    public $options;

    /** @var string */
    public $selected;

    public function __construct(string $name, array $options, string $selected = null)
    {
        $this->name = $name;

        $this->options = $options;

        $this->selected = $selected;
    }

    public function isSelected(string $optionName): bool
    {
        return $optionName === $this->selected;
    }
} 
```

注意这个类扩展了`\Spatie\BladeX\ViewModel`。`select-field`上的每个属性都被传递给构造函数。这种传递是基于名字的，`name`属性将被传递给一个名为`$name`的构造函数参数，`options`属性将被传递给`$options`等等。任何其他争论都将在 ioc 容器之外解决。这对于依赖注入来说很方便。

视图模型的所有公共属性和方法都将被传递给呈现`select-field`组件的刀片视图。公共方法将作为闭包存储在变量中，该变量以视图模型中的公共方法命名。这是那个视图的样子。

```
<select name="{{ $name }}">
    @foreach($options as $value => $label)
        <option {!! $isSelected($value) ? 'selected="selected"' : '' !!} name="{{ $value }}">{{ $label }}</option>
    @endforeach
</select> 
```

在渲染 BladeX 组件时，输出如下:

```
<div>
  <select name="countries">
    <option name="be">Belgium</option>
    <option selected="selected" name="fr">France</option>
    <option name="nl">The Netherlands</option>
  </select>
</div> 
```

如果你对此有任何疑问，请在下面的评论中告诉我。