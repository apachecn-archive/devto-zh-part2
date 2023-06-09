# Vue.js 组件通信

> 原文：<https://dev.to/flaviocopes/vuejs-components-communication-3cgo>

Vue 中的组件可以通过各种方式进行通信。

## 道具

第一种方式是使用[道具](https://flaviocopes.com/vue-props/)。

父节点通过向组件声明添加参数来“传递”数据:

```
<template>
  <div>
    <Car color="green" />
  </div>
</template>

<script>
import Car from './components/Car'

export default {
  name: 'App',
  components: {
    Car
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

道具是单向的:从父母到孩子。每当父对象更改属性时，新值都会发送给子对象并重新渲染。

反之则不然，你永远不应该在子组件中改变道具。

## 事件从孩子到家长的交流

事件允许你从孩子到父母进行交流:

```
<script>
export default {
  name: 'Car',
  methods: {
    handleClick: function() {
      this.$emit('clickedSomething')
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

当在模板
中包含组件时，父组件可以使用`v-on`指令来截取它

```
<template>
  <div>
    <Car v-on:clickedSomething="handleClickInParent" />
    <!-- or -->
    <Car @clickedSomething="handleClickInParent" />
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    handleClickInParent: function() {
      //...
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

当然可以传递参数:

```
<script>
export default {
  name: 'Car',
  methods: {
    handleClick: function() {
      this.$emit('clickedSomething', param1, param2)
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

并在父目录中检索它们:

```
<template>
  <div>
    <Car v-on:clickedSomething="handleClickInParent" />
    <!-- or -->
    <Car @clickedSomething="handleClickInParent" />
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    handleClickInParent: function(param1, param2) {
      //...
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 使用事件总线在任何组件之间进行通信

使用事件，你不局限于父子关系。

可以使用所谓的**事件总线**。

上面我们使用了`this.$emit`在组件实例上发出一个事件。

我们可以做的是在一个更容易访问的组件上发出事件。

`this.$root`，词根成分，常用于此。

您还可以创建一个专用于此作业的 Vue 组件，并将其导入到您需要的位置。

```
<script>
export default {
  name: 'Car',
  methods: {
    handleClick: function() {
      this.$root.$emit('clickedSomething')
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

任何其他组件都可以侦听该事件。您可以在`mounted`回调:
中这样做

```
<script>
export default {
  name: 'App',
  mounted() {
    this.$root.$on('clickedSomething', () => {
      //...
    })
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 替代品

这就是 Vue 提供的开箱即用。

当你不再需要它时，你可以看看 Vuex 或其他第三方库。

> 我正在学习 Vue.js 课程。如果你对学习 Vue 感兴趣，[上名单](https://vuecourse.com/)下周就能获得一本 100 多页的关于 Vue 基础知识的免费电子书！