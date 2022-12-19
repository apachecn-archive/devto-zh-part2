# 使用 SDL2:控制我们的简笔画

> 原文：<https://dev.to/noah11012/using-sdl2-4of8>

上一次，我们的简笔画朋友从左边移到了右边。这一次，我们将通过使用键盘来控制图像的移动。

在我发表这篇文章之前，我做了一些重构，包括为我们的图像创建一个名为`StickFigure`的新类，将新类放在`Application`中，并用`nullptr`替换所有的`NULL`实例。从 C++ 11 开始，它就伴随着语言，基本上，它是一个指针文字。

这是我修改过的所有文件:

Application.hpp:

```
class Application
{
public:
    Application();
    ~Application();

    void loop();
    void update(double delta_time);
    void draw();
private:
    StickFigure m_stick_figure;

    SDL_Window  *m_window;
    SDL_Surface *m_window_surface;
    SDL_Event    m_window_event;
}; 
```

Enter fullscreen mode Exit fullscreen mode

Application.cpp:

```
void Application::update(double delta_time)
{
    m_stick_figure.update(delta_time);
}

void Application::draw()
{
    SDL_FillRect(m_window_surface, nullptr, SDL_MapRGB(m_window_surface->format, 0, 0, 0));

    m_stick_figure.draw(m_window_surface);

    SDL_UpdateWindowSurface(m_window);
} 
```

Enter fullscreen mode Exit fullscreen mode

stick_figure.hpp:

```
#pragma once 
#include <SDL2/SDL.h> 
class StickFigure
{
public:
    StickFigure();
    ~StickFigure() = default;

    void update(double delta_time);
    void draw(SDL_Surface *window_surface);
private:
    SDL_Surface *m_image;
    SDL_Rect     m_position;
    double       m_x;
    double       m_y;
}; 
```

Enter fullscreen mode Exit fullscreen mode

stick_figure.cpp:

```
#include "stick_figure.hpp" 
StickFigure::StickFigure()
{
    m_image = SDL_LoadBMP("stick_figure.bmp");

    m_position.x = 0;
    m_position.y = 0;
    m_position.w = 22;
    m_position.h = 43;

    m_x = 0.0;
    m_y = 0.0;
}

void StickFigure::update(double delta_time)
{
    m_x = m_x + (5.0 * delta_time);
    m_position.x = m_x;
}

void StickFigure::draw(SDL_Surface *window_surface)
{
    SDL_BlitSurface(m_image, nullptr, window_surface, &m_position);
} 
```

Enter fullscreen mode Exit fullscreen mode

我想让你注意的一件事是，`Application`中的`update()`和`draw()`不会更新简笔画的位置，也不会直接将其绘制到屏幕上。那现在是`m_stick_figure`的责任了。相反，我们在类似于`m_stick_figure`的`StickFigure`对象上调用`update()`和`draw()`。这样，每个班级现在负责自己的管理。

## 键盘输入

我们想使用传统的 WASD 键来移动简笔画。我们需要知道按了什么按钮才能让它工作。SDL2 为我们提供了一个返回键数组的函数，有点像键盘的快照。

那么，这个函数的名字是什么呢？

它叫做`SDL_GetKeyboardState()`，它有一个指向`int`的指针，如果不为空，它给出键盘上的键数。我们不会使用这个，所以我们将传入`nullptr`。

这个函数返回一个常量数组`Uint8`或无符号数组`char`。在我们使用这个函数之前，我们将为`StickFigure`添加一个名为`handle_events()`的函数，来处理`Application`类可能遇到的任何输入。

```
void handle_events(SDL_Event const &event); 
```

Enter fullscreen mode Exit fullscreen mode

除了我们的新方法之外，我将添加一个枚举类来保存这个图像可以去的不同方向。

```
enum class Direction
{
    NONE,
    UP,
    DOWN,
    LEFT,
    RIGHT
};

...

private:
    Direction    m_direction; 
```

Enter fullscreen mode Exit fullscreen mode

**什么是枚举类？**

我不会解释它，但你可以查看这个 [Stackoverflow 帖子](https://stackoverflow.com/questions/18335861/why-is-enum-class-preferred-over-plain-enum)。

我们可以通过使用一个`SDL_Scancode`枚举来索引 SDL 返回给我们的这个数组。如果其中一个索引为 1，则按下，否则为 0。

在`handle_update()`方法中，我们检查一个键是否被按下，如果是，是哪一个。然后我们相应地设定方向。

```
switch(event.type)
{
    case SDL_KEYDOWN:
        Uint8 const *keys = SDL_GetKeyboardState(nullptr);

        if(keys[SDL_SCANCODE_W] == 1)
            m_direction = Direction::UP;
        else if(keys[SDL_SCANCODE_S] == 1)
            m_direction = Direction::DOWN;
        else if(keys[SDL_SCANCODE_A] == 1)
            m_direction = Direction::LEFT;
        else if(keys[SDL_SCANCODE_D] == 1)
            m_direction = Direction::RIGHT;
        break;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`Application`类的`loop()`方法中，我们将`m_window_event`传递给`StickFigure::hande_input()`

```
while(SDL_PollEvent(&m_window_event) > 0)
{
    m_stick_figure.handle_events(m_window_event);
    switch(m_window_event.type)
    {
        case SDL_QUIT:
            keep_window_open = false;
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

类`StickFigure`中的方法`update()`根据`m_direction`设置的值来移动图像。

```
switch(m_direction)
    {
        case Direction::NONE:
            m_x += 0.0;
            m_y += 0.0;
            break;
        case Direction::UP:
            m_y = m_y - (5.0 * delta_time);
            break;
        case Direction::DOWN:
            m_y = m_y + (5.0 * delta_time);
            break;
        case Direction::LEFT:
            m_x = m_x - (5.0 * delta_time);
            break;
        case Direction::RIGHT:
            m_x = m_x + (5.0 * delta_time);
            break;
    }

    m_position.x = m_x;
    m_position.y = m_y; 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行。

[![](../Images/66313cf461ee21b10db0a55749f9e938.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cxCIx7OB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1h4l4emi2plp4bww2c7y.png)

我希望这是更多的使用键盘控制角色的文章。

在我离开之前，我想提出的另一件事是关于拥有良好的文档。我不会教你关于 SDL2 的所有内容，也不会教你 SDL2 中特定主题的所有内容。如果您想进一步了解 SDL2 中的某个主题，您需要好的文档。

在 [SDL2 Wiki](https://wiki.libsdl.org/) 网站上，您可以找到我自 GD50*以来一直使用的文档。

*GD50 是游戏开发 50 的缩写，是哈佛大学关于 edx.org 的免费在线课程。如果你想学习更多关于游戏开发的知识，这是我推荐的课程。

本系列的所有代码都可以在我的 Github 资源库中找到:

[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)

## 接下来是什么？

亲爱的读者，这是一个惊喜。