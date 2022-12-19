# 理解 Vuejs 生命周期挂钩

> 原文：<https://dev.to/samolabams/understanding-vuejs-lifecycle-hooks-5ejk>

Vuejs 中的组件有一个生命周期，当它创建组件、将组件挂载到 DOM、更新组件和销毁组件时，它自己管理这个生命周期。换句话说，每个组件都有所谓的生命周期事件——它的诞生，像变化和死亡这样的生活事件。_ 我们可以通过实现一个或多个由 Vue 自身调用的生命周期挂钩来利用生命周期中的关键时刻，从而让我们有机会在组件生命周期的特定阶段添加自己的代码。

> Vue 有八个生命周期挂钩，记住它们的关键是要知道其中四个是被触发的事件，表明实际事件将会发生。它们在实际挂钩之前以“before”开始，并在实际挂钩之前被激发。

让我们看看这些钩子的作用。

**beforeCreate** —这是 Vue 实例初始化后调用的第一个钩子。在此阶段，数据观察(反应)、事件、计算属性和观察器尚未建立。因此，我们不能与组件的任何部分进行交互。

```
<script>
export default {
  data() {
    return {
      counter: 0
    }
  },

  beforeCreate() {
    console.log('I am the first hook to get called!');
    console.log(`You can see that my data property is ${typeof this.counter}`); // undefined
  }
}
</script> 
```

**Created** —这个钩子在实例创建后被调用。在这个阶段，实例已经完成处理，数据观察(反应性)、计算属性、方法、观察器和事件回调都已经设置好。在这个阶段，您不能与 DOM 交互，因为您的组件还没有安装。$el 属性还不可用。

```
<script>
export default {
  data() {
    return {
      counter: 0
    }
  },

  created() {
    console.log('I am the created hook.');
    console.log(`You can now see that my data property is ${typeof this.counter}`);
  }
}
</script> 
```

**beforeMount** —在这个阶段，从模板或渲染选项，或者从 Vue 初始化到的元素的 outerHTML 编译模板。模板还没有呈现，$el 方法也不存在。请注意，在服务器端渲染过程中不会调用这个钩子。

```
<script>
export default {
  beforeMount() {
    console.log(`I am ready to be rendered.!`)
  }
}
</script> 
```

**Mounted** —在实例被挂载后调用，其中 el 属性被新创建的 vm 替换。$el。如果根实例被挂载到文档内元素。调用 mounted 时，$el 也将在文档中。组件在挂载的钩子被调用后变得完全可用，我们可以与它完全交互。

需要注意的一点是，挂载的钩子不能保证元素已经被添加到 DOM 中。为了在这个阶段执行依赖于 DOM 的代码，将代码放在 Vue.nextTick()函数的回调方法中( *Vue.nextTick()将其回调推迟到下一个 DOM 更新周期*之后执行)。请参见下面的示例:

```
<template>
  <p>I'm text inside the component.</p>
</template>

<script>
export default {
  mounted() {
    // Element might not have been added to the DOM yet
    this.$nextTick(() => {
        // Element has been definitely added to the DOM
       console.log(this.$el.textContent); // I'm text inside the component.
    }
  }
}
</script> 
```

**beforeUpdate** —每当我们的数据发生变化并且 DOM 需要更新时，就在 DOM 打补丁之前调用它。这是在更新之前访问现有 DOM 的好地方，例如手动删除添加的事件侦听器。在服务器端呈现期间不调用这个钩子，因为只有初始呈现在服务器端执行。

```
<script>
export default {
  data() {
    return {
      counter: 0
    }
  },

  beforeUpdate() {
    console.log(this.counter) // Logs the counter value every second, before the DOM updates.
  },

  created() {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
</script> 
```

**更新的** —钩子在改变后被触发。当这个钩子被调用时，组件的 DOM 已经被更新了，所以您可以在这里执行依赖于 DOM 的操作。然而，在大多数情况下，你应该避免改变钩子内部的状态。为了对状态变化做出反应，通常最好使用[计算属性](https://devdocs.io/vue~2/api/index#computed)或[观察器](https://devdocs.io/vue~2/api/index#watch)来代替。

请注意，updated 不能保证所有子组件都被重新渲染。如果你想等到整个视图被重新渲染，你可以使用[虚拟机。$nextTick](https://devdocs.io/vue~2/api/index#vm-nextTick) 在更新的:

```
<template>
  <p ref="dom-element">{{counter}}</p>
</template>
<script>
export default {
  data() {
    return {
      counter: 0
    }
  },

  updated() {
    // Fired every second, should always be true
    console.log(+this.$refs['dom-element'].textContent === this.counter)
  },

  created() {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
</script> 
```

**beforeDestroy** —在销毁 Vue 实例之前调用。在这个阶段，实例仍然是完全正常的。您可以在这里执行必要的清理。请注意，在服务器端渲染过程中不会调用这个钩子。

```
<script>
export default {
  data() {
    return {
      counter: 0
    }
  },

  beforeDestroy() {
    // Clean up the counter.
    // (In this case, effectively nothing)
    this.counter = null
    delete this.counter
  }
}
</script> 
```

**销毁** —在 Vue 实例被销毁后调用。当这个钩子被调用时，Vue 实例的所有指令都被解除绑定，所有事件监听器都被移除，所有子 Vue 实例也都被销毁。请注意，在服务器端渲染过程中不会调用这个钩子。

```
<script>
import DestroyerNotifierService from './notifier'

export default {
  destroyed() {
    console.log(this) // There's practically nothing here!
    DestroyerNotifierService.informThem('Component destroyed')
  }
}
</script> 
```

> 所有生命周期挂钩都自动将它们的 this 上下文绑定到组件实例，以便您可以访问数据、计算属性和方法。这意味着**你不应该使用一个箭头函数来定义一个生命周期方法**(例如，created: () = > this.fetchTodos())。原因是箭头函数绑定了父上下文，所以这不会是您所期望的 Vue 实例，this.fetchTodos 将是未定义的。

换句话说，箭头函数没有自己的值*这个*。arrow 函数中的这个值是从封闭(词法)范围继承的。所以，你不能在钩子内部使用它们。

### 结论

在本文中，我们学习了 Vuejs 组件生命周期及其应用。

#### 感谢阅读。