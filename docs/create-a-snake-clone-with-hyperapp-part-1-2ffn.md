# 使用 Hyperapp 创建蛇克隆，第 1 部分

> 原文：<https://dev.to/avalander/create-a-snake-clone-with-hyperapp-part-1-2ffn>

*(封面图片由[张秀坤·范尼](https://unsplash.com/photos/YkZW4ffuDnc)在 [Unsplash](https://unsplash.com/) )*

这是我们将要建造的东西的演示。

在本教程中，我将讲述如何使用 [hyperapp](https://github.com/hyperapp/hyperapp) 创建一个蛇克隆体。没有大的要求，但是你至少应该已经阅读过 hyperapp 的[入门指南](https://github.com/hyperapp/hyperapp#getting-started)，并且熟悉 ES6 语法。

特别是，这些是您应该熟悉的 ES6 特性，以便理解代码。

*   [导入报表](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)。
*   [箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。
*   [销毁作业](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。
*   [传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)。
*   [三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。实际上不是 ES6 的一个特性，但是我经常使用它，所以你应该能够读懂它。

# 创建项目并安装依赖项

要创建项目，只需使用`npm init`在一个空文件夹中创建一个新项目，并安装以下依赖项。

```
$ npm i --save hyperapp @hyperapp/fx 
```

Enter fullscreen mode Exit fullscreen mode

*   hyperapp : [hyperapp](https://github.com/hyperapp/hyperapp) 是一个用于创建 web 应用程序的极简 javascript 框架，很大程度上是受 Elm 的启发。
*   **@ hyperapp/FX**:[hyperapp/FX](https://github.com/hyperapp/fx)提供了我们可以用来轻松设置时间间隔和其他副作用的函数。

我使用 webpack 来构建这个项目，但是我不会在这里讨论如何设置它。如果你觉得懒，你可以从这个 repo 下载设置[。](https://github.com/Avalander/hypersnake-tutorial/tree/setup-build)

现在我们应该准备开始编码了。

# 设置 hyperapp

Hyperapp 公开了一个名为`app`的函数，它接收一个初始状态、我们的应用程序可用的操作、一个根据状态呈现视图的函数以及一个装载应用程序的 DOM 元素。因为我们正在使用`@hyperapp/fx`，我们需要用`withFx`方法包装我们的`app`。让我们从我们的`main.js`文件开始。

```
// main.js
import { app } from 'hyperapp'
import { withFx } from '@hyperapp/fx'

const state = {}

const actions = {}

const view = state => {}

const game = withFx(app) (state, actions, view, document.body) 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 SVG 辅助对象

我们将使用 SVG 来渲染我们的游戏。我们可以很容易地使用 canvas API 来代替，但是我发现当你只需要画基本的形状时，SVG 是一个很好的选择。

包`@hyperapp/html`提供了一些方便的函数来创建虚拟 dom 节点。不幸的是，还没有官方的软件包提供类似的帮助功能来创建 SVG 元素。我们仍然可以用`hyperapp`的`h`函数创建 SVG 元素，但是如果我们能像这样编写代码不是很好吗？

```
svg({ viewBox: '0 0 600 400' }, [
    g({}, [
        rect({ x: 0, y: 0, width: 50, height: 50, fill: '#a4b398' })
    ])
]) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以很容易地自己编写这样的助手，所以让我们继续创建一个`svg.js`文件，并将其导入到我们的`main.js`中。

```
// svg.js
import { h } from 'hyperapp'

export const svg = (attrs, children) => h('svg', attrs, children)
export const g = (attrs, children) => h('g', attrs, children)
export const rect = (attrs, children) => h('rect', attrs, children) 
```

Enter fullscreen mode Exit fullscreen mode

```
// main.js
import { svg, g, rect } from './svg' 
```

Enter fullscreen mode Exit fullscreen mode

> ## 注:jsx 呢？
> 
> 如果你在 hyperapp 上寻找资源，你会注意到大多数都使用 jsx 而不是 html 助手。虽然我们可以在这里做同样的事情，但是我更喜欢使用普通的 Javascript 来定义我的视图，所以我坚持使用 html helper 函数。
> 
> 然而，如果你更愿意写 jsx，你应该能够按照这个教程，你不需要带有 svg 帮助函数的`svg.js`文件。如果你使用 jsx，只要确保你从`hyperapp`中导入函数`h`到你的`main.js`中。

现在我们都设置好了，是时候开始实际构建我们的游戏了。

# 创建背景

背景将是一个绿色的矩形，覆盖整个可玩区域。让我们开始定义一些常数。

```
// main.js
const SIZE = 15
const WIDTH = SIZE * 40
const HEIGHT = SIZE * 27

const COLORS = {
    background: '#088c64',
} 
```

Enter fullscreen mode Exit fullscreen mode

`SIZE`是细胞将会有多大。`WIDTH`和`HEIGHT`是游戏区域的大小。我们没有用绝对值来定义它们，而是按照与`SIZE`成比例的方式来定义，这样一来，无论比例大小如何，电路板的相对尺寸总是相同的。

是我们将要用来填充背景的颜色。

我们需要在游戏将要呈现的地方添加一个`svg`元素，所以让我们修改一下我们的`view`函数。

```
// main.js
const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [

    ]) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在那里嵌套更多的 SVG 元素来创建我们的背景，但是如果我们有很多元素要绘制的话,`view`函数会变得很大，所以让我们为背景创建一个组件。

```
// main.js
const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [
        Background(),
    ])

const Background = () =>
    g({ key: 'background' }, [
        rect({ x: 0, y: 0, width: WIDTH, height: HEIGHT, fill: COLORS.background }),
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们应该会在屏幕上看到一个绿色的大矩形。

# 创造蛇

让我们添加游戏的主角，蛇。我们将把蛇的位置存储为我们的`state`对象中的一组点。

```
// main.js
const state = {
    snake: [
        { x: 3 * SIZE, y: 3 * SIZE },
        { x: 2 * SIZE, y: 3 * SIZE },
        { x: 1 * SIZE, y: 3 * SIZE },
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加一些颜色来渲染我们的蛇。

```
//main.js
const COLORS = {
    snake: {
        fill: '#bcaba0',
        stroke: '#706660',
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建另一个组件来渲染蛇。

```
// main.js
const Snake = state =>
    g({ key: 'snake' },
        state.map(({ x, y }) => rect({
            x, y, width: SIZE, height: SIZE,
            fill: COLORS.snake.fill,
            stroke: COLORS.snake.stroke,
            'stroke-width': 2
        }))
    )

const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [
        Background(),
        Snake(state.snake),
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

1.  函数`Snake`接收蛇的身体数组作为参数。
2.  它创建了一个 SVG 组来封装蛇的身体元素。
3.  它将 body 数组中的每个点映射到具有一些样式属性的相同坐标中的一个`rect`对象。

## 使蛇动起来

现在我们应该在屏幕上看到我们的蛇，但它还没有移动。是时候解决这个问题了。

我们需要一种方法来定期更新我们的状态。我们可以使用`@hyperapp/fx`的`delay`函数。`delay`的工作方式很像`setTimeout`，但是它接收的是给定延迟后要调用的动作的名称，而不是函数。让我们看看如何使用`delay`来创建我们的游戏循环。

```
// main.js
import { withFx, delay } from '@hyperapp/fx'

const UPDATE_INTERVAL = 150

const actions = {
    frame: () => [
        delay(UPDATE_INTERVAL, 'frame')
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们从`@hyperapp/fx`导入函数`delay`。
2.  我们创建常数`UPDATE_INTERVAL`，它是每一帧之间经过的毫秒数。
3.  我们创建了一个名为`frame`的动作，它将每隔`UPDATE_INTERVAL`毫秒产生另一个帧。

这很方便，但是还没有发生任何事情。我们需要触发第一个框架，因此更新链将开始滚动。幸运的是，`hyperapp`的`app`函数返回一个连接了所有动作的对象，所以我们可以从那里第一次调用`frame`。

```
// main.js
const game = withFx(app) (state, actions, view, document.body) // This line is there already, don't write it again.
game.frame() 
```

Enter fullscreen mode Exit fullscreen mode

这应该能让事情有所进展。然而，什么也没有发生，我们只有每 150 毫秒产生更多的`frame`动作的`frame`动作，但是它们没有做任何其他事情。让我们创建一个动作，每次生成一个帧时，它都会打印到控制台，以检查它是否工作。

```
// main.js
const actions = {
    sayHi: () => console.log('Hello, there!'),
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要一种方法来触发每次我们进入一个新的帧的动作。这对于`@hyperapp/fx`来说很简单。使用`@hyperapp/fx`，一个动作可以返回一组效果(其中一个效果是`delay`，我们已经知道了)。还有一个叫做`action`的效果，它从应用程序的 actions 对象触发一个动作。所以让我们从`@hyperapp/fx`导入`action`，从`frame`触发`sayHi`。

```
// main.js
import { withFx, delay, action } from '@hyperapp/fx'

const actions = {
    frame: () => [
        action('sayHi'),
        delay(UPDATE_INTERVAL, 'frame'),
    ],
    sayHi: () => console.log('Hello, there!'),
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在查看控制台，你会看到一堆`Hello, there!`文本堆积如山。

正如我们已经看到的，`action`在我们的`actions`对象中接收一个动作的名称并触发它。或者，它接收第二个带参数的参数，该参数将被发送到触发的操作。我们稍后会用到它。

在控制台上打印文本很有趣，但我们是来看蛇移动的，所以让我们开始吧。

我们首先需要的是蛇前进的方向。我们将在`state`对象中添加一个值为`'right'`的`direction`属性。

```
// main.js
const state = {
    direction: 'right',
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将删除`sayHi`动作，并创建一个动作来更新蛇。

```
// main.js
const actions = {
    frame: () => [
        action('updateSnake'),
        delay(UPDATE_INTERVAL, 'frame'),
    ],
    updateSnake: () => state => ({
        ...state,
        snake: updateSnake(state.snake, state.direction),
    }),
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经创建了动作`updateSnake`，它将返回当前状态的一个浅层副本以及 snake 的一个更新版本，我们在`frame`中触发该动作。

我们仍然需要实现函数`updateSnake`。有很多方法可以让蛇动起来。简单的方法是从尾部开始遍历数组，将每个单元格移动到它前面的单元格的位置，然后将头部移向当前方向。

```
// main.js
const updateSnake = (snake, direction) => {
    for (let i = snake.length - 1; i > 0; i--) {
        snake[i].x = snake[i - 1].x
        snake[i].y = snake[i - 1].y
    }
    if (direction === 'right') {
        snake[0].x += SIZE
    }
    if (direction === 'left') {
        snake[0].x -= SIZE
    }
    if (direction === 'down') {
        snake[0].y += SIZE
    }
    if (direction === 'up') {
        snake[0].y -= SIZE
    }
    return snake
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们循环遍历蛇，从最后一个单元格开始，到第二个单元格结束。我们将每个单元格移动到它前面的单元格的位置。
2.  我们将头部向当前方向移动一个位置。

现在我们应该看到蛇向右移动。即使这样做是可行的，我们也可以做一些更简洁的事情来移动头部，而不是使用一堆`if`语句。我建议的方法是用一个可能的方向作为关键字的字典和一个带有`x`和`y`分量的向量，它们将被应用到速度中来计算运动。

这比听起来容易。让我们从创建方向字典开始。

```
// main.js
const DIRECTIONS = {
    left: { x: -1, y: 0 },
    right: { x: 1, y: 0 },
    up: { x: 0, y: -1 },
    down: { x: 0, y: 1 },
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们从我们的`updateSnake`函数中删除了那串`if`语句，取而代之的是通过增加乘以当前方向的相关坐标的单元大小来转换头部的坐标`x`和`y`。

```
// main.js
const updateSnake = (snake, direction) => {
    for (let i = snake.length - 1; i > 0; i--) {
        snake[i].x = snake[i - 1].x
        snake[i].y = snake[i - 1].y
    }

    snake[0].x += SIZE * DIRECTIONS[direction].x
    snake[0].y += SIZE * DIRECTIONS[direction].y

    return snake
} 
```

Enter fullscreen mode Exit fullscreen mode

## 控制方向

我们的蛇正在移动。下一步是能够用箭头键改变方向。

为了实现这一点，我们将使用一个效果在按键时触发一个动作。现在您可能已经猜到了，`@hyperapp/fx`为此公开了一个名为`keydown`的函数，所以让我们导入并使用它。

```
// main.js
import { withFx, delay, action, keydown } from '@hyperapp/fx' 
```

Enter fullscreen mode Exit fullscreen mode

`keydown`，很像`action`和`delay`接收一个当按键被按下时触发的动作的名称作为参数。我们只需要触发那个效果一次，所以我们必须为它找一个地方。最简单的是创建一个会触发`keydown`效果的`start`动作和第一个`frame`动作，并调用那个动作而不是`frame`来开始游戏循环。

```
// main.js
const actions = {
    start: () => [
        keydown('keyPressed'),
        action('frame'),
    ],
}

// Replace 'game.frame()' with this.
game.start() 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须实施`keyPressed`行动。基本上我们要忽略所有不是`ArrowUp`、`ArrowDown`、`ArrowLeft`或者`ArrowRight`的键，我们要把这四个平移到等价的方向。让我们首先用键和方向之间的翻译创建一个新字典。

```
// main.js
const KEY_TO_DIRECTION = {
    ArrowUp: 'up',
    ArrowDown: 'down',
    ArrowLeft: 'left',
    ArrowRight: 'right',
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来有点重复，但它会让我们的生活变得更容易。

现在进行`keyPressed`动作。它将接收一个常规的`keydown`事件，我们只对它的属性`key`感兴趣(如果我们对它感兴趣，属性键将是这四个`Arrow[Something]`值中的一个，否则就是另一个字符串)。如果箭头键被按下，`keyPressed`动作应该更新状态中的方向，否则不做任何事情。

```
// main.js
const actions = {
    keyPressed: ({ key }) => state => ({
        ...state,
        direction: Object.keys(KEY_TO_DIRECTION).includes(key)
            ? KEY_TO_DIRECTION[key]
            : state.direction
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这种方法可行，但在语义上不准确。我们称我们的行动为`keyPressed`，但它实际上改变了方向。如果`keyPressed`只根据按下的键检查是否需要触发另一个动作，并且我们创建一个负责改变方向的新动作，我们可以更准确。

```
// main.js
const actions = {
    keyPressed: ({ key }) =>
        (Object.keys(KEY_TO_DIRECTION).includes(key)
            ? [ action('changeDirection', KEY_TO_DIRECTION[key]) ]
            : []
        ),
    changeDirection: direction => state => ({
        ...state,
        direction,
    }),
} 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧。现在`keyPressed`将检查事件的`key`属性是否是我们的`KEY_TO_DIRECTION`字典中的一个键。如果是这样，它将触发一个具有适当方向的`changeDirection`，否则不会触发任何附加动作。

简单地接收一个方向并用该方向更新状态。

还有一件事我们需要处理。在目前的状态下，我们的蛇可以切换到相反的方向。如果它在向右移动，玩家按下左箭头，它会向左改变方向，走过自己。我们希望防止这种情况发生。

为了实现这一点，我们将更加完善我们的`changeDirection`行动。如果新方向与当前方向不相反，它将只更新*而不是盲目更新方向*。为了容易地知道当前方向和新方向是否相反，我们将用每个方向的相反方向创建一个新字典(这是我们创建的最后一个方向字典，我保证)。

```
// main.js
const OPPOSITE_DIRECTION = {
    up: 'down',
    down: 'up',
    left: 'right',
    right: 'left',
}

const actions = {
    changeDirection: direction => state => ({
        ...state,
        direction: (direction === OPPOSITE_DIRECTION[state.direction]
            ? state.direction
            : direction
        )
    }),
} 
```

Enter fullscreen mode Exit fullscreen mode

现在`changeDirection`只有和之前的方向不相反才会切换到新的方向。

但是，代码中有一个错误。`changeDirection`可以在帧间触发多次，而蛇只会移动一次。因此，如果蛇向左移动，玩家按下向上箭头，则`direction`变为`'up'`。现在，如果玩家在下一帧之前按下右箭头，`direction`将在蛇向上移动之前变为`'right'`。实际上，蛇将在下一帧从左向右转换方向。

去吧，把`UPDATE_INTERVAL`改成更大的值，比如`500`，自己看。

避免这种情况的一种方法是在状态中添加一个新属性`next_direction`，并让`changeDirection`更新该属性。然后，我们总是在`direction`有当前的方向，我们可以检查我们没有设置相反的方向。

然后，我们将创建一个新的动作`updateDirection`，它将每帧只更新一次方向。

```
// main.js
const state = {
    direction: 'right',
    next_direction: 'right',
}

const actions = {
    frame: () => [
        action('updateDirection'),
        action('updateSnake'),
        delay(UPDATE_INTERVAL, 'frame'),
    ],
    updateDirection: () => state => ({
        ...state,
        direction: state.next_direction,
    }),
    changeDirection: direction => state => ({
        ...state,
        next_direction: (direction === OPPOSITE_DIRECTION[state.direction]
            ? state.next_direction
            : direction
        )
    }),
} 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧。

1.  我们给`state`添加了一个新的属性`next_direction`。
2.  `changeDirection`将在`next_direction`而不是`direction`中放置下一帧的方向，检查新值是否与`direction`中的方向相反。
3.  我们创建了一个新的动作`updateDirection`，它将在每帧被触发一次，并在蛇被更新之前将最近的值放在`next_direction`中并放在`direction`中。

# 结论

这是一个很大的文本，祝贺你到目前为止！在教程的第二部分，我们将探索如何添加苹果和得分，使蛇成长，并在蛇与边界或自身发生碰撞时结束游戏。

你可以在这里找到我们已经写好的代码。

深呼吸，泡一大杯茶，准备好了再继续教程的第二部分(也是最后一部分)。

[![avalander image](img/dfd7bac1c61f0ed6ce18f3bb69b0f187.png)](/avalander) [## 使用 Hyperapp 创建蛇克隆，第 2 部分

### avalander Aug 2 ' 187 分钟读数

#tutorial #javascript #hyperapp #snake](/avalander/create-a-snake-clone-with-hyperapp-part-2-lp4)