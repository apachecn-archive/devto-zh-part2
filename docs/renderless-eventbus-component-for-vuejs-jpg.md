# Vue.js 的无渲染 EventBus 组件

> 原文：<https://dev.to/basitali/renderless-eventbus-component-for-vuejs-jpg>

# 假设:

本文假设您知道使用 Vue 作为事件总线在组件之间进行通信的小技巧。如果没有，请阅读由 [Joshua Bemenderfer](https://twitter.com/@tribex_) 撰写的文章[用 Vue.js](https://alligator.io/vuejs/global-event-bus/) 创建全球事件总线。

# 为什么？

我开始在我的组件中使用 EventBus，实现看起来像这样，

```
 mounted() {
    EventBus.$on('some-event', this.handleEvent)
  },
  destroyed() {
    EventBus.$off('some-event', this.handleEvent)
  } 
```

随着事件数量的增加，这变得有点混乱，更糟糕的是？你可以忘记关闭事件。

# 解

我想到的两个解决方案是，

1.  混合到自动销毁事件
2.  处理`mounted`和`destroyed`逻辑的虚拟组件。

就我个人而言，我不喜欢 mixins，这就是为什么我甚至没有考虑第一点。

编写虚拟组件似乎更有趣。此外，html 模板中的事件侦听器看起来比 JS 模板好得多(并且它符合[https://vue JS . org/v2/guide/events . HTML # Why-Listeners-in-HTML](https://vuejs.org/v2/guide/events.html#Why-Listeners-in-HTML)背后的思想)。

这是 EventBus 组件的样子，

```
import _ from 'lodash'
import { EventBus } from '../common/eventBus.js'

export default {
  data() {
    return {
      events: Object.keys(this.$listeners),
      methods: _.mapValues(this.$listeners, (value, key) => this.handleEvent.bind(this, key))
    }
  },
  mounted() {
    this.events.forEach(event => {
      this.methods[event].SOMETHING = event
      EventBus.$on(event, this.methods[event])
    })
  },
  destroyed() {
    this.events.forEach(event => {
      EventBus.$off(event, this.methods[event])
    })
  },
  methods: {
    handleEvent(name, ...args) {
      this.$emit(name, ...args)
    }
  }
} 
```

演示在 https://codesandbox.io/s/k5v2owk8v7 举行。

# 脚注

这是我第一次在任何平台上发表文章，期待你的评论。