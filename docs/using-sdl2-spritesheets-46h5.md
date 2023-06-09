# 使用 SDL2: Spritesheets

> 原文：<https://dev.to/noah11012/using-sdl2-spritesheets-46h5>

让我们回顾一下到目前为止我们在这个系列中所取得的成就:

我们的旅程从简单开始。一个黑色的窗口出现在屏幕上，上面有我们选择的图像。然后，我们在屏幕上移动图像。最终我们厌倦了不能与程序互动，我们控制了我们的简笔画，并把它移动到任何我们想要的地方。

我认为下一部分是这个系列的逻辑步骤，那就是 sprite sheets。

我敢肯定，至少你们中的一些人知道什么是精灵表，但对于你们其余的人来说，它是在一个图像中收集的精灵的集合。这给了我们仅使用一个图像的优势，但是可以被视为多个单独的子图像。

我假设你们大多数人会像我一样从网上下载一张雪碧表。如果精灵表的提供者没有提供单个精灵的尺寸，你将不得不自己测量。

## 精灵表类

正如您可能已经预料到的，我们需要一个管理 sprite 工作表的类。我已经把它变得非常简单，完全符合我们的需求。

spritesheet.hpp:

```
#pragma once 
#include <SDL2/SDL.h>
#include "utilities.hpp" 
class Spritesheet
{
public:
    Spritesheet(char const *path, int row, int column);
    ~Spritesheet();

    void select_sprite(int x, int y);
    void draw_selected_sprite(SDL_Surface *window_surface, SDL_Rect *position);

private:
    SDL_Rect     m_clip;
    SDL_Surface *m_spritesheet_image;
}; 
```

Enter fullscreen mode Exit fullscreen mode

原理表. CPP:t0]

```
#include "spritesheet.hpp" 
Spritesheet::Spritesheet(char const *path, int row, int column)
{
    m_spritesheet_image = load_bmp(path);

    m_clip.w = m_spritesheet_image->w / column;
    m_clip.h = m_spritesheet_image->h / row;
}

Spritesheet::~Spritesheet()
{
    SDL_FreeSurface(m_spritesheet_image);
}

void Spritesheet::select_sprite(int x, int y)
{
    m_clip.x = x * m_clip.w;
    m_clip.y = y * m_clip.h;
}

void Spritesheet::draw_selected_sprite(SDL_Surface *window_surface, SDL_Rect *position)
{
    SDL_BlitSurface(m_spritesheet_image, &m_clip, window_surface, position);
} 
```

Enter fullscreen mode Exit fullscreen mode

`Spritesheet`类获取一个 sprite 工作表图像以及行和列的路径。在构造函数中，我们加载图像并计算 sprite 的宽度和高度。你的精灵表应该被平均分配。一个精灵的尺寸应该等于其余精灵的尺寸。我们可以通过将图像的宽度和高度分别除以行和列来计算精灵的尺寸。

在该方法中，`selection_sprite()`采用一个 x 偏移和一个 y 偏移。我们将 x 轴偏移量和 y 轴偏移量分别乘以子画面的宽度和高度。

绘制精灵是通过调用`draw_selected_sprite()`来完成的。它接受一个指向目标表面的`SDL_Surface`的指针和一个指向目标表面上 sprite 位置的`SDL_Rect`的指针。

## 动画

在`StickFigure`类中，我们将删除上次使用的`SDL_Surface`，并用新的`Spritesheet`类替换它。

```
private:
    Spritesheet  m_spritesheet; 
```

Enter fullscreen mode Exit fullscreen mode

现在在构造函数中，我们可以选择任何我们想要的精灵。我们将使用第一个精灵作为默认精灵。

```
m_spritesheet.select_sprite(0, 0); 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行。

[![](img/e69f760657ac4f10dfd28ce21542d9fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ra_9ARjd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uqoj79aa677lyx8pev2q.png)

哦，我做的另一个改变是用白色填充窗口，因为我用的简笔画是黑色的。

现在，让我们让动画工作。我在 stick_figure.cpp 文件中为行的偏移量定义了常数。

```
int const SPRITESHEET_UP = 0;
int const SPRITESHEET_LEFT = 1;
int const SPRITESHEET_RIGHT = 2;
int const SPRITESHEET_DOWN = 3; 
```

Enter fullscreen mode Exit fullscreen mode

这主要是为了可读性。

我们还在`StickFigure`类中添加了另一个变量，用于任何行的列的偏移量。

```
int          m_spritesheet_column; 
```

Enter fullscreen mode Exit fullscreen mode

现在在`update()`方法中，我们根据方向改变行。

```
switch(m_direction)
{
    case Direction::NONE:
        m_x += 0.0;
        m_y += 0.0;
        m_spritesheet.select_sprite(0, 0);
        break;
    case Direction::UP:
        m_y = m_y - (500.0 * delta_time);
        m_spritesheet.select_sprite(m_spritesheet_column, SPRITESHEET_UP);
        break;
    case Direction::DOWN:
        m_y = m_y + (500.0 * delta_time);
        m_spritesheet.select_sprite(m_spritesheet_column, SPRITESHEET_DOWN);
        break;
    case Direction::LEFT:
        m_x = m_x - (500.0 * delta_time);
        m_spritesheet.select_sprite(m_spritesheet_column, SPRITESHEET_LEFT);
        break;
    case Direction::RIGHT:
        m_x = m_x + (500.0 * delta_time);
        m_spritesheet.select_sprite(m_spritesheet_column, SPRITESHEET_RIGHT);
        break;
}

m_position.x = m_x;
m_position.y = m_y;

m_spritesheet_column++;

if(m_spritesheet_column > 8)
    m_spritesheet_column = 1; 
```

Enter fullscreen mode Exit fullscreen mode

我们还循环回到第二个图像，而不是第一个，因为这个精灵是如何制作的，这样会更好。你的雪碧可能不一样。

在`draw()`方法中，我们绘制精灵并延迟 100 毫秒或 1/10 秒。因为我们暂停了一小段时间，所以我们需要增加每秒移动的像素数。最初，它是 5 个像素，但现在我们是 500 个像素。

编译并运行。

[![](img/ea83d2ad53693c4bef1a50df1b24ebdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3jnJYBjV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zu7250bfimv6fpvszmuo.png)

如果你有任何问题，我很乐意回答。

## 接下来是什么

我们将学习如何打开不同的图像，如 PNGs，以及如何优化表面 blitting。

该系列的所有源代码都可以在我的 Github 资源库中找到:

[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)