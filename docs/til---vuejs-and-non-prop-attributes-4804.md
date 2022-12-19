# TIL - Vue.js 和非属性

> 原文：<https://dev.to/raymondcamden/til---vuejs-and-non-prop-attributes-4804>

这个周末，我为一个 Vue.js 组件( [vue-static-map](https://github.com/eperedo/vue-static-map) )做了一次公关，结果完全是浪费时间。差不多了。我的意思是——我增加了对某些东西的支持，这些东西实际上最终成为了 Vue 的一个固有特性。当然，我浪费了一些时间，但我也学到了一些东西，正如我的读者所知，每次我搞砸了，我都会把它写在博客上。

好吧，那我做了什么？我再次提交 PR 的项目是[谷歌静态地图 API](https://developers.google.com/maps/documentation/static-maps/) 的包装器。这是我最喜欢的 API 之一，因为它由一个带有特定参数的图片 URL 组成。如果你不需要交互式谷歌地图，只需要一张地图，那么静态地图 API 是完美的。

Vue 组件(由 [Eduardo P. Rivero](https://github.com/eperedo) 制造)包装了 API 并使其更加简单。然而，我注意到它缺少了一些东西——为图像标签指定一个`alt`或`title`标签的能力。所以我修改了他的组件，允许 alt 和 title:

```
props: {
        altText: {
            type: String,
            required: false,
            default: 'Static Google Map',
        },
        titleText: {
            type: String,
            required: false,
            default: 'Static Google Map',
        },
        // and so forth.... 
```

Enter fullscreen mode Exit fullscreen mode

就这样，对吗？除了我没有意识到(但幸运的是 Eduadro 意识到了)Vue *已经*将传入没有在组件中明确定义的属性！而且这个也有记载:[非道具属性](https://vuejs.org/v2/guide/components-props.html#Non-Prop-Attributes):

> 非属性是传递给组件的属性，但没有定义相应的属性。
> 
> 虽然显式定义的 props 更适合将信息传递给子组件，但是组件库的作者并不总是能够预见到组件可能被使用的上下文。这就是为什么组件可以接受任意属性，这些属性被添加到组件的根元素中。

这仅仅意味着当他的组件使用`img`作为根元素时，它已经支持接受一个`alt`和`title`标签。(他的代码确实有一个他移除的预设`alt`标签。)Vue 在这一点上其实也相当聪明。因此，如果你的组件有一个现有的`class`或`style`值，并且你传入自定义值，它将合并它们而不是替换它们。你可以在这里阅读这个。最后，如果你不喜欢这样，你可以[禁用](https://vuejs.org/v2/guide/components-props.html#Disabling-Attribute-Inheritance)你的组件中的行为。

所有这些都被记录了下来，但我错过了一些东西，这是一个惊喜。想看看例子吗？在我上一次的 Vue.js 演示中，我构建了一个简单的 Nicolas Cage 组件。(它只是包裹了[PlaceCage.com](http://www.placecage.com/))。最初，我只内置了对高度和宽度的支持——这两者都会进入 URL 以选择正确的图片。为了支持 alt/title，我什么都不用做。在下面的代码笔中，你可以看到我使用标题的地方，如果你把鼠标放在结果上，它就能正常工作。

[https://codepen.io/cfjedimaster/embed/oqMZQV/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/oqMZQV/?height=600&default-tab=result&embed-version=2)