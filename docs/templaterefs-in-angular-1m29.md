# 角度模板参考

> 原文：<https://dev.to/prestonjlamb/templaterefs-in-angular-1m29>

如果你和我一样，你可能听说过 Angular 中的`TemplateRef` s，但不知道那是什么意思。这可能会让你不敢去了解它们是什么或者它们是如何工作的。但是学起来真的没那么可怕。只需要一点点工作和一些练习，好处是巨大的。

我最初是从艾萨克·曼的《T4》这篇文章中得知年代的。它引起了我的注意，因为我在 React 中听说了很多关于渲染道具的事情，但并不知道这是如何应用于 Angular 的。我最后读了艾萨克在`ng-template` s 和`TemplateRef` s 上的几篇文章。它们都是很棒的文章，你可以在这里[找到它们。](https://blog.angularindepth.com/@isaacplmann)

好吧，我们开始吧。在向我管理的`angular-tag-select`包添加一个`TemplateRef`组件选项时，我学到了以下内容。到目前为止，使用组件意味着您必须使用我确定的样式。有一个设置，你可以传入一个对象来改变一些样式。例如，这些选项包括图标类，并且非常有限。覆盖任何默认样式都很困难。所以我想提供一种方法，让人们可以从标签选择组件中获益，而不被默认样式所束缚。

创建使用`TemplateRef` s 的组件时有两个基本部分。第一部分是模板。模板应该包含一个元素来输出用户将看到的模板。这可能是一个`div`或其他 HTML 元素，也可能是一个`<ng-container>`。如果你使用一个 HTML 元素，这个元素将会出现在页面上。如果使用`<ng-container>`，那么页面上就不会有任何多余的元素。这里有一个使用`<ng-container>` :
的模板示例

```
<ng-container *ngTemplateOutlet="layoutTemplate; context: ctx"></ng-container> 
```

`*ngTemplateOutlet`由两部分组成:T1 和 T2。`layoutTemplate`是一个用 Angular 的`@ContentChild()`装饰器装饰的类变量。这允许您将一个`<ng-template>`从父组件传递到这个组件。`context`允许你在父组件中访问类变量。它是一个包含变量和函数的对象，你想访问它们。

```
public ctx: any = {
    tagsSelectedAtStart: this.tagsSelectedAtStart,
    possibleTags: this.possibleTags,
    selectedTags: this.selectedTags,
    fns: {
        toggleTag: this.toggleTag,
    }
}; 
```

实现使用`TemplateRef`的组件的另一个重要部分是在父组件中。假设我们的`TemplateRef`组件是`<tag-select>`组件，我们在应用程序的`<submit-form>`组件中实现这个组件。这是`<submit-form>`组件模板应该看起来的最基本的样子:

```
<tag-select>
    <ng-template>
        <!-- HTML to display parts of the tag-select component -->
    </ng-template>
</tag-select> 
```

将`<tag-select>`组件放在页面上，然后里面的`<ng-template>`标签说明了为什么和在哪里可以传入`<tag-select>`组件的显示元素。在这种情况下，使用这个组件，您需要显示两个基本部分:选择的标签和可能选择的标签。不管你想显示什么，它的 HTML 需要放在`<ng-template>`标签之间。

现在，你还记得在`<ng-container>`标签上我们声明了`context`变量吗？我们通过以下方式访问父节点中的变量:

```
<tag-select>
    <ng-template let-selected="selected" let-possible="possible">
        <!-- HTML to display parts of the tag-select component -->
    </ng-template>
</tag-select> 
```

这样做之后，我们就可以访问内部类变量了。在这种情况下，它使我们能够访问已经选择的标签列表和可能的标签列表，因此我们可以根据需要显示这两个列表。您也很可能在上下文中公开函数。

关于`context`，有几件事需要记住，如果你使用一个类变量来声明它，就像上面看到的，以及你所公开的函数。第一个是每次更新内部类变量时，上下文也需要显式更新。如果您以下面的方式声明了`context`,您就不需要在任何事情发生变化的时候显式地更新上下文；Angular 会帮你做的:

```
<ng-container *ngTemplateOutlet="layoutTemplate; context: { selected: selected, possible: possible }">
</ng-container> 
```

至于函数，如果您只是使用普通的语法在组件中编写函数，那么您会遇到与`undefined`变量有关的问题。要解决这个问题，请使用箭头函数。下面的图片将演示。

```
toggleTag = (tag: Tag) => {
    // Do Stuff
    this.updateContext('selected', this.selected)
}

updateContext(key: string, value: any) {
    this.ctx[key] = value;
} 
```

我知道这看起来很复杂，但是一旦我开始尝试，就不会太糟糕。我仍然有很多练习和很长的路要走，但是我很兴奋这将为我作为一个 Angular 开发者打开的可能性。使用或提供为最终用户执行复杂逻辑的包，但允许他们决定外观和感觉的能力是非常强大的。这就是这种模式在 React 中如此流行的原因。