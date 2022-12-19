# 使用 SDL2:优化表面块传输

> 原文：<https://dev.to/noah11012/using-sdl2-optimizing-surface-blitting-38f8>

这是一篇关于如何优化你的 blitting 和如何在 SDL2 中打开类似 png 的图片的文章。这不是一个有趣的帖子，但却是一个重要的帖子。

## 问题

大多数位图是 24 位的，现代屏幕通常不是 24 位的。如果我们试图将一个 24 位的图像 blit 到一个非 24 位的表面上，那么每次我们 blit 它时，SDL2 都会进行转换。这是一个我们可以通过首先转换成屏幕格式来加快位传输过程的区域。SDL2 提供了这样一个叫做`SDL_ConvertSurface()`的函数。它需要一个你想要转换的表面和你想要转换的格式。该函数接受第三个参数，但这是 SDL 1.2 天的参数，应该始终设置为 0。

```
SDL_Surface *load_bmp(char const *path, SDL_Surface *target_surface)
{
    SDL_Surface *optimized_version = nullptr;
    SDL_Surface *image_surface = SDL_LoadBMP(path);

    if(!image_surface)
        return 0;

    optimized_version = SDL_ConvertSurface(image_surface, image_surface->format, 0);

    if(!optimized_version)
    {
        SDL_FreeSurface(image_surface);
        return 0;
    }

    SDL_FreeSurface(image_surface);

    return optimized_version;
} 
```

Enter fullscreen mode Exit fullscreen mode

每个`SDL_Surface`实例都有一个名为`format`的成员变量，它保存了一个曲面正在使用的格式类型。

## 加载不同的图像类型

除了 BMP 之外，SDL2 本身不能加载图像。SDL2 有一个叫做扩展库的东西，可以扩展 SDL2 的功能。

我们将使用的扩展库叫做 SDL2_image。在 Windows 上，您必须搜索如何设置 SDL2_image，但是在 Linux 发行版(如 Ubuntu 及其衍生版本，如 Linux Mint)上，您可以使用以下命令:

```
sudo apt-get install libsdl2-image-dev 
```

Enter fullscreen mode Exit fullscreen mode

其他用 Manjaro 之类的可以用这个:

```
sudo pacman -S sdl2_image 
```

Enter fullscreen mode Exit fullscreen mode

像 SDL2 一样，我们必须在使用它之前初始化这个库。初始化函数为我们想要使用的图像类型提供一个参数。

如果只需要一个参数，这怎么可能呢？

很简单，我们只是把旗子放在一起。

```
int flags = IMG_INIT_PNG | IMG_INIT_JPEG; 
```

Enter fullscreen mode Exit fullscreen mode

`IMG_Init()`返回已启动的图像加载程序的位掩码。由于 JPEG 加载器发生了内部错误，我们可能只能使用 png 而不能使用 JPEG。我们需要将这个位图与变量`flags`进行 AND 运算，并检查它是否等于`flags`。如果没有，那么不是所有的图像加载器都被正确启动。

```
int flags = IMG_INIT_PNG | IMG_INIT_JPEG;
int initiated_flags = IMG_Init(flags);

if((initiated_flags & flags) != flags))
{
     std::cout << "Failed to initialize all image loaders\n";
     std::cout << "IMG Error: " << IMG_GetError() << "\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

如果出现错误，我们可以使用`IMG_GetError()`来获取最新的错误消息。

现在 SDL2_image 已经初始化，我们可以使用`IMG_Load()`打开任何具有初始化时指定的相同格式的图像。

```
SDL_Surface *image_surface = IMG_Load("image.png");

if(!image_surface)
{
    std::cout << "Failed to open image\n";
    std::cout << "IMG Error: " << IMG_GetError() << "\n";
}

// use image_surface

SDL_FreeSurface(image_surface); 
```

Enter fullscreen mode Exit fullscreen mode

当你用完后，记得在返回的图像上使用`SDL_FreeSurface()`。

有了这种打开更广泛图像的新能力，我们不再局限于使用 BMP。

我很高兴。我厌倦了将 png 转换成 BMP。

SDL2_image 的文档可在此处找到:

[https://www . libsdl . org/projects/SDL _ image/docs/SDL _ image _ frame . html](https://www.libsdl.org/projects/SDL_image/docs/SDL_image_frame.html)

## 接下来是什么

在下一部分，我们将开始使用硬件将图像渲染到屏幕上。

所有源代码都可以在我的 Github 资源库中找到:

[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)