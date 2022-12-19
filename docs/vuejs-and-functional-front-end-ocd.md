# Vue.js 和功能前端

> 原文：<https://dev.to/proticm/vuejs-and-functional-front-end-ocd>

你好，我的开发伙伴，欢迎阅读另一篇文章。根据标题，我们将在这里谈论一些非常有趣的东西。Vue.js 将重点放在带有模板的声明性组件上，这很好，但它也支持 JSX。

好，让我们从快速回答什么是函数式编程和 JSX 开始。

## 功能编程

与指导开发人员将程序分解成对象的面向对象的方法不同，函数式方法鼓励将程序分解成小函数，这些函数随后用于形成更高级的程序。

> 在计算机科学中，函数式编程是一种编程范式，一种构建计算机程序的结构和元素的风格，它将计算视为数学函数的求值，并避免改变状态和可变数据

上面的文字引自关于函数式编程的官方 wiki 页面，但是那到底是什么意思呢？

简而言之，这意味着我们正在以一种不依赖于或者能够改变任何外部状态的方式来创建函数，这使我们得出另一个结论，对于给定的输入，它们将总是返回相同的输出。

从长远来看，我们需要了解两种主要资产:

1.  纯函数
2.  不变

### 纯函数

这就是函数式编程如此特别的原因。纯函数就像任何其他函数一样，是我们应用程序的组成部分。它的特殊之处在于它的输出只依赖于给定的输入。这意味着我们可以任意多次调用我们的函数，但是如果我们传入相同的参数，输出将始终与上一次调用中的一样。

### 永恒性

不可变对象是指创建后就不能更改的对象。简单地说，我们不能改变我们目标值。对于这些对象，当我们想要改变它的任何属性时，我们总是需要创建一个新的实例。

原始不可变类型的例子是`String`类型。即使当我们使用它时，它看起来是可变的，但事实并非如此，因为每当我们给我们的`String`重新赋值时，就会创建一个类型为`String`的新对象，我们的新值被赋给它。

## JSX

快速的谷歌搜索会告诉我们 JSX 代表 JavaScript XML。这使我们能够在 JavaScript 文件中编写类似 XML 的代码。听起来很酷，但正如我在之前的[帖子中提到的(稍后见，现在继续阅读)](https://dev.to/proticm/react-or-vue-or-something-new-3bd)它让我们远离了标准的 web 开发。无论如何，我们应该承认 JSX 是相当强大的。

现在，在这些简短的解释之后，我们可以通过创建一个功能组件示例来深入了解 Vue.js 的工作方式。

让我们从定义功能组件可能有用的需求开始。假设我们希望根据用户角色显示不同的区域。例如，我们可以让用户担任管理员、普通用户和来宾角色。

常规模板应该是这样的:

```
<script type="text/x-template" id="area-template">
    <div v-if="role === 'Admin'">
        Admin user area
    </div>
    <div v-else-if="role === 'Normal'">
        Normal user area
    </div>
    <div v-else-if="role === 'Guest'">
        Guest user area
    </div> </script> 
```

更多的角色会导致更多的 if-else 语句。这正是我们想要避免的功能组件。下面的代码说明了这一点:

```
Vue.component('example-component', {
    props: {
        role: {
            type: String,
            required: true
        }
    },
    render: function (createElement) {
        var text = this.role + ' user area';
        return createElement('div', text);
    }
}); 
```

如您所见，我们的组件没有模板属性，但是我们引入了一个渲染函数。这个函数负责组件的渲染。它将总是返回相同的输出，根据 Vue.js 文档网站的说法，这个输出叫做`createNodeDescription`。它包含向 Vue 描述它应该在页面上呈现什么样的节点的信息。

当然，为了让我们的示例工作，我们需要创建一个新的 Vue 实例，以及页面上的一个元素:

```
new Vue({
    el: '#app',
    data: {
        admin: 'Admin',
        normal: 'Normal',
        guest: 'Guest'
    }
}); 
```

```
<main id="app">
    <example-component :role=admin></example-component>
    <example-component :role=normal></example-component>
    <example-component :role=guest></example-component>
</main> 
```

上面的代码将在我们的页面上显示三个 div 元素，并在我们的 render 函数中创建相应的文本。

我希望你喜欢阅读这篇简短但直观的文章，它将帮助你开始用函数的方式构建应用程序。

这就是现在的男孩女孩们。感谢您的阅读，祝您好运。