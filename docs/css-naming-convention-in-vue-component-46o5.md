# Vue 组件中的 CSS 命名约定

> 原文：<https://dev.to/nakajmg/css-naming-convention-in-vue-component-46o5>

很长一段时间我都在考虑 Vue 组件中的 CSS 命名约定。

这个命名约定是我的答案之一。它不太复杂，易于操作。

我考虑了为 Vue 组件定制的 CSS 命名约定
以前，SFC(单个文件组件)使用 BEM 命名法，但我觉得编写组件是多余的。所以基于 ECSS 的想法，我静下心来按照 SFC 定制。

它叫做 *SFCSS* 意思是 SFC 的 CSS。

## 即使使用`scoped`命名约定也是必要的

首先，作为先决条件，即使`<style scoped>`没有泄露组件的风格，命名约定也是必要的。根据`scoped`写得恰如其分的类名理由，后面肯定会有付出。

[官方风格指南#组件风格范围](https://vuejs.org/v2/style-guide/index.html#Component-style-scoping-essential)为了不应用，需要添加一个组件特定的前缀等等。

然而，SFC 的开发本质上不同于普通网站，所以我认为前缀不一定是正确的答案。

## ECSS 中命名的例子

例如，ECSS 命名是使用以下模式完成的。

```
.namespace-Component_ChildNode-variant {} 
```

Enter fullscreen mode Exit fullscreen mode

`namespace`给出组件所属位置的前缀等`-variant`是边界元法中的修饰语。`Component`块和`ChildNode`元素在哪里？

我们对 SFC 的命名约定进行了如下修改。

## SFCSS

在 SFCSS 中，命名是通过以下模式完成的。

```
.ComponentName {}
.ComponentName_ChildNode {}
._variant {} 
```

Enter fullscreen mode Exit fullscreen mode

只使用这三种模式。

## [组件 Name](#componentname)

`ComponentName`使用与 vue 组件的`name`相同的名称。

```
<template>
  <div class="MyComponent"></div>
</template>

<script>
  export default {
    name: 'MyComponent',
  }
</script>

<style lang="scss" scoped>
  .MyComponent {}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

通过将根类名与 vue 组件的`name`进行匹配，可以更容易地从 DOM 节点中识别组件。

如果能使用 vue-devtools，这个思路就没必要了，但是在没有 vue-devtools 的生产环境和浏览器中的验证会很有用。

## 子节点

`ChildNode`是除了要授予类的组件的根元素之外的元素。

例如，用下面的粒度设置`ChildNode`。

```
<template>
  <div class="MyComponent">
    <h1 class="MyComponent_Heading">
      heading
    </h1>
    <ul class="MyComponent_List">
      <li class="MyComponent_ListItem"
        v-for="item in list"
      >
        {{item.label}}
      </li>
    </ul>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

#### 坏模式

原则上，下面的`ChildNode`个连续类名应该是**个不可接受的**🙅

```
.MyComponent_List_Item {} / * bad * / 
```

Enter fullscreen mode Exit fullscreen mode

## 变体

`variant`是一个类，用于根据条件改变样式的元素。ECSS 包含一个完整的选择器，比如作为类名的`ComponentName_ChildNode`，但是因为您可以使用`scoped`，所以将其定义为一个单独的类。

`ComponentName`和`ChildNode`是帕斯卡格，而`variant`是骆驼格，在类名的开头以`_`开始。

```
._variantClassName {} 
```

Enter fullscreen mode Exit fullscreen mode

由于`variant`有很多用`: class`把它拆下来的用法，所以用一个不需要引号的字符串来构造就很方便了。

### 命名空间是不必要的

我们认为`namespace`在 vue 组件中是不必要的。ECSS 的`namespace`表示组件所属的区域 etc，但不需要限制在 vue 组件中使用的位置。

由于 vue 组件将具有唯一的名称，因此您不需要避免因前缀而导致的名称冲突。

## 将根类限制为一个

在`.vue`的`<style>`内，将根类(`ComponentName`)限制为一个。只有样式是用一对一的组件描述的。

如果你想写一个以上的根类，这可能是一个标志，分成不同的组成部分。

## [T3 用`lang="scss"`省略用`&`的描述](#use-raw-langscss-endraw-to-omit-description-with-raw-amp-endraw-)

例如，在上面以`ChildNode`为例的`template`中，将`style`写成如下。

```
<template>
  <div class="MyComponent">
    <h1 class="MyComponent_Heading">heading</h1>
    <ul class="MyComponent_List">
      <li class="MyComponent_ListItem"
        v-for="item in list"
        :class="{_selectedItem: isSelectedItem(item)}"
      >
        {{item.label}}
      </li>
    </ul>
  </div>
</template>

<style lang="scss" scoped>
  .MyComponent {
    &_Heading {}
    &_List {}
    &_ListItem {}
    ._selectedItem {}
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

尽管`template`中对类名的描述并没有减少，但我们认为总的来说`style`看起来比全部写完整的要好。这个`& _`省略对于划分组件也很有用。

## 总之

当我在实际案例中尝试这个 SFCSS 时，我没有受到组件类名的困扰。我认为发展正以一种平衡的状态进行着，只是没有恰当地命名。

*本文由谷歌翻译*翻译。[日文原帖](https://qiita.com/nakajmg/items/683395c20a3afbdb2d99)