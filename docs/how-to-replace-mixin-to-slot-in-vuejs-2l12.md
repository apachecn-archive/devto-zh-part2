# 如何将 mixin 替换为 Vue.js 中的 slot

> 原文：<https://dev.to/terrierscript/how-to-replace-mixin-to-slot-in-vuejs-2l12>

Mixin 是 Vue.js 的特色之一。

[https://vuej . org/v2/guide/mixins . html](https://vuejs.org/v2/guide/mixins.html)

这为多个组件提供了通用选项。

React 过去也有一个 mixin，但是现在已经弃用了。

这个原因在以下文件中有描述。

*   [https://react js . org/blog/2016/07/13/mixins-considered-habital . html](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)
*   [https://medium . com/@ Dan _ abra mov/mixins-are-dead-long-live-higher-order-components-94a 0 D2 F9 e 750](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750)

总结在这里。

*   引入隐式依赖
*   原因名称类别
*   导致滚雪球般的复杂性

也可以在 Vue.js 上申请。

# 如何替换 mixin？答案:槽。

在 React 中，我们可以用高阶组件或功能子组件来解决这个问题。

在 Vue.js 中，我们可以用 slot 代替 mixin。

在本文中，我使用鼠标事件跟踪的例子。

## 第一:使用 mixin 模式。

Mixin 的例子在这里:

```
// mouseMixin.js
export default {
  data() {
    return {
      x: 0,
      y: 0
    };
  },
  methods: {
    mousemove(e) {
      this.x = e.clientX;
      this.y = e.clientY;
    }
  }
} 
```

这里还有组件示例:

```
<!-- Item -->
<template>
  <div @mousemove="mousemove">
    <div>Item with mixin</div>
    <div>x: {{ x }}</div>
    <div>y: {{ y }}</div>
  </div>
</template>

<script>
import mouseMixin from "./mouseMixin"

export default {
  mixins: [ mouseMixin ],
};
</script> 
```

最后，组件被称为那些:

```
<!-- Parent.vue -->
<template>
  <div>
    <Item />
  </div>
</template>

<script>
export default {
  components: {
    Item,
  }
};
</script> 
```

在这个例子中，一些属性(`x`、`y`和`mousemove`)是隐式的。找到这些属性并不容易。

## 下一步:使用插槽

首先，编写 mixin 用法组件:

```
<!-- Item.vue -->
<template>
  <div>
    <div>Item with scope</div>
    <div>x: {{ x }}</div>
    <div>y: {{ y }}</div>
  </div>
</template>

<script>
export default {
  props: {
    x: Number,
    y: Number
  }
};
</script> 
```

这很简单，因为只得到了`x`和`y`。

接下来，我们创建`MouseEvent`组件。
这从`mousemove`得到`x`和`y`并传给`<slot>`和

```
<!-- MouseEvent.vue -->
<template>
  <div @mousemove="mousemove">
    <slot :x="x" :y="y"></slot>
  </div>
</template>

<script>
export default {
  data() {
    return {
      x: 0,
      y: 0
    };
  },
  methods: {
    mousemove(e) {
      this.x = e.clientX;
      this.y = e.clientY;
    }
  }
};
</script> 
```

最后，将`<MouseEvent>`和`<Item>`与`slot-scope`结合起来。

```
<!-- Parent.vue -->
<template>
  <div>
    <MouseEvent>
      <div slot-scope="{x, y}">
        <Item :x="x" :y="y" />
      </div>
    </MouseEvent>
  </div>
</template>

<script>
export default {
  components: {
    MouseEvent,
    Item,
  },
};
</script> 
```

好吧，是工作。

我们可以用`mixins`代替`slot`。你可以在避开`mixins`T3 的时候使用这个模式，但是 Vue.js 的作用域是如此的冗余。这是一个小痛点