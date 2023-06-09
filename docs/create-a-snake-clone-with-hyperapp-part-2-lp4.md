# 使用 Hyperapp 创建蛇克隆，第 2 部分

> 原文：<https://dev.to/avalander/create-a-snake-clone-with-hyperapp-part-2-lp4>

*(封面图片由[张秀坤·范尼](https://unsplash.com/photos/YkZW4ffuDnc)在 [Unsplash](https://unsplash.com/) )*

这是教程的第二部分，如果你还没有，请确保遵循[第 1 部分](https://dev.to/avalander/create-a-snake-clone-with-hyperapp-part-1-2ffn)。你可以在这里查看到目前为止代码应该是什么样子的。游戏最终版本的试玩是[这里是](https://avalander.github.io/hypersnake-tutorial/)。

# 创造苹果

让我们从添加一个创建苹果的函数开始。这个函数应该把苹果放在棋盘上的任意一个单元格中。

```
// main.js
const randInt = (from, to) =>
    Math.floor(Math.random() * (to - from) + from)

const createApple = () =>
    ({
        x: randInt(0, WIDTH/SIZE) * SIZE,
        y: randInt(0, HEIGHT/SIZE) * SIZE,
    }) 
```

Enter fullscreen mode Exit fullscreen mode

1.  `randInt`将返回一个在`from`和`to`之间的随机整数。
2.  `createApple`将在棋盘内返回一个带有随机`x`和`y`坐标的物体。

我们还需要选择一些颜色来渲染我们的苹果，所以让我们将它添加到我们的`COLORS`常量中。

```
// main.js
const COLORS = {
    apple: {
        fill: '#ff5a5f',
        stroke: '#b23e42',
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在状态对象中添加一个苹果。

```
// main.js
const state = {
    apple: createApple(),
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单。现在让我们在屏幕上画我们的苹果。我们将为它创建一个新的组件，它将简单地用我们之前在苹果坐标上选择的颜色绘制一个矩形。

```
// main.js
const Apple = ({ x, y }) =>
    g({ key: 'apple' }, [
        rect({
            x, y, width: SIZE, height: SIZE,
            fill: COLORS.apple.fill,
            stroke: COLORS.apple.stroke,
            'stroke-width': 2
        })
    ])

const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [
        Background(),
        Apple(state.apple),
        Snake(state.snake),
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

确保将`view`函数中的`Apple`组件放在`Snake`组件之前，否则当蛇和苹果重叠时，苹果会被画在上面。

## 吃苹果

当头在同一个牢房时，蛇应该吃苹果。首先，我们将创建一个函数`collision`，如果两个对象在同一个单元格中，它将返回`true`，否则返回`false`。

```
// main.js
const collision = (a, b) =>
    a.x === b.x && a.y === b.y 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将创建一个动作来检查蛇头是否与苹果在同一个单元格中，如果是，则触发另一个动作来吃掉苹果。

```
// main.js
const actions = {
    frame: () => [
        action('updateDirection'),
        action('updateSnake'),
        action('checkEatApple'),
        delay(UPDATE_INTERVAL, 'frame'),
    ],
    checkEatApple: () => state =>
        (collision(state.snake[0], state.apple)
            ? [ action('eatApple'),
                action('relocateApple'), ]
            : []
        ),
    eatApple: () => state => ({
        ...state,
        snake: growSnake(state.snake),
    }),
    relocateApple: () => state => ({
        ...state,
        apple: createApple(),
    }),
}

const growSnake = snake =>
    [ ...snake, {
        x: snake[snake.length - 1].x,
        y: snake[snake.length - 1].y,
    }] 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们创建了`checkEatApple`动作。它将检查蛇头和苹果是否在同一个单元格中。如果是这样，就会触发两个新的动作，`eatApple`和`relocateApple`，否则不会触发任何附加动作。
2.  我们从`frame`动作触发`checkEatApple`动作，这样它将检查每一帧。
3.  我们创建动作`eatApple`。它会在蛇的尾部增加一个新的细胞。
4.  我们创建动作`relocateApple`。它将使用我们之前实现的`createApple`函数在随机位置创建一个新的苹果。

## 添加分数

我们希望有一个分数，蛇每吃一个苹果，分数就会增加，并显示在屏幕上。让我们向状态添加一个`score`属性并呈现它，然后我们将负责递增它。

```
// main.js
const state = {
    score: 0,
} 
```

Enter fullscreen mode Exit fullscreen mode

为了能够渲染它，我们将需要一个额外的 SVG 助手来创建一个[tex](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/text)元素。让我们将它添加到我们的`svg.js`文件中。

```
// svg.js
export const text = (attrs, children) => h('text', attrs, children) 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个`Score`组件，并在我们的`view`函数中呈现它。

```
// main.js
import { g, rect, svg, text } from './svg'

const score_style = {
    font: 'bold 20px sans-seriff',
    fill: '#fff',
    opacity: 0.8,
}

const Score = state =>
    g({ key: 'score' }, [
        text({
            style: score_style,
            x: 5,
            y: 20,
        }, state)
    ])

const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [
        Background(),
        Apple(state.apple),
        Snake(state.snake),
        Score(state.score),
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们创建了一些样式来更好地显示乐谱文本。
2.  我们创建了`Score`组件，它将接收来自状态的分数，并将其呈现为一个`text`元素。
3.  我们在`view`函数中添加了对`Score`的调用。

为了增加分数，我们将创建一个新的动作，`updateScore`,当蛇吃苹果时，这个动作将由`checkEatApple`触发。

```
// main.js
const actions = {
    checkEatApple: () => state =>
        (collision(state.snake[0], state.apple)
            ? [ action('eatApple'),
                action('relocateApple'),
                action('updateScore', 10) ]
            : []
        ),
    updateScore: value => state => ({
        ...state,
        score: state.score + value
    }), 
```

Enter fullscreen mode Exit fullscreen mode

# 结束游戏

我们可以控制这条蛇，它正在吃随机放置的苹果，每吃一个苹果就增加一分。唯一缺少的是结束游戏的方法。

传统上，贪吃蛇游戏有两个结束条件:

1.  蛇头与棋盘的一个边界发生碰撞。
2.  蛇头会与身体的任何其他细胞发生碰撞。

我们将实现这两个目标。

## 出界

为了检查蛇是否与边界碰撞，我们将在更新它之后检查它的位置是否超出了任何棋盘边界。我们将从创建一个函数`isOutOfBounds`开始，该函数将接收一个点，如果它超出了棋盘的界限，则返回`true`，否则返回`false`。

```
// main.js
const isOutOfBounds = ({ x, y }) =>
    x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT 
```

Enter fullscreen mode Exit fullscreen mode

我们希望在游戏结束时停止更新游戏，所以我们不会从`frame`本身触发一个新的`frame`动作，而是创建一个新的动作，并将其命名为`continue`。这个动作会检查蛇是否出界，如果没有，会触发新的`frame`，否则不会。

```
// main.js
const actions = {
    frame: () => [
        action('updateDirection'),
        action('updateSnake'),
        action('checkEatApple'),
        action('continue'),
    ],
    continue: () => state =>
        (isOutOfBounds(state.snake[0])
            ? []
            : delay(UPDATE_INTERVAL, 'frame')
        ),
} 
```

Enter fullscreen mode Exit fullscreen mode

往前跑，跑到所有的边界，你会看到游戏停止运行。

## 自我碰撞

为了检查蛇头是否与蛇尾碰撞，我们将创建一个新函数`selfCollision`，它将迭代蛇尾的每个单元格，如果找到与蛇头位置相同的单元格，则返回`true`，否则返回`false`。

```
// main.js
const selfCollision = ([ head, ...tail ]) =>
    tail.some(cell => collision(head, cell)) 
```

Enter fullscreen mode Exit fullscreen mode

函数`Array.prototype.some`接收一个谓词函数，如果它对数组中的任何元素求值为`true`，则返回`true`，否则返回`false`，这正是我们所需要的。此外，我们可以重用函数`collision`来检查头部是否与尾部的任何细胞发生碰撞。

为了在蛇踩到自己身上时结束游戏，我们可以在`continue`动作中添加一个对`selfCollision`的检查，如果它返回`true`就结束游戏。

```
// main.js
const actions = {
    continue: () => state =>
        (isOutOfBounds(state.snake[0]) || selfCollision(state.snake)
            ? []
            : delay(UPDATE_INTERVAL, 'frame')
        ),
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结束游戏画面

现在，只要满足两个结束条件中的一个，游戏就会停止运行，但这还不够，我们需要在屏幕上显示一个*游戏，以便用户知道游戏已经结束。*

我们需要知道游戏是正在运行还是已经结束，以决定我们是否必须在屏幕上渲染游戏。我们将为状态对象添加一个`is_running`属性，并将其初始化为`true`。

```
// main.js
const state = {
    is_running: true,
} 
```

Enter fullscreen mode Exit fullscreen mode

当游戏结束时，我们将设置`is_running`为假。为了实现这一点，我们将创建一个新动作`updateIsRunning`，并在游戏结束时从`continue`动作中触发它，以将`is_running`设置为`false`。

```
// main.js
const actions = {
    continue: () => state =>
        (isOutOfBounds(state.snake[0]) || selfCollision(state.snake)
            ? action('updateIsRunning', false)
            : delay(UPDATE_INTERVAL, 'frame')
        ),
    updateIsRunning: value => state => ({
        ...state,
        is_running: value,
    }),
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建一个组件，将我们的游戏呈现在屏幕上。

```
// main.js
const game_over_style = {
    title: {
        font: 'bold 48px sans-seriff',
        fill: '#fff',
        opacity: 0.8,
        'text-anchor': 'middle',
    },
    score: {
        font: '30px sans-seriff',
        fill: '#fff',
        opacity: 0.8,
        'text-anchor': 'middle',
    }
}

const GameOver = score =>
    g({ key: 'game-over'}, [
        rect({
            x: 0, y: 0, width: WIDTH, height: HEIGHT,
            fill: '#000',
            opacity: 0.4,
        }),
        text({
            style: game_over_style.title,
            x: WIDTH/2, y: 100,
        }, 'Game Over'),
        text({
            style: game_over_style.score,
            x: WIDTH/2, y: 160,
        }, `Score: ${score}`),
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的，我们简单地创建了一个`GameOver`函数，它返回一个半透明的矩形来使游戏变暗，一个文本显示*游戏结束*，一个文本显示最终比分。

现在让`view`函数在游戏不运行的时候渲染一下。

```
// main.js
const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [
        Background(),
        Apple(state.apple),
        Snake(state.snake),
        Score(state.score),
        !state.is_running ? GameOver(state.score) : null,
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

这就足够了，然而，因为`GameOver`组件已经告诉我们最终的分数，所以当游戏结束时没有必要再渲染`Score`组件，所以我们可以根据`is_running`的值来渲染。

```
// main.js
const view = state =>
    svg({ viewBox: `0 0 ${WIDTH}  ${HEIGHT}`, width: WIDTH, height: HEIGHT}, [
        Background(),
        Apple(state.apple),
        Snake(state.snake),
        state.is_running
            ? Score(state.score)
            : GameOver(state.score),
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

# 改进

游戏现在可以运行了，但是如果你想做更多的实验，我们还可以做一些事情来改进和扩展它。以下是可能的改进列表。

*   让游戏每 100 分跑得更快。实现这一点的一个简单方法是在状态中使用更新间隔，而不是常数，但是要考虑到它不能为零或更低。
*   我们用来移动蛇的算法相当简单，我们真的不需要为身体的每个细胞计算新的位置。另一种方法是选取尾部的最后一个单元格，并将其移动到数组开头头部的新位置，而不移动任何其他单元格。
*   增加一种重启游戏的方式(即不重装窗口)。
*   让不同的苹果获得不同的分数。
*   这不太可能，但苹果可能会出现在已经被蛇占据的细胞中，找到一种方法来防止这种情况。

# 结论

你可以在这里查看最终代码。

我希望这篇教程能帮助你更好地理解如何用`hyperapp`和`@hyperapp/fx`建模复杂的动作，并且你对它的能力有更好的了解。不要犹豫，在评论区写下你的想法或问题。