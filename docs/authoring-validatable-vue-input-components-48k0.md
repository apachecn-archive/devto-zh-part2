# 创作可验证的 Vue 输入组件

> 原文：<https://dev.to/logaretm/authoring-validatable-vue-input-components-48k0>

构建 Vue.js 组件非常有趣和简单，你几乎可以构建任何东西，并且在大多数时候都可以工作。但是当涉及到作为**输入**的组件时，几乎没有什么可以涵盖的。

我们将从使用 Vue.js 指南介绍基础知识开始，然后逐步集成组件，以便与 [vee-validate](https://github.com/baianat/vee-validate) 无缝协作。

### 实现 v-model 接口

正如[在 Vue 官方指南中提到的](https://vuejs.org/v2/guide/components.html#Form-Input-Components-using-Custom-Events),你的组件应该能够使用 v-model 指令，这可以归结为每当值改变时发出一个输入事件，并且它应该接受一个值输入，这当然可以被定制为包含其他事件和道具。

首先，我们的自定义文本组件应该是这样的:

```
<template>
  <input type="text" :value="value" @input="onInput" />
</template>

<script>
export default {
  props: {
    value: String
  },
  methods: {
    onInput (e) {
      this.$emit('input', $event.target.value);
    }
  }
};
</script> 
```

它还没有做很多事情，它只是接受一个值属性，并在用户通过本机输入事件操作输入时发出一个事件，所以在某种程度上，它将交互从本机 HTML 输入转换为 Vue 组件。

这样，使用 v-model 是这个组件改变状态的唯一方式，简单且易于管理。最重要的是可重复使用。

### 根据需要添加事件

原生 HTML 输入有大量的事件可以监听，但是大多数时候你只对其中的几个感兴趣，在我们的例子中，我们只对输入事件感兴趣，我们还应该添加一些有用的事件，比如聚焦、模糊和改变。

```
<template>
  <input
    type="text"
    :value="value"
    @input="updateValue"
    @change="updateValue"
    @blur="$emit('blur')"
    @focus="$emit('focus')"/>
</template>

<script>
export default {
  props: {
    value: String
  },
  methods: {
    updateValue (e) {
      this.$emit('input', $event.target.value);
    }
  }
};
</script> 
```

在您自己的项目中使用您自己的组件时，这可能是不必要的，但是如果您计划向大众发布您的组件，那么您可能需要包含一些用户可能有兴趣列出的事件，例如，模糊可以用于触发 UI 更改或触发对该输入的验证。

### 添加道具

像事件一样，一些用户可能会对在组件上设置一些属性感兴趣，就像在本地 HTML 输入上一样，例如，disabled 属性非常有价值，可能还有标签和名称属性。

此外，让输入类型总是设置为文本在移动设备上不太容易实现，因为键盘可以感知上下文来显示与输入类型相关的字符，如电子邮件的`@`和 URL 的`.com`。

让我们添加我们的道具，并将其绑定到内部输入属性:

```
<template>
  <div class="form__input">
    <label v-if="label" class="form__label">{{ label }}</label>
    <input
      class="input"
      :type="type" 
      :value="value" 
      :disabled="disabled" 
      @input="updateValue" 
      @change="updateValue" 
      @blur="$emit('blur')" 
      @focus="$emit('focus')"
    />
  </div>
</template>

<script>
export default {
  props: {
    name: {
      type: String
    },
    label: {
      type: String
    },
    disabled: {
      type: Boolean,
      default: false
    },
    value: String,
    type: {
      type: String,
      default: 'text',
      validate: (val) => {
        // we only cover these types in our input components.
        return ['text', 'url', 'email', 'password', 'search'].indexOf(val) !== -1;
      }
    }
  },
  methods: {
    updateValue (e) {
      this.$emit('input', $event.target.value);
    }
  }
};
</script> 
```

我们可以做很多事情，例如:自动完成**、**占位符和更多的**。**

### 启用验证

很可能你想验证我们的这个小组件，我们有两种方法:

*   作为对输入事件的响应，从父节点验证它(反应式)。
*   让它在值改变时验证自己(被动)。

现在，虽然第二个选项听起来更加包容和有用，但您应该认识到，它强制对组件进行验证，它是选择退出而不是选择加入。

此外，在单一责任模式中:**如果组件的值是有效的，是否应该关注组件？**或者是所述值的消费者的关注点，消费者是父组件还是表单组件。

第一种方法简化了我们的组件实现，使它更轻便，组件应该只报告它的值，而不是它的有效性。

Vee-validate 支持这两种方法，但是根据您的实现，第二种方法可能更难维护，因为它需要将错误传达给父组件，以防止表单提交，例如，直到一切都有效。

现在，让我们采用第一种方法，我们不需要向组件添加任何东西，它就可以工作了:

```
 <template>
  <div>
    <text-input 
      name="email" 
      label="Email Address" 
      v-validate="'required|email'"
      v-model="email"
      type="email"
    ></text-input>
  </div>
</template>

<script>
import TextInput from '@components/inputs/Text';
export default {
  components: {
    TextInput
  },
  data: () => ({
    email: null
  })
};
</script> 
```

问题来了，我们的父组件可以使用 validator API 判断组件是否有效，但不能判断我们的用户。回到我们的问题，如果组件应该关心它的值是否有效，也许应该，因为我们需要向用户显示一些危险信号。

但是它不应该说“我的值有效”，而是应该问:“我的值有效吗？”这可以很容易地用道具来完成。

让我们向组件添加错误属性，并将其绑定到它视图:

```
<template>
  <div class="form__input">
    <label v-if="label" class="form__label">{{ label }}</label>
    <input
      class="input"
      :type="type" 
      :value="value" 
      :disabled="disabled" 
      @input="updateValue" 
      @change="updateValue" 
      @blur="$emit('blur')" 
      @focus="$emit('focus')"
    />
    <p v-if="error" class="form__error">{{ error }}</p>
  </div>
</template>

<script>
export default {
  props: {
    error: {
      type: String
    },
    // ...
  },
  methods: {
    // ...
  }
};
</script> 
```

```
<template>
  <div>
    <text-input 
      name="email" 
      label="Email Address" 
      v-validate="'required|email'"
      v-model="email"
      type="email"
      :error="errors.first('email')"
    ></text-input>
  </div>
</template>

<script>
import TextInput from '@components/inputs/Text';
export default {
  // ...
};
</script> 
```

好了，现在我们有了一个干净的组件，可以用一些漂亮的特性进行验证。剩下的唯一一件事就是解决一些与 vee-validate 相关的问题。

Vee-validate 验证组件的方式与验证 HTML 输入的方式相同，主要使用事件，但是当您在表单提交之前调用 validateAll 进行手动验证时会发生什么呢？vee-validate 不知道输入的当前值，因为它不跟踪它们，如果您想改为在模糊上验证呢？我们的组件不发出模糊事件的值，所以验证器不知道该做什么。

### VeeValidate 组件选项

有一个新增加的特性叫做[组件选项](https://github.com/baianat/vee-validate/pull/868)，是组件和 VeeValidate 之间的契约。它允许你的组件告诉 vee-validate 一些东西，比如如何获取当前值、组件名以及应该使用哪些事件来验证组件。

```
<script>
export default {
  $_veeValidate: {
    // must not be arrow functions.
    // the name getter
    name () {
      return this.label;
    },
    // the value getter
    value () {
      return this.value; // `this` is the component instance, `value` is the prop we added.
    }
  },
  props: {
    // ...
  },
  methods: {
    // ...
  }
};
</script> 
```

就这样，我们得到了一个很好的自定义输入组件，可以很好地与 vee-validate 配合使用。当然，还有改进的空间，已经有很多 Vue.js 组件框架做得很好，比如[vue tity](https://github.com/vuetifyjs/vuetify)。

### 其他输入类型呢？

文本输入虽然功能多样，但对于某些输入值类型来说，在许多方面都有所欠缺。比如日期、选择、文件以及任何你能想到的东西。

我在这个[沙箱](https://codesandbox.io/s/2wyrp5z000?view=preview)中包含了其他类型的输入，您可能会发现这些输入对您的下一个组件实现很有用。它们与我们在这里所做的有着相同的原则:**将值传递给模型，并告诉验证器如何获取它。**

我们在这里涵盖了很多！现在继续给你的输入组件一个漂亮的触感。感谢阅读！