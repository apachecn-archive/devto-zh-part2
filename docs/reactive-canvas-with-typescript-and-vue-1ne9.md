# 带有打字稿和 Vue 的活性画布

> 原文：<https://dev.to/deciduously/reactive-canvas-with-typescript-and-vue-1ne9>

# 或者说我是如何学会停止忧虑和热爱习俗的

我的“卡住的东西”系列中的另一个！这个特殊问题的解决方案最终相当简单，也许是显而易见的，但对我来说是一个迂回的过程，所以希望这对某些人有用。

[Vue](https://vuejs.org/) 提供[指令](https://vuejs.org/v2/api/#Directives)将你的模板与你的脚本挂钩。大多数情况下，这些就足够了，但是控制一个`canvas`元素需要低级别的 DOM 访问。`<canvas>`不支持`v-model`，所以我们需要一些其他的方法将数据传递到元素中进行渲染，这样它就可以与我们的视图模型保持同步。

幸运的是，他们会想到那个。使用[自定义指令](https://vuejs.org/v2/guide/custom-directive.html)，我们可以为自己的模板制作自己的`v-something`，我们可以为模板定义自己的行为。

编写此代码是为了适应由 Vue CLI 3.0 创建的项目，其中选择了“TypeScript”选项和类样式的组件语法。与其他配置一起使用应该很简单——这里的核心是指令本身。有关完整的语法，请参见文档链接。

我们将使用一个最简单的基于类的单文件组件:

```
<template>
  <div class="rxcanvas">
    <span>{{ size }}</span>
    <input type="range" min="1" max="100" step="5" id="size" v-model="size">
    <label for="size">- Size</label>
    <p><canvas></canvas></p>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Dot from "@/dot"; // defined below

@Component
export default class RxCanvas extends Vue {
  private data() {
    return {
      size: 10
    };
  }

  // computed property
  get dot(): Dot {
    return new Dot(this.$data.size);
  }
}
</script>

<style scoped>
</style> 
```

Enter fullscreen mode Exit fullscreen mode

我们的点类只知道给定一个画布元素作为目标来绘制自己:

```
// dot.ts
export default class Dot {
    private readonly color: string = "#000";
    constructor(private radius: number) { }
    public draw(canvas: HTMLCanvasElement): void {
        // resize canvas to dot size
        const canvasDim = this.radius * 2;
        canvas.width = canvasDim;
        canvas.height = canvasDim;

        // get context for drawing
        const ctx = canvas.getContext('2d')!;

        // start with a blank slate
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // find the centerpoint
        const centerX = canvas.width / 2;
        const centerY = canvas.height / 2;

        // create the shape
        ctx.beginPath();
        ctx.arc(centerX, centerY, this.radius, 0, 2 * Math.PI, false);
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.stroke();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了获得我们想要的行为，即一个适当大小和绘制的画布与我们的滑块输入同步，我们希望在每次更改时激发更多的逻辑，而不是简单地撞击一个数字。我们已经将所有的逻辑隐藏在我们的`Dot`类中- `Dot.draw(el)`知道如何做它需要的一切。我们只需要这个方法在有变化时自动触发。

首先，我们可以将该指令直接放在模板中的 canvas 元素上——我们已经知道它涉及哪些数据:

```
<canvas v-draw="dot"></canvas> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们的自定义指令叫做`draw`。你可以给它起任何你喜欢的名字。所有指令都有前缀`v-`。我们传入了`"dot"`，它是在我们的`RxCanvas`类上定义的计算属性。这样，每当`size`改变时，这个计算属性将创建一个大小正确的新点。

自定义指令在 Vue 组件上定义。当使用`vue-property-decorator`时，你可以把它放在装饰器选项:

```
@Component({
  directives: {
    "draw": function(canvasElement, binding) {
    // casting because custom directives accept an `Element` as the first parameter
      binding.value.draw(canvasElement as HTMLCanvasElement);
    }
  }
})
export default class RxCanvas extends Vue {
    // data(), dot(), etc
} 
```

Enter fullscreen mode Exit fullscreen mode

...就是这样！`binding.value`包含我们从计算的属性中得到的实际的`Dot`。这种语法利用了可用于指令的简写方式，允许我们精简定义，而不是拼出我们使用的每个钩子。承认在大多数情况下，这个特性的用户希望相同的逻辑发生在`bind`和`update`上，我们只是用我们的指令逻辑定义了一个函数，而不是一个包含钩子函数的对象，默认情况下它会得到那个行为。如果不使用简写，您可以将这个逻辑定义如下:

```
directives: {
    draw: {
      bind: function(canvasElement: Element, binding: VNodeDirective) {
        binding.value.draw(canvasElement as HTMLCanvasElement);
      },
      update: function(canvasElement, binding) {
        binding.value.draw(canvasElement as HTMLCanvasElement);
      }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在创建组件时，`bind`规则被触发一次，而每当从`RxCanvas`类创建的`VNode`实例发生变化时，就会触发`update`规则——包括对其`data`的变化。像这样拼写是冗长和重复的——可能的话最好用简写。

该自定义指令仅在您的`RxCanvas`组件上可用。如果你想在多个组件上使用它，全局定义它:

```
// index.ts
Vue.directive('draw': function(canvasElement, binding) {
      binding.value.draw(canvasElement as HTMLCanvasElement);
}); 
```

Enter fullscreen mode Exit fullscreen mode

万岁。