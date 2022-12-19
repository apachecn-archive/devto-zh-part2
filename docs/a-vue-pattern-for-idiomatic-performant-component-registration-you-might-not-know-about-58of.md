# 一个您可能不知道的用于惯用的、高性能的组件注册的 Vue 模式

> 原文：<https://dev.to/afontcu/a-vue-pattern-for-idiomatic-performant-component-registration-you-might-not-know-about-58of>

如果你玩过 Vue **单个文件组件**一点，你可能知道如何从另一个组件“调用”一个组件:

*   导入子组件
*   在父组件的组件对象上注册它。
*   将组件添加到模板/渲染函数中。

```
 <template>
      <some-random-thing />
    </template>

    <script>
    import SomeRandomThing from './components/SomeRandomThing'

    export default {
      components: {
        SomeRandomThing,
      },
    }
    </script> 
```

Enter fullscreen mode Exit fullscreen mode

这是一种常见的模式，最终可能会变得乏味。在这篇短文中，我们将学习一种(或两种)避免重复的模式。我们还将免费改进我们的应用程序性能。

让我们开始吧！

* * *

想象一个 Header 组件，它保存了应用程序标题的信息和布局。现在想象一下，这些信息可能是用户相关的，也可能是公司相关的，这取决于……我不知道，一个设定值。随便啦。

现在假设我们有一个 UserInfo 和 CompanyInfo 组件。我们希望根据之前已经配置的设置值显示一个或另一个。

* * *

## 版本 1:好 ol’道

这是我们上面概述的方法。

这大概是所有人都会想到的“*默认*”方式(包括我！):