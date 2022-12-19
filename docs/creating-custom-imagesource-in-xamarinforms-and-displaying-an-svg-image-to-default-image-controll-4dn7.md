# 在 Xamarin 中创建自定义 ImageSource。窗体，并向默认图像控件显示一个 SVG 图像

> 原文：<https://dev.to/muak_x/creating-custom-imagesource-in-xamarinforms-and-displaying-an-svg-image-to-default-image-controll-4dn7>

自从 Xamarin。Forms 2.3.5，您可以创建自己的 ImageSource。

这篇文章演示了如何使用自定义 ImageSource 向默认图像控件显示 SVG 图像。

## 储存库

[https://github.com/muak/SvgImageSource](https://github.com/muak/SvgImageSource)

## Nuget

[https://www.nuget.org/packages/Xamarin.Forms.Svg/](https://www.nuget.org/packages/Xamarin.Forms.Svg/)

## 添加自定义图像源的步骤

1.  在共享项目上，创建一个从 ImageSource 类派生的类。(例如 SomeImageSource)。
2.  在每个平台项目上，创建实现 IImageSourcehandler 的 SomeImageSourceHandler 类。这实际上是为了阅读图像所需要的。
3.  在 SomeImageSourceHandler 中实现 LoadImageAsync 方法，在这里写读取一个图像的过程。
4.  向 Xamarin 注册 SomeImageSource 和 SomeImageSourceHandler 类型。以便启用 SomeImageSource。

## 定义自定义图像源

我解释了为显示 SVG 图像创建 SvgImageSource 的例子。

使 StreamImageSouce 继承，因为获取 SVG 资源流的过程与其相同。在这里定义一些常用的 BindableProperty 和一些静态方法来创建 ImageSource。

我抄袭了 Xamarin 的。表单来源获取程序集的一部分。这似乎是从调用方法中获取程序集。

```
public class SvgImageSource : StreamImageSource, ISvgImageSource
{
    public static BindableProperty WidthProperty =
        BindableProperty.Create(
            nameof(Width),
            typeof(double),
            typeof(SvgImageSource),
            default(double),
            defaultBindingMode: BindingMode.OneWay
        );

    public double Width {
        get { return (double)GetValue(WidthProperty); }
        set { SetValue(WidthProperty, value); }
    }

    public static BindableProperty HeightProperty =
        BindableProperty.Create(
            nameof(Height),
            typeof(double),
            typeof(SvgImageSource),
            default(double),
            defaultBindingMode: BindingMode.OneWay
        );

    public double Height {
        get { return (double)GetValue(HeightProperty); }
        set { SetValue(HeightProperty, value); }
    }

    // ... Omitted

    static Assembly AssemblyCache;

    public static void RegisterAssembly(Type typeHavingResource = null)
    {
        if (typeHavingResource == null)
        {
            MethodInfo callingAssemblyMethod = typeof(Assembly).GetTypeInfo().GetDeclaredMethod("GetCallingAssembly");
            if (callingAssemblyMethod != null)
            {
                AssemblyCache = (Assembly)callingAssemblyMethod.Invoke(null, new object[0]);
            }
        }
    }

    public static ImageSource FromSvg(string resource, double width, double height, Color color = default(Color))
    {
        if (AssemblyCache == null)
        {
            MethodInfo callingAssemblyMethod = typeof(Assembly).GetTypeInfo().GetDeclaredMethod("GetCallingAssembly");
            if (callingAssemblyMethod != null)
            {
                AssemblyCache = (Assembly)callingAssemblyMethod.Invoke(null, new object[0]);
            }
            else
            {
                return null;
            }
        }
        var source = (SvgImageSource)FromSvg(resource, color);

        source.Width = width;
        source.Height = height;

        return source;
    }

    public static ImageSource FromSvg(string resource, Color color = default(Color))
    {
        if (AssemblyCache == null)
        {
            MethodInfo callingAssemblyMethod = typeof(Assembly).GetTypeInfo().GetDeclaredMethod("GetCallingAssembly");
            if (callingAssemblyMethod != null)
            {
                AssemblyCache = (Assembly)callingAssemblyMethod.Invoke(null, new object[0]);
            }
            else
            {
                return null;
            }
        }

        var realResource = GetRealResource(resource);
        if (realResource == null)
        {
            return null;
        }

        Func<Stream> streamFunc = () => AssemblyCache.GetManifestResourceStream(realResource);

        return new SvgImageSource { Stream = token => Task.Run(streamFunc, token), Color = color };

    }

    static string GetRealResource(string resource)
    {
        return AssemblyCache.GetManifestResourceNames()
                            .FirstOrDefault(x => x.EndsWith(resource, StringComparison.CurrentCultureIgnoreCase));

    }
} 
```

## 为 iOS 实现 IImageSourceHandler

这和源 Xamarin 差不多。Forms StreamImageSourceHandler。

我用 [Ngraphics](https://github.com/praeclarum/NGraphics) 将 LoadImageAsync 改为从 SVG 流中获取它。

LoadImageAsync 方法实际上是用来加载一个原生映像的，自定义的 ImageSource 可以通过在这里编写自己的进程来运行。

虽然这是 iOS 的例子，但它也可以以同样的方式在 Android 上实现。
更多信息请见 [Github 来源](https://github.com/muak/SvgImageSource/blob/master/SvgImageSource.Droid/SvgImageSourceHandler.cs)。

```
public class SvgImageSourceHandler : IImageSourceHandler
 {
     internal static float ScreenScale;

     public async Task<UIImage> LoadImageAsync(ImageSource imagesource, CancellationToken cancelationToken = default(CancellationToken), float scale = 1)
     {
         UIImage image = null;
         var svgsource = imagesource as SvgImageSource;
         if (svgsource?.Stream != null)
         {
             using (var streamImage = await ((IStreamImageSource)svgsource).GetStreamAsync(cancelationToken).ConfigureAwait(false))
             {
                 if (streamImage != null)
                     image = GetUIImage(streamImage, svgsource.Width, svgsource.Height, svgsource.Color, scale);
             }
         }

         if (image == null)
         {
             Log.Warning(nameof(SvgImageSourceHandler), "Could not load image: {0}", svgsource);
         }

         return image;
     }

     UIImage GetUIImage(Stream stream, double width, double height, Color color, float scale)
     {
         Graphic g = null;
         using (var sr = new StreamReader(stream))
         {
             g = Graphic.LoadSvg(sr);
         }

         var newSize = SvgUtility.CalcAspect(g.Size, width, height);

         if (width > 0 || height > 0)
         {
             g = SvgUtility.Resize(g, newSize);
         }

         if (scale <= 1)
         {
             scale = ScreenScale;
         }

         var canvas = Platforms.Current.CreateImageCanvas(newSize, scale);

         if (color != Xamarin.Forms.Color.Default)
         {
             var nColor = new NGraphics.Color(color.R, color.G, color.B, color.A);

             foreach (var element in g.Children)
             {
                 SvgUtility.ApplyColor(element, nColor);
                 element.Draw(canvas);
             }
         }
         else
         {
             g.Draw(canvas);
         }

         return canvas.GetImage().GetUIImage();
     }
 } 
```

## 注册自定义 ImageSource 和 ImageSourceHandler

最后用 Xamarin 注册。“用 SvgImageSourceHandler 处理 SvgImageSource”的表单。

这个例子是在初始化这个库的时候注册它。

请注意，此方法未在 intellisense 中显示，因为它是在 EditorBrowsable 属性中指定的。

```
public static class SvgImage
{
    public static void Init()
    {
        Internals.Registrar.Registered.Register(typeof(SvgImageSource), typeof(SvgImageSourceHandler));
    }
} 
```

通过注册到内部。Registrar.Registered，这样一个控件作为一个 Image 调用对应 ImageSource 类型的处理程序，并调用 LoadImageAsync 方法，得到一个原生图像。因此 Xamarin。Image 可以显示一个 SVG 图像而不改变它的代码。

结果是，默认图像控件可以设置为 SVG 图像并显示，而无需更改自身。

[![](../Images/9650f1156f07513c11b40770f4259450.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7z3kYK_W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ck4pp3gk0nhtbxxq5rpr.png)

有关 SvgImageSource 的更多信息，请参见[自述文件](https://github.com/muak/SvgImageSource)。

## 结论

披露 Xamarin。Forms.Internals 非常好。我对改变图像控件的行为而不像本文那样扩展它印象深刻。

同样，我认为，如果我自己的手势识别器也能被创建就好了。