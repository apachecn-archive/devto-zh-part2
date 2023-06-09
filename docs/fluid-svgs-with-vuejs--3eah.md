# 带有 Vue.js 的流体 SVG

> 原文：<https://dev.to/lobo_tuerto/fluid-svgs-with-vuejs--3eah>

在进行数据可视化时，我非常喜欢使用流体 SVG。

流体 SVG 是那些可以在*父对象*允许的范围内在水平轴上延伸的 SVG。它们保持它们的长宽比，如果它们的大小发生变化，它们会相应地增长和收缩以进行自我调整。

诀窍在于如何定义``元素，尤其是它的`viewBox`属性。
T5 也是，不要在上面定义一个`height`或者`width`。

* * *

这里有一个单独的文件组件在 **Vue.js** 中，它的行为与此完全一样。

姑且叫它`src/components/FluidSvg.vue` :

```
<template>
  <div>

    
      <rect
        class="my-square"
        :x="rect.x"
        :y="rect.y"
        :width="rect.width"
        :height="rect.height"
      ></rect>
    

  </div>
</template>

<script>
export default {
  name: 'FluidSvg',

  data () {
    return {
      svg: {
        width: 1000,
        height: 1000
      },
      rect: {
        x: 100,
        y: 100,
        width: 300,
        height: 300
      }
    }
  },

  computed: {
    viewBoxString () {
      return `0 0 ${this.svg.width}  ${this.svg.height}`
    }
  }
}
</script>

<style>
svg {
  background-color: antiquewhite;
  border: 2px solid black;
}

.my-square {
  fill: white;
  stroke: red;
  stroke-width: 2;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

现在，在其他组件中使用它，就像这样:

```
<template>
  <div>
    <div class="example-1">
      <fluid-svg></fluid-svg>
    </div>

    <div class="example-2">
      <fluid-svg></fluid-svg>
    </div>

    <div class="example-3">
      <fluid-svg></fluid-svg>
    </div>
  </div>
</template>

<script>
import FluidSvg from '@/components/FluidSvg'

export default {
  name: 'HelloWorld',
  components: { FluidSvg },
}
</script>

<style>
.example-1 {
  width: 100px;
}

.example-2 {
  width: 200px;
}

.example-3 {
  width: 400px;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这是你将会看到的:

[![Fluid SVGs](img/5e7320c37bba64735ecad470521c0d7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HHXN9RD0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lobotuerto.com/blog/fluid-svgs-with-vuejs/fluid-svgs.png)