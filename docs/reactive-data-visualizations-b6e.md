# 反应式数据可视化

> 原文：<https://dev.to/ignoreintuition/reactive-data-visualizations-b6e>

数据要讲故事。如果你有一个跟踪减肥的应用程序，一个积累交易的网站，或者一个收集反馈的调查，它应该向你的用户传达一些信息。不传达信息的数据在功能上是无用的。显而易见，如果你正在构建一个收集数据的东西，你应该向你的用户传达一些信息。

反应性数据是立即在系统中传播的数据。通过紧密耦合的元素绑定，您可以用页面上输入的新数据立即更新整个应用程序。这提供了一个独特的机会去做一些对你的用户非常有价值的事情。反应式数据可视化。一个系统，通过它您可以即时更新您的数据结构以及图形、图表和其他视觉线索，以实时讲述您的故事。

Vue.js 就是这样一个框架，它允许我们以这种方式绑定数据。通常，这是一种在页面上呈现上下文内容的方式。我们将看一个稍微不同的用例。在整篇文章中，我们将构建一个系统，在这个系统中，数据被输入并立即绑定到一个 SVG，以便为用户可视化。目的是帮助他们以更深入、更有意义的方式理解数据。使用这些工具，您可以构建健壮的仪表板系统，将外部数据源与其个性化数据相结合。

我们将从使用 [Vue CLI](https://cli.vuejs.org/) 启动一个 Vue 项目开始。如果您不熟悉 Vue CLI，我推荐您阅读他们网站上的一些很棒的文档，或者关于这个主题的许多其他文章。出于本文的目的，我将假设您有一个 Vue 项目，其中有一个名为 DataViz 的单一路由，如下所示

```
import DataViz from '@/components/DataViz'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'DataViz',
      component: DataViz
    }
  ]
}) 
```

让我们添加一个数据集。我们将使用的数据集是一个教师评分系统，允许他们输入学生成绩，并在条形图中查看每个学生的分数。一个有点做作但简单的例子。我们从四个学生开始:

```
students:  [{  "name":  "Jack",  "grade":  100  },  {  "name":  "Mary",  "grade":  50  },  {  "name":  "Jim",  "grade":  45  },  {  "name":  "Jill",  "grade":  95  } 
```

我们将把这个数据集输入到我们的数据方法中，以便它在我们的组件中可用。接下来，我们的模板中需要引用这些数据。这需要两个步骤:将数据建模到输入字段，并将数据填充到图形中。建模部分是标准的 Vue.js:

```
<template>
  <div id="content">
    <H1> Reactive Data with Vue </H1>
    <div class="component">
      <form>
        <div v-for="a in students">
          <input v-model="a.grade"/>
        </div>
      </form>
    </div>
  </div>
</template> 
```

这将为我们提供一个简单的表单来编辑任何分数:

[![form fields](img/801960e431b71c49f36c0fdd5e7599ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kiEX6q_J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9n19t6nfzcm4fky1ffyy.png)

表单就绪后，接下来要做的事情是将这些元素中的数据与数据可视化的组件相关联。为了简单起见，我选择了条形图。所需要的只是使宽度成为坡度的函数，而 y 位置成为数组索引的函数:

```
 <div class="component">
      
        <rect v-for="(a, index) in students" class="bar"
          height  = "20"
          x       = "0"
          :width  = "a.grade * 2"
          :y      = "index * 22" />
      
    </div> 
```

Vue.js 将负责绑定表单字段和图表之间的数据。我们现在有了一个全功能反应图。添加一点样式，你应该有这样的东西:

[![Reactive Chart](img/4896fb5e4fc3954eac3c38c742e109a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HMIuy2Fw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d0lfkzia6vdxbi1d6asa.png)

从这里开始，我让你来使用反应数据创建一些辉煌的视觉效果。你可以在我的 [GitHub](https://github.com/ignoreintuition/reactiveData) 上找到我的项目的完整源代码，如果你正在寻找灵感，你可以看到我在我的 [D3Vue 项目](https://github.com/ignoreintuition/d3vue)中创建的一些反应可视化。干杯。