# 使用 SDL2: 2D 加速渲染

> 原文：<https://dev.to/noah11012/using-sdl2-2d-accelerated-renderering-1kcb>

嘶！您想知道如何让您的 SDL2 应用程序运行得更快吗？

**甜！听起来很棒。给我带路。**

SDL2 的发布增加了使用 GPU 的能力。一直以来，我们都在做软件渲染，也就是 CPU 做所有图形计算的地方。CPU 速度很快，但并未针对将图形输出到屏幕上进行必要的计算进行优化。

**我们如何使用带有 SDL2 的 GPU？**

为此我们必须使用一个`SDL_Renderer`。为了创建一个，我们使用了`SDL_CreateRenderer()`函数，它接受以下参数:

1.  一个指向`SDL_Window`的指针
2.  我们想要使用的渲染驱动程序的索引
3.  和 0 或更多的“或”运算在一起。

我得到了第一个和第三个，但是我不知道我的渲染驱动程序的索引。

如果您不想手动设置它，只需提供-1 并让 SDL2 为您完成这项工作。

我们将使用`SDL_RENDERER_ACCERELATED`渲染器标志来获得 2DS 加速图形。

```
SDL_Window *window = ...;

SDL_Renderer *window_renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED); 
```

Enter fullscreen mode Exit fullscreen mode

一个小提示:如果你也想使用窗口的渲染器，不要试图获取窗口的表面。如果你这样做，坏事就会发生。

我们得到了我们的渲染器。现在怎么办？

`SDL_Renderer`的工作原理是将像素复制到其中，然后调用`SDL_RenderPresent()`。

喜欢聊天吗？

精神上相似，是的。

好的，我知道了。首先，我将一幅图像加载到一个`SDL_Surface`中，然后复制它。

有一个问题。

**什么事？**

用于复制像素数据的函数是`SDL_RenderCopy()`，它需要一个`SDL_Texture`。

我知道什么是纹理。

SDL2 中的内容与您现在可能想到的内容并不完全相同。

**好的，那么`SDL_Surface`和`SDL_Texture`有什么不同呢？**

`SDL_Surface`是像素和一些额外信息的集合，如正在使用的格式，而`SDL_Texture`也是像素的集合，但存储在一个有效的和驱动程序特定的表示中。

**好的，有没有把一张图片加载到`SDL_Texture`的函数？**

号码

**那我们怎么用一个`SDL_Texture`？**

我们必须将它从一个`SDL_Surface`转换成一个`SDL_Texture`。

我们通过什么做到的？

通过使用带两个参数的`SDL_CreateTextureFromSurface()`:

1.  一个指向`SDL_Renderer`的指针
2.  和一个指向一个`SDL_Surface`的指针。

```
SDL_Surface *surface = ...;

SDL_Texture *texture = SDL_CreateTextureFromSurface(window_renderer, surface);

if(!texture)
{
    std::cout << "Failed to convert surface into a texture\n";
    std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
}

SDL_FreeSurface(surface); 
```

Enter fullscreen mode Exit fullscreen mode

当你完成转换成纹理的时候记得释放表面。当你使用完纹理后，确保在上面调用`SDL_DestroyTexture()`。

让我们把纹理渲染到屏幕上。

你是不是错过了什么？

不。我想我们已经有了所有需要渲染到屏幕上的东西。

我们需要先清空屏幕，傻瓜！

啊，我的错。

我们需要像你建议的那样清空屏幕。我们可以通过使用`SDL_RenderClear()`来做到这一点，它需要一个指向`SDL_Renderer`的参数。如果我们想改变`SDL_RenderClear()`用来清空窗口的颜色，我们可以调用`SDL_SetRenderDrawColor()`，它接受一个指向`SDL_Renderer`的指针和每个参数的红色、绿色、蓝色和 alpha 值。

默认情况下，它使用黑色。

```
SDL_RenderClear(window_renderer);

SDL_RenderCopy(window_renderer, texture, nullptr, nullptr);

SDL_RenderPresent(window_renderer); 
```

Enter fullscreen mode Exit fullscreen mode

`SDL_RenderCopy()`采用以下参数:

1.  指向 SDL 渲染器的指针
2.  指向 SDL 纹理的指针
3.  来源`SDL_Rect`
4.  而目的地`SDL_Rect`

现在你知道了！SDL2 中的 2D 加速图形。有关 2D 加速的更多信息，请参见文档的这一部分:

[https://wiki.libsdl.org/CategoryRender](https://wiki.libsdl.org/CategoryRender)

## 接下来是什么

我们有足够的 SDL2 知识，最终创造出自己的小规模游戏。我们将要制作的游戏是经典的街机游戏 Pong。这可能会是一个多部分的文章，第一个可能不会发布几天。所以，坚持住！