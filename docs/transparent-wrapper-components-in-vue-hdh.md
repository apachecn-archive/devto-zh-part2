# Vue 中的透明包装组件

> 原文：<https://dev.to/kball/transparent-wrapper-components-in-vue-hdh>

在当前的 Vue 项目中，我有几个不同的组件，它们几乎都是本机元素，但在它们周围有一个小小的包装，增加了功能。示例包括使用特定日期选择器的日期输入和自动调整大小的文本区域。

对于这些组件，我希望它们的行为尽可能接近于它们是本机组件的行为——允许我透明地传递属性、添加事件监听器等等。

在 Vue 中做到这一点是完全可能的，但是需要一点时间。下面是我得出的步骤，以自动调整文本区域为例。

### 步骤 1:使用:value 和@input 实现 v-model

根据组件的 [Vue 指南，组件上的 v-model 本质上是通过传入`value` prop，并应用`input`事件处理程序来工作的。](https://vuejs.org/v2/guide/components.html#Using-v-model-on-Components)

让我们的组件看起来像原生元素的第一步是用一个类似于
的模板来设置它们

```
<textarea
  :value="value"
  @input="input"
  >
</textarea> 
```

Enter fullscreen mode Exit fullscreen mode

还有看起来像
的 javascript

```
export default {
  props: ['value'],
  methods: {
    input(event) {
      this.$emit('input', event.target.value);
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果有人将 v-model 与我们的组件一起使用，它的行为就像他们将 v-model 应用于底层 textarea 一样。

### 第二步:通过其他事件监听器

我们希望确保输入正常工作，因为这是使 v-model 正常工作的关键，但我们也希望我们的组件处理我们可能应用的其他事件侦听器。例如，我们可能希望在某些位置或其他地方应用一个焦点监听器。

我们将利用组件上的内置`$listeners`对象，而不是试图考虑所有可能的侦听器，这让我们可以将所有侦听器应用到组件。

然后，我们将挑选出输入的一个(我们已经在处理了)，并使用类似于
的 javascript 批量应用其余的

```
computed: {
  listeners() {
    const { input, ...listeners } = this.$listeners;
    return listeners;
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

然后在模板中批量应用它们:

```
<textarea
  :value="value"
  @input="input"
  v-on="listeners"
  >
</textarea> 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:传递属性

除了事件侦听器之外，我们还希望传递我们可能希望进入的任何属性。默认情况下，Vue 会为我们做这件事——组件上设置的所有属性都被传递给组件的根元素。

*然而*，在这种情况下我们想通过设置默认值来特殊处理其中的一些属性。为此，我们将通过设置`inheritAttrs: false`来禁用这种自动属性传递，然后使用类似的方法让侦听器在我们的默认值之后重新应用它们。

例如，在我们的自动调整文本区域中，我们希望默认行数为 3，因此我们可以做如下事情:

```
computed() {
  rows() {
    return this.$attrs.rows || 3;
  },
  attrs() {
    const { rows, ...attrs } = this.$attrs;
    return attrs;
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

然后在模板中:

```
<textarea
  :value="value"
  @input="input"
  v-on="listeners"
  :rows="rows"
  v-bind="attrs"
  >
</textarea> 
```

Enter fullscreen mode Exit fullscreen mode

### 应用自定义功能

最后，为了简单地添加我们的自动调整功能，使用 autosize npm 包，我们导入 autosize 并添加一个快速挂接:

```
mounted() {
  autosize(this.$el);
}, 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们完整的单个文件组件模板如下所示:

```
<textarea
  :value="value"
  @input="input"
  v-on="listeners"
  :rows="rows"
  v-bind="attrs"
  >
</textarea> 
```

Enter fullscreen mode Exit fullscreen mode

我们的 javascript 看起来像:

```
export default {
  props: ['value'],
  inheritAttrs: false,
  computed: {
    listeners() {
      const { input, ...listeners } = this.$listeners;
      return listeners;
    },
    rows() {
      return this.$attrs.rows || 3;
    },
    attrs() {
      const { rows, ...attrs } = this.$attrs;
      return attrs;
    },
  },
  methods: {
    input(event) {
      this.$emit('input', event.target.value);
    },
  },
  mounted() {
    autosize(this.$el);
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】