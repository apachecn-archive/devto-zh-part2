# 使用 SDL2: Pong 2

> 原文：<https://dev.to/noah11012/using-sdl2-pong-2-4pe>

从我们离开的地方继续，`Paddle`类被创建来处理一个 paddle 的几个方面。目前，有三种方法来管理与您的球拍相关的特定问题:

1.  `handle_input()`

主窗口没有处理的任何事件将在`Paddle`中处理，如果类需要这样做的话。

1.  `update()`

更新我们的桨的状态，包括位置，当我们到达时，碰撞。

1.  `draw()`

用矩形绘制一个桨的表示。

`Paddle`类也包含位置的状态，由这些私有方法变量组成:

```
double m_y;
Type      m_type;
Direction m_direction;
SDL_Rect  m_position; 
```

Enter fullscreen mode Exit fullscreen mode

如果您至少从文章[使用 SDL2:移动图像](https://dev.to/noah11012/using-sdl2-scaling-and-moving-an-image-pj)开始关注过这个系列，您会知道我们遇到了一个 C++如何处理不同类型的算术运算的问题。例如，将`double`加到`int`将使`int`操作数*提升*为`double`。问题的要点是，通过在`Pong`的主更新循环中传递`1.0/60.0`到`update()`，帧速率被限制在 60 fps。这个数字大约等于`0.0167`，你会注意到它是一个浮点数。试图将浮点数存储在仅用于保存整数值的变量中会截断浮点数的小数部分。为了纠正这个问题，我们有一个额外的变量来首先保存新位置的结果，然后将其存储在正确的`SDL_Rect`成员变量中。这样，小数部分被保存在`double`变量中。

还和我在一起吗？

很好。

`Paddle`类的附加属性是`m_direction`和`m_type`。第一个是一个`enum`类，用于保持当前方向，以桨正航向。第二个区别于其他桨。我们将需要这个来跟踪我们正在处理的事件。

```
enum class Type {LEFT, RIGHT};
enum class Direction {NONE, UP, DOWN}; 
```

Enter fullscreen mode Exit fullscreen mode

我们用来移动拨片的逻辑如下:

```
void Paddle::handle_input(SDL_Event const &event)
{
    switch(event.type)
    {
        case SDL_KEYDOWN:
            Uint8 const *keys = SDL_GetKeyboardState(nullptr);

            if(m_type == Type::LEFT)
            {
                if(keys[SDL_SCANCODE_W])
                {
                    m_direction = Direction::UP;
                }
                else if(keys[SDL_SCANCODE_S])
                {
                    m_direction = Direction::DOWN;
                }
            } else if(m_type == Type::RIGHT)
            {
                if(keys[SDL_SCANCODE_UP])
                {
                    m_direction = Direction::UP;
                }
                else if(keys[SDL_SCANCODE_DOWN])
                {
                    m_direction = Direction::DOWN;
                }
            }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于更新功能:

```
void Paddle::update(double delta_time)
{
    if(m_direction == Direction::UP)
    {
        m_y = m_y - 5.0 * delta_time;
        m_position.y = m_y;
    }
    else if(m_direction == Direction::DOWN)
    {
        m_y = m_y + 5.0 * delta_time;
        m_position.y = m_y;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，绘图功能:

```
void Paddle::draw(SDL_Renderer *renderer)
{
    SDL_SetRenderDrawColor(renderer, 255, 255, 255, 255);
    SDL_RenderFillRect(renderer, &m_position);
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
} 
```

Enter fullscreen mode Exit fullscreen mode

SDL2 允许绘制像矩形这样的图元。我们在这种情况下使用填充的矩形，但是，如果你需要一个矩形的轮廓，使用`SDL_RenderDrawRect()`。

编译并运行。

[![](../Images/955f80d28e3c4cec920c1c78bb4d4275.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TVrLXeHa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/madjwskza2hixq0o6t3l.png)

## 接下来是什么

在下一篇文章中，我们将把球显示在屏幕上，也许还会让碰撞检测工作起来。

Github 库:[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)