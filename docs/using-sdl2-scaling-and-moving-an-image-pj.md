# 使用 SDL2:移动图像

> 原文：<https://dev.to/noah11012/using-sdl2-scaling-and-moving-an-image-pj>

在开始下一部分之前，我们需要稍微讨论一下 SDL2 中的资源管理。如您所知，SDL2 是一个 C 库。这意味着它严重依赖指针和传递指针。通常在 C 语言中，当一个函数返回一个指针时，通常意味着有动态分配的东西，指向它的指针被返回给你。c #中没有自动清理功能，所有的事情都是手动完成的，包括资源管理。

你的观点是什么？

我想说的是，我们一直在泄漏内存。

***喘息！*什么？！你从来没有告诉过我们这些？你怎么能这样！**

我知道，我知道。我们没有释放资源，因为程序是短命的，它会弄乱代码。此外，从技术上讲，我们并没有泄漏内存，因为它在我们的窗口关闭后被操作系统收回了。

抱歉我反应过度了。

接受道歉。现在结束了，让我们先做一些清理工作。

我们不能把所有东西都放在`main()`函数中，所以我们必须把代码分成不同的部分。不过，你可以使用函数，因为我用的是 C++，所以我会使用类。

我将创建一个名为`Application`的类，目前有两个方法:

1.  `update()`
2.  `draw()`

方法`update()`将处理任何事件并绘制到后台缓冲区。
`draw()`会简单的调用`SDL_UpdateWindowSurface()`来显示后台缓冲。

main.cpp:

```
#include <iostream>
#include "application.hpp" 
int main()
{
    Application app;

    app.update();
    app.draw();
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错不是吗？所有真正的工作现在都隐藏在`Application`类后面，留下我们的`main()`函数被整理。

Application.hpp:

```
#pragma once 
#include <SDL2/SDL.h>
#include <iostream> 
class Application
{
public:
    Application();
    ~Application();

    void update();
    void draw();
private:
    SDL_Window  *m_window;
    SDL_Surface *m_window_surface;
    SDL_Event    m_window_event;
}; 
```

Enter fullscreen mode Exit fullscreen mode

Application.cpp:

```
#include "application.hpp" 
Application::Application()
{
    m_window = SDL_CreateWindow("SDL2 Window",
                                SDL_WINDOWPOS_CENTERED,
                                SDL_WINDOWPOS_CENTERED,
                                680, 480,
                                0);

    if(!m_window)
    {
        std::cout << "Failed to create window\n";
        std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
        return;
    }

    m_window_surface = SDL_GetWindowSurface(m_window);

    if(!m_window_surface)
    {
        std::cout << "Failed to get window's surface\n";
        std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
        return;
    }
}

Application::~Application()
{
    SDL_FreeSurface(m_window_surface);
    SDL_DestroyWindow(m_window);
}

void Application::update()
{
    bool keep_window_open = true;
    while(keep_window_open)
    {
        while(SDL_PollEvent(&m_window_event) > 0)
        {
            switch(m_window_event.type)
            {
                case SDL_QUIT:
                    keep_window_open = false;
                    break;
            }
        }

        draw();
    }
}

void Application::draw()
{
    SDL_UpdateWindowSurface(m_window);
} 
```

Enter fullscreen mode Exit fullscreen mode

和上次差不多，只是现在分成了两种方法。`update()`检查是否有任何事件需要处理，如果没有/如果所有事件都已处理，调用`draw()`。

我想让你们注意的是这个类的解构者:

```
Application::~Application()
{
    SDL_FreeSurface(m_window_surface);
    SDL_DestroyWindow(m_window);
} 
```

Enter fullscreen mode Exit fullscreen mode

这次我们使用`SDL_FreeSurface()`和`SDL_DestroyWindow()`来释放资源。

希望这种设计有助于可维护性。

## SDL_Rect

上一次当我们在窗口表面上 blitting 一个图像时，我们使用了`SDL_BlitSurface()`，我们可以传入的四个参数中的两个是指向`SDL_Rect` s 的指针。我们将利用`SDL_Rect`来定位和缩放我们的图像。

首先，我们需要一张图片来测试。我在我最喜欢的图像处理程序 GIMP 中创建了一个小简笔画。正如你很快就会看到的，无论从哪方面来说，我都不是艺术家。

我决定创建一个名为`load_surface()`的助手函数来加载 BMP 图像。我把它放在 Application.cpp 文件中，但是理想情况下，您应该为您的助手函数创建一个文件，除非它们与类密切相关，在这种情况下，它们不是。

```
SDL_Surface *load_surface(char const *path)
{
    SDL_Surface *image_surface = SDL_LoadBMP(path);

    if(!image_surface)
        return 0;

    return image_surface;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将添加另外两个成员变量到我们的类中，分别叫做`m_image`和`m_image_position`。

```
private:
    SDL_Surface *m_image;
    SDL_Rect     m_image_position;

    SDL_Window  *m_window;
    SDL_Surface *m_window_surface;
    SDL_Event    m_window_event; 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在开发一个合适的应用程序，你最有可能(如果你正在使用 C++)把`SDL_Surface`和`SDL_Rect`包装在一起，并把这个新类放到一个更合理的地方，比如一个用于保存窗口内容的`Window`类。我们只是制作一个程序来展示 SDL2 中的一个特定功能，所以这并不重要。

接下来，我们像上次一样加载图像并显示在屏幕上。我使用的图像是 22 x 43。

在构造函数中，我们添加了下面一行代码:

```
m_image = load_surface("stick_figure.bmp"); 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我们将跳过错误检查。

在`draw()`方法中，我添加了将像素复制到屏幕上的代码行。

```
SDL_BlitSurface(m_image, NULL, m_window_surface, NULL); 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行。

[![](img/ebdafb05b0f6a0f0686544e312616d13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oA-u8_KP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6tseuhr96dhg0whv9s6y.png)

我们的简笔画在窗口的左上角。现在让我们制作他向右走的动画。

好的，那么我们如何做到这一点呢？

还记得`SDL_Rect`吗？这就是我们要用来移动图像的东西。

一个`SDL_Rect`由四个成员组成:

1.  一个 x 位置
2.  y 位置
3.  宽度
4.  一个高度

在 blit 图像之前，我们可以创建一个`SDL_Rect`，改变它的一些成员字段，然后将它传递给`SDL_BlitSurface()`函数。*

*好吧，我又没有对你完全坦诚。`SDL_BlitSurface()`实际上是扩展到`SDL_UpperBlit()`的宏。他们为什么这样做？我不确定。

在`Application`类中，我们添加了一个名为`m_image_position`的成员。这将是控制图像如何定位和缩放的`SDL_Rect`。

在构造函数中，我们添加代码将`m_image_position`的成员变量设置为适当的值。

```
m_image_position.x = 0;
m_image_position.y = 0;
m_image_position.w = 22;
m_image_position.h = 43; 
```

Enter fullscreen mode Exit fullscreen mode

**挺住！这是一个糟糕的设计！并非所有图像都是 22 x 43。需要更有活力的东西！**

同样，我试图在不牺牲太多好的实践的情况下尽可能保持代码简单。现在，只要替换你的图像的尺寸。

```
SDL_BlitSurface(m_image, NULL, m_window_surface, &m_image_position); 
```

Enter fullscreen mode Exit fullscreen mode

`SDL_BlitSurface()`中的最后一个参数是一个指向`SDL_Rect`的指针，它决定了图像将被复制到窗口的哪个区域以及如何缩放。

编译并运行。

有些和上次一样。

我们可以开始看动画了吗？

是的，现在一切都准备好了，我们现在可以制作图像动画了。

在`update()`方法中，我们将每一帧增加图像的 x 位置 1。

```
m_image_position.x += 1; 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行。

**哇。那个简笔画飞过去了。**

对你们中的一些人来说，你们甚至可能看不到简笔画。这是因为您的计算机正在尽最大努力将图像移过窗口。

好吧，有道理。我们如何解决它？

很简单，我们需要根据自上一帧以来经过的时间来缩放图像的增量。

**那么，我们如何在 SDL2 中做到这一点呢？**

没有`SDL_AmountOfTimeThatHasPassedSinceTheLastFrame()` *功能。我们得想出点什么来。

*你能想象输入那么长的函数名吗？

但是在我们做任何事情之前，我们必须做一些重构。不是每个人都喜欢吗？

我们将引入一个名为`loop()`的新方法，现在`update()`将接受一个`double`。

在 main.cpp 中:

```
Application app;

app.loop(); 
```

Enter fullscreen mode Exit fullscreen mode

在应用程序中。hpp:

```
void loop();
void update(double delta_time); 
```

Enter fullscreen mode Exit fullscreen mode

使用我们类中的这个新方法，我们可以将事件处理循环移入其中，现在`update()`只有一个作业。

现在，我通过将`1.0/60.0`或大约`0.01667`传递给`update()`，将帧速率限制在每秒 60 帧。

```
void Application::loop()
{
    bool keep_window_open = true;
    while(keep_window_open)
    {
        while(SDL_PollEvent(&m_window_event) > 0)
        {
            switch(m_window_event.type)
            {
                case SDL_QUIT:
                    keep_window_open = false;
                    break;
            }
        }

        update(1.0/60.0);
        draw();
    }
}

void Application::update(double delta_time)
{
    m_image_position.x = m_image_position.x + (1 * delta_time);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还有一个问题。

这次又是什么？

如果我们手动计算简笔画每帧移动多少，并将结果放入`m_image_position.x`中，我们得到的值为零。为什么？它与 C++如何使用相同的操作符处理不同的类型有关。C++只在使用操作符时使用相同的类型，因此操作数必须给出其中之一。这就是所谓的推广规则*。例如，如果一个操作数是一个`double`，那么其他操作数将被转换为一个`double`。

*至少我是这么叫的。

于是，`(1 * delta_time)`转换成了`(1.0 * delta_time)`。
在计算子表达式之后，计算表达式`m_image_position.x + result_of_subexpression`。

因为`m_image_position.x`是一个`int`，而`result_of_subexpression`是一个`double`，原因我们在前面已经看到了，`m_image_position.x`中的值将被提升为`double`。

请记住，我通过将`1.0/60.0`或大约`0.01667`传递给`update()`，将帧速率限制为 60 fps。当我们计算表达式`m_image_position.x + (1 * 0.01667)`时，我们得到`0.01667`。我会带你走过这些步骤。如果`m_image_position.x`从 0 开始:

`0 + (1 + 0.01667)`
`0 + (1.0 + 0.01667`
`0 + 0.01667`

**那么，为什么简笔画每帧不移动大约`0.01667`个像素呢？**

简单:当你有一个类型为`int`的变量，你试图把类型为`double`的变量放入其中时，小数部分被截断，只有整数被放入值中。

当我们把`0.01667`放入`m_image_position.x`时，它被转换成一个`int`，因为`m_image_position.x`是一个`int`。

实际上，我们每秒钟移动零个像素。

有什么解决办法吗？

没错。我们向类中添加了两个新的类型为`double`的变量。

```
double       m_image_x;
double       m_image_y; 
```

Enter fullscreen mode Exit fullscreen mode

呃，我们很快就要重构这个了。这越来越失控了。

在构造函数中，我们将两者都初始化为`0.0`。

```
m_image_x = 0.0;
m_image_y = 0.0; 
```

Enter fullscreen mode Exit fullscreen mode

```
void Application::update(double delta_time)
{
    m_image_x = m_image_x + (5 * delta_time);
    m_image_position.x = m_image_x;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个实现中，`m_image_x`保持计算的小数部分。

编译并运行。

[![](img/b105a41892afb89b03cd684d9e1cf647.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1ILgJ1ok--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qvxwotv9h51hm2kmkant.png)

现在事情变得有些奇怪了。

这是因为我们在绘图之前没有清空屏幕，而是将先前绘制的项目留在了屏幕上。

简单的解决方法是在块传输之前的`draw()`中添加这行代码:

```
SDL_FillRect(m_window_surface, NULL, SDL_MapRGB(m_window_surface->format, 0, 0, 0)); 
```

Enter fullscreen mode Exit fullscreen mode

我知道我们还没有谈到很多新东西，但是让我们慢慢来。

用我们想要的任何颜色填充表面上的矩形区域。如果我们愿意，我们可以选择填充表面的一个区域。最后一个参数是我们希望填充区域的颜色。它是一个 32 位宽或 4 字节的`Uint32`或简单的无符号`int`。幸运的是，SDL2 提供了一个名为`SDL_MapRGB()`的函数，它有四个参数。

1.  曲面的格式。
2.  红色值
3.  绿色价值
4.  蓝色价值

我理解最后三个论点，但第一个是什么？

在`SDL_Surface`中有一个成员变量，它是一个指向`SDL_PixelFormat`的指针。它包含诸如每像素位数、像素每个组成部分的掩码等信息，并且有一个名为`format`的成员。它的类型是`SDL_PixelFormatEnum`，这是 SDL2 可以处理的所有格式。如果您查看文档，您会在一些值中看到一个熟悉的缩写词，如`SDL_PIXELFORMAT_RGB332`和一些您可能不熟悉的值，如`SDL_PIXELFORMAT_YVYU`。当我们向`SDL_MapRGB()`提供格式时，我们让它知道我们想要使用的格式版本。

编译并运行。

[![](img/fe4316d416a47d1a2878a4594ed1672d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cLA3WUwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ivweilu39ah96aw9wov1.png)

现在我们的简笔画在屏幕上移动，没有任何怪异之处。

## 接下来是什么？

在下一篇文章中，我们将获得对简笔画的控制，并按照我们喜欢的方式移动它。

这一部分可能是我迄今为止所写的文章中包含的更多信息。如果你有任何问题，我很乐意回答。

这个系列的所有代码都可以在我的 Github 仓库中找到:[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)