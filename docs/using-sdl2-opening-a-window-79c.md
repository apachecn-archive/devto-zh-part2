# 使用 SDL2:打开窗口

> 原文：<https://dev.to/noah11012/using-sdl2-opening-a-window-79c>

在这一部分，我们将打开并运行一个窗口。首先，创建一个新的 C++文件，想怎么叫就怎么叫。没关系，不过我会叫我的`main.cpp`。

复制并粘贴以下代码:

```
#include <SDL2/SDL.h>
#include <iostream> 
int main()
{
    if(SDL_Init(SDL_INIT_VIDEO) < 0)
    {
        std::cout << "Failed to initialize the SDL2 library\n";
        return -1;
    }

    SDL_Window *window = SDL_CreateWindow("SDL2 Window",
                                          SDL_WINDOWPOS_CENTERED,
                                          SDL_WINDOWPOS_CENTERED,
                                          680, 480,
                                          0);

    if(!window)
    {
        std::cout << "Failed to create window\n";
        return -1;
    }

    SDL_Surface *window_surface = SDL_GetWindowSurface(window);

    if(!window_surface)
    {
        std::cout << "Failed to get the surface from the window\n";
        return -1;
    }

    SDL_UpdateWindowSurface(window);

    SDL_Delay(5000);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经正确配置了你的 IDE，比如 Visual Studio，那么点击“运行”按钮。如果你像我一样用终端，可以用 g++或者 clang++。

`g++ -o sdl2-program main.cpp `sdl2-config --cflags --libs`

`clang++ -o sdl2-program main.cpp `sdl2-config --cflags --libs`

编译并运行它。您将看到一个没有响应的黑色窗口弹出，并将在 5000 毫秒或 5 秒后关闭。

[![](img/4c75e87996311b59f4ed1236e42dc19d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--om-6O_5d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06oc79wbr8hhopf6072t.png)

现在让我们浏览一下代码，看看发生了什么。

`if(SDL_Init(SDL_INIT_VIDEO) < 0)`

在我们可以使用它提供的任何功能之前，需要首先初始化 SDL2。SDL2 分为称为子系统的几个部分。现在，我们只需要视频支持。如果您想要初始化多个子系统，或者将不同的标志放在一起:

`SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO)`

成功时返回 0，失败时返回负数。我们检查这个函数是否失败。如果是，打印到控制台让用户知道。

```
{
        std::cout << "Failed to initialize the SDL2 library\n";
        std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
        return -1;
} 
```

Enter fullscreen mode Exit fullscreen mode

`SDL_GetError()`获取失败的 SDL 函数设置的错误信息。

接下来，我们创建实际的窗口。

```
SDL_Window *window = SDL_CreateWindow("SDL2 Window",
                                          SDL_WINDOWPOS_CENTERED,
                                          SDL_WINDOWPOS_CENTERED,
                                          680, 480,
                                          0); 
```

Enter fullscreen mode Exit fullscreen mode

`SDL_CreateWindow()`需要几个参数:

1.  窗口的标题
2.  窗口的 x 位置
3.  窗口的 y 位置
4.  窗口的宽度
5.  窗口的高度

如果成功，它返回一个指向`SDL_Window`的指针；如果失败，则返回一个指向`NULL`的指针。

我们检查窗口是否失败，如果是，让用户知道:

```
if(!window)
{
    std::cout << "Failed to create window\n";
    std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
    return -1;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到了窗户的表面。一个`SDL_Surface`是一个`struct`,它包含一组用于软件位块传送的像素。当我们在屏幕上得到一幅图像时，我们将会更深入地了解什么是位块传送，但可以说，位块传送是将一幅图像从一个表面复制到另一个表面的过程。

```
SDL_Surface *window_surface = SDL_GetWindowSurface(window);

if(!window_surface)
{
    std::cout << "Failed to get the surface from the window\n";
    std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
    return -1;
} 
```

Enter fullscreen mode Exit fullscreen mode

你现在知道该怎么做了。

最后，我们通过调用`SDL_UpdateWindowSurface()`来更新窗口的表面。该函数采用一个指向`SDL_Window`的指针，如果成功则返回 0，如果失败则返回负数。这里我们不会检查是否有问题。

然后我们调用`SDL_Delay()`函数。如果我们没有它，我们就看不到窗口，因为它会被创建并迅速关闭。我们暂停 5 秒钟，看看是否一切正常。

好吧，诺亚，这很好，但这不是很实用。我们如何保持窗口打开直到用户关闭它？

好问题。在我回答这个问题之前，我必须解释一下什么是*事件*。

事件是让我们知道用户是否做了什么的东西。用户是否移动了鼠标、输入了关键字、移动了控制器上的操纵杆等等。

事件以**队列**的形式到达我们这里。队列是一种遵循先入先出或 FIFO 原则的数据结构。想到一行人。第一个入队的人第一个退队。事件队列也是如此。首先被触发的事件是最先被处理的。

SDL2 中的事件存储在一个名为`SDL_Event`的联合中。如果你不知道什么是联合，它就像是一个`struct`,除了一次只有一个成员存储数据。

我们可以通过**向**查询`SDL_PollEvent()`的事件。轮询意味着从队列中检索事件。`SDL_PollEvent()`获取一个指向`SDL_Event`的指针，如果有未决事件，则返回 1，如果队列中没有事件，则返回 0。

我们可以使用一个`while`循环来连续检查一个事件是否需要被处理。

```
int main()
{
    if(SDL_Init(SDL_INIT_VIDEO) < 0)
    {
        std::cout << "Failed to initialize the SDL2 library\n";
        std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
        return -1;
    }

    SDL_Window *window = SDL_CreateWindow("SDL2 Window",
                                          SDL_WINDOWPOS_CENTERED,
                                          SDL_WINDOWPOS_CENTERED,
                                          680, 480,
                                          0);

    if(!window)
    {
        std::cout << "Failed to create window\n";
        std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
        return -1;
    }

    SDL_Surface *window_surface = SDL_GetWindowSurface(window);

    if(!window_surface)
    {
        std::cout << "Failed to get the surface from the window\n";
        std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
        return -1;
    }

    SDL_Event e;
    while(SDL_PollEvent(&e) > 0)
    {
        SDL_UpdateWindowSurface(window);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行。

现在它很快出现又消失了。

**嘿！我以为我们会有一个窗口停留在屏幕上！你骗了我们！**

哇哦。在你开始向我扔西红柿之前，我需要你记住一些事情:`SDL_PollEvent()`如果*队列中没有事件*，则返回 0。`while`循环条件中的 0 表示停止执行。这仅仅意味着没有事件需要处理。对此有一个简单的解决方法:用另一个 while 循环包围事件处理`while`循环:

```
bool keep_window_open = true;
while(keep_window_open)
{
    SDL_Event e;
    while(SDL_PollEvent(&e) > 0)
    {
        SDL_UpdateWindowSurface(window);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行。

好的，太好了。现在我关不上窗户了。我们如何解决这个问题？

你不能关闭窗口的原因是我们没有处理窗口关闭事件。

**为什么 SDL2 不默认这样做？**

好吧，想想这个场景:假设你已经创建了一个游戏，你想让它设置成如果用户点击 X 按钮，那么会有一个对话框提示他们在退出游戏之前保存他们所取得的任何进展。如果 SDL2 没有赋予我们处理窗口关闭事件的能力，那么您就无法实现这样的功能。

我将使用一个`switch`而不是一串`if` / `if else`语句，因为它看起来更好，尤其是当我们开始处理更多的事件时。

```
bool keep_window_open = true;
while(keep_window_open)
{
    SDL_Event e;
    while(SDL_PollEvent(&e) > 0)
    {
        switch(e.type)
        {
            case SDL_QUIT:
                keep_window_open = false;
                break;
        }

        SDL_UpdateWindowSurface(window);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在一个`SDL_Event`工会中，我们有一个`type`成员。它的目的是存储它当前持有的事件类型。

编译并运行。

现在一切都按预期运行。

在我们结束这一部分之前，让我们把一幅图像放到屏幕上。你需要一个位图图像来工作。位图图像的扩展名为`.bmp`。下载一个或创建一个，然后我们就可以开始了。

```
SDL_Surface *image = SDL_LoadBMP("image.bmp");

if(!image)
{
    std::cout << "Failed to load image\n";
    std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
    return -1;
}

bool keep_window_open = true;
while(keep_window_open)
{
    SDL_Event e;
    while(SDL_PollEvent(&e) > 0)
    {
        switch(e.type)
        {
            case SDL_QUIT:
                keep_window_open = false;
                break;
        }

        SDL_BlitSurface(image, NULL, window_surface, NULL);
        SDL_UpdateWindowSurface(window);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/91923a7e64d97c138b2982f31f486341.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HSuvDu-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/amkaenduil98xs6abdir.png)

我们使用`SDL_LoadBMP()`加载一个位图图像，检查它是否失败，如果成功，使用`SDL_BlitSurface()`将它传送到屏幕上。该函数有四个参数:

1.  我们要从中复制像素的源表面
2.  指向源表面的一个`SDL_Rect`的指针
3.  我们要将像素复制到的目标表面
4.  指向目标表面的一个`SDL_Rect`的指针

什么是`SDL_Rect`？它是一个`struct`来保存一个矩形的位置和尺寸。

我们到底为什么需要这个？

如果我们只想复制图像的一部分，我们可以使用一个`SDL_Rect`并将它放置在源表面上，提取我们想要的部分。如果我们想要定位和缩放源表面，我们可以使用一个`SDL_Rect`来覆盖目标表面到我们想要表面被复制到的地方。

现在，为源和目的地`SDL_Rect`传入 NULL，因为我们想要整个图像，并且我们想要拉伸图像以适合整个窗口。

我在 GIMP 中制作了我的位图，它的尺寸和窗口一样，背景是黑色的，这样看起来就像屏幕上有文字一样。如果你有一个不同尺寸的图像，它可能会模糊/压扁。

SDL2 使用一种叫做**的东西，双缓冲**。我们有两个缓冲器可用。第一个被称为后台缓冲区，是我们的画布:我们可以在上面画任何我们想画的东西，但是在我们调用`SDL_UpdateWindowSurface()`之前，它不会向外界显示。前端缓冲区是当前显示的内容。当您调用`SDL_UpdateWindowSurface()`时，它会获取后台和前台缓冲区并交换它们的位置。这使得前面是后面，后面是前面。

早些时候，我承诺我会更深入地了解 blitting 是什么。位块传输是一个术语，意思是**位边界块传输**。

嗯，天啊，诺亚。那真的很有帮助。

我知道，但是一旦你知道它是什么，它就有希望变得更有意义。

位边界块传输是将内存块(通常是像素)从一个源复制到另一个源的过程。

当我们加载位图并显示在窗口上时，我们将图像的表面块传输到窗口的表面上。请记住，表面是像素的集合。我们有效地复制了图像中的像素，并将其粘贴到窗口上。

在我们结束之前，我还有一件事要分享。虽然这些教程可能会有所帮助，但是有一个好的文档作为参考还是很重要的。

官方版本在 [SDL2 Wiki](https://wiki.libsdl.org/APIByCategory) 结束。

### 我们的下一步是什么？

接下来，我们将学习如何使用`SDL_Rect` s，因为它们在 SDL2 开发中很重要，我们将使用它们在窗口中移动图像。

本系列的所有源代码都可以在 Github 上查看和下载:

[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)