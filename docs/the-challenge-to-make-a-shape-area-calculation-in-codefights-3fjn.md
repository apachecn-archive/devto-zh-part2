# 在编码信号中进行形状面积计算挑战

> 原文：<https://dev.to/equiman/the-challenge-to-make-a-shape-area-calculation-in-codefights-3fjn>

语言:【🇪🇸】[西班牙语](https://dev.to/equimancho/codesignal-el-reto-de-calcular-el-area-de-una-figura-3o5k)-【🇺🇸】[英语](https://dev.to/equiman/the-challenge-to-make-a-shape-area-calculation-in-codefights-3fjn)

* * *

从大学开始，我就不被鼓励去做编程挑战，直到我被邀请去 [CodeSignal](https://app.codesignal.com/signup/8qEwaRfZDeActXZsW/main) 一个页面，在那里你可以和几个朋友或公司“玩”关于解决算法的游戏。

我在第二个入门阶段发现了一个计算形状面积的练习，它吸引了我所有的注意力，在开始开发它之前，我想了至少三种方法来解决它:循环、递归和数学……太晚了，现在我已经染上了开发热。

# 问题

下面我们将定义一个`n`-有趣的多边形。你的任务是找出给定`n`的多边形的面积。

有趣的多边形是一个边长为 T1 的正方形。一个`n`-有趣的多边形是通过将`n - 1`-有趣的多边形和`1`-有趣的多边形并排附加到它的边缘而获得的。可以看到下图中的`1` -、`2` -、`3` -和`4`-有趣的多边形。

<figure>[![Shape Area](../Images/930caffe7b78980b5e69fef7fd9c3ad8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u5BHfIvI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ithwid4aoppbawxs1c43.png) 

<figcaption>图片取自 [CodeSignal](https://codesignal.com)</figcaption>

</figure>

# 样品

对于`n = 2`，输出应该是:`shapeArea(n) = 5`。
对于`n = 3`，输出应该是:`shapeArea(n) = 13`。

# 我的解决方案

我决定给自己一个任务，用 node.js 的 3 种方法来解决它。我在同一天解决了循环和递归，但数学解决方案花费了我比预期更多的时间，因为我必须复习几年前在大学里看到的关于数值方法的笔记。

```
const x = {};
x.loopShapeArea = (n) => {
  let area = 1;
  for (let i = 1; i <= n; i++) {
    area += i * 4 - 4;
  }
  return area;
};

x.recursionShapeArea = (n) => {
  if (n === 1) {
    return 1;
  } else {
    return n * 4 - 4 + x.recursionShapeArea(n - 1);
  }
};

x.mathShapeArea = (n) => {
  return Math.pow(n, 2) + Math.pow(n - 1, 2);
};

const shapeArea = (n) => {
  let solution = {};
  if (0 < n && n <= Math.pow(n, 4)) {
    let obj = {
      0: "loopShapeArea",
      1: "recursionShapeArea",
      2: "mathShapeArea"
    };
    for (let item in obj) {
      let fx = obj[item];
      solution[fx] = {};
      solution[fx].result = {};
      let hrstart = process.hrtime();
      for (let i = 1; i <= n; i++) {
        let result = x[fx](i);
        solution[fx].result[i] = result;
      }
      let hrend = process.hrtime(hrstart);
      solution[fx].execution = {};
      solution[fx].execution.s = hrend[0];
      solution[fx].execution.ms = hrend[1] / 1000000;
    }
    return solution;
  } else {
    return Error("Not a valid number");
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 结果

这个练习有一个 4000ms 的超时条件，所以添加一个测量运行时间的方法是很好的。从一开始，我就认为最优解应该是数学。你怎么想呢?哪个更快？

```
let n = 9; //Change this value
const result = shapeArea(n);
for (let item in result) {
  console.log(`${item} -> Execution time (hr): ${result[item].execution.s}s ${result[item].execution.ms}ms.`
  );
}
console.log(result); 
```

Enter fullscreen mode Exit fullscreen mode

我为什么喜欢 CodeSignal？因为有时你想出了一个复杂而详尽的答案，而当你看到其他解决方案时，你找到了一个可笑而简单的方法来解决它。该死…为什么我以前没想到呢？

加入我们的 [CodeSignal](https://app.codesignal.com/signup/8qEwaRfZDeActXZsW/main) ,享受编写我们喜欢的代码吧！

# 奖金追踪

我第一次编写这段代码时，在`hrstart`和`hrstart`之间添加了`console.log`，注入了不需要的错误，打印时间计入了解决方案中。当我看到结果时，我似乎疏远了递归是数学解决方案的赢家。

但是当我把`console.log`从那里移走后，我看到了真正的结果。所以，**如果你想要一个真实的时间度量，避免额外的操作或者屏幕交互**。

* * *

**都是乡亲们！**
**快乐编码** 🖖

[![beer](../Images/192892baef71a32ea4a5e98a4927b05e.png)T2】](https://github.com/sponsors/deinsoftware)