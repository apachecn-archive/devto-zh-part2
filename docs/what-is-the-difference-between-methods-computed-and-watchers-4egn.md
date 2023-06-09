# 方法、计算和观察器之间有什么区别？

> 原文：<https://dev.to/blankbash/what-is-the-difference-between-methods-computed-and-watchers-4egn>

我正在学习 Vue 的入门，我开始感到困惑，尝试了 CSS 技巧和堆栈溢出，但在实践中仍然不理解。有人需要把我当五岁小孩来解释。

#### 目前为止我得到了什么:

**Computed** -它们基于依赖关系被缓存，并且只在依赖关系改变时重新评估。

**方法**—

> 每当发生重新渲染时，方法调用将总是运行该函数。

渲染或重新渲染的确切定义是什么？每一个数据:价值变化？

Computed 和 methods 具有相同的结构，但是它们在代码中的位置不同...🤔🤔🤔

```
//vm instance
computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
//...

// in component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

**观看** -我对这个感到困惑，Vue 的入门指南说这是一个回调，警告最好使用 computed，但不解释它的用途...

我将感谢对这个问题的任何澄清👍