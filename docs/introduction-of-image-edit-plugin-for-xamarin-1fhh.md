# Xamarin 的图像编辑插件介绍

> 原文：<https://dev.to/muak_x/introduction-of-image-edit-plugin-for-xamarin-1fhh>

我需要在我的应用程序中处理图像的裁剪、大小调整、旋转和保存。所以我用 DependencyService 实现了。

在那之后，我尝试提取处理过程作为一个库使用，我认为这应该作为一个插件。

这就是为什么我制作了一个编辑图片的插件。这个插件可以在 NuGet 上获得。

## 来源

[https://github.com/muak/Xamarin.Plugin.ImageEdit](https://github.com/muak/Xamarin.Plugin.ImageEdit)

## Nuget

[https://www.nuget.org/packages/Xamarin.Plugin.ImageEdit/](https://www.nuget.org/packages/Xamarin.Plugin.ImageEdit/)

## 安装

```
Install-Package Xamarin.Plugin.ImageEdit 
```

安装到您的 PCL /。NETStandard 项目和每个平台项目。

## 支持

| 平台 | 支持 | 版本 |
| --- | --- | --- |
| Xamarin.iOS | 是 | iOS 9+ |
| Xamarin。机器人 | 是 | API 22+标准 |
| Windows 10 UWP | 不 |  |
| Xamarin.Mac | 不 |  |

## 特性

*   调整大小
*   种植
*   旋转
*   转向单色
*   转换为 png/jpeg 字节数组
*   获取 ARGB 像素信息

[https://www.youtube.com/embed/u__sdgHhinM](https://www.youtube.com/embed/u__sdgHhinM)

## 如何使用

在以下代码中，可以对图像进行裁剪、旋转、调整大小和转换 PNG 数据:

```
using (var image = await CrossImageEdit.Current.CreateImageAsync(imageByteArray)) {
    var croped = await Task.Run(() =>
            image.Crop(10, 20, 250, 100)
                 .Rotate(180)
                 .Resize(100, 0)
                 .ToPng()
    );
} 
```

### 用 DI 容器(如. prism.forms)

如果作为插件使用，很难进行单元测试。
考虑到单元测试，你应该使用 DI 容器比如 prism.forms 上的 unity 作为下面的代码:

```
//View model constructor
public ViewModel(IImageEdit imageEdit){

    using (var image = await imageEdit.CreateImageAsync(imageByteArray)) {
        var croped = await Task.Run(() =>
                image.Crop(10, 20, 250, 100)
                     .Rotate(180)
                     .Resize(100, 0)
                     .ToPng()
        );
    }
}

//on platform
public class iOSInitializer : IPlatformInitializer
{
    public void RegisterTypes(IUnityContainer container)
    {
        container.RegisterType<IImageEdit,ImageEdit>();
    }
} 
```

## API 用法

### IImageEdit。CreateImageAsync

此方法用于获取 EditableImage 对象。

```
//from byte[]
var editableImage = await CrossImageEdit.Current.CreateImageAsync(imageByteArray);

//from stream
var editableImage = await CrossImageEdit.Current.CreateImageAsync(imageStream); 
```

您可以使用 got EditableImage 操作图像。

### IEditableImage。调整大小

此方法调整图像大小并返回 IEditableImage。

```
var width = 200;
var height = 150;
editableImage.Resize(width, height);
editableImage.Resize(width, 0); //auto height
editableImage.Resize(0, height); //auto width

editableImage.Resize(50); //specify max length of long side. other side auto size. 
```

将宽度或高度指定为 0，另一边将按照纵横比自动调整。

只传递一个参数，长边长度将成为长度值，另一边将按照长宽比自动调整。

### IEditableImage。农作物

该方法裁剪图像并返回 IEditableImage。

```
var x = 10;
var y = 10;
var width = 50;
var height = 50;
editableImage.Crop(x, y, width, height); 
```

### IEditableImage。辐状的

该方法旋转图像并返回 IEditableImage。

```
var degree = 90; // 0-360;
editableImage.Rotate(degree); 
```

旋转角度指定为 0 到 360 度。

### 砂砾石。明天罗马

此方法将图像颜色转换为单色，并返回 IEditableImage。

```
editableImage.ToMonochrome(); 
```

### 砂砾石。托邦

此方法将图像转换为 PNG 并返回 PNG 字节数组。

```
var pngBytes = editableImage.ToPng(); 
```

### 砂砾石。ToJpeg 格式

此方法将图像转换为 JPEG，并返回 JPEG 字节数组。

```
var jpgBytes = editableImage.ToJpeg(90); // quality(0-100) 
```

图像质量指定为 0 到 100。

### 砂砾石。烤面包片

该方法返回包含 ARGB 信息的图像像素数组。

例如，当该数组的元素具有 0xFF00F090 值时，状态如下。

| A | 稀有 | G | B |
| --- | --- | --- | --- |
| 消防 | 00 | 寡霉素敏感比较因子 | Ninety |

为了从这个值中得到一个单独的颜色，使用了如下代码。

```
var pixels = editableImage.ToArgbPixels();

var pixel = pixels[10];
var r = pixel & 0x00FF0000 >> 16; //Get R
var g = pixel & 0x0000FF00 >> 8;  //Get G
var b = pixel & 0x000000FF;       //Get B 
```

### 砂砾石。getnativedimage

此方法返回每个平台本机映像。如果平台是 iOS，返回 UIImage 否则，返回位图。

## 总结性的

如果你能使用这个图书馆，我将不胜感激。

如果有什么问题、请求、bug 报告，请向 GitHub issues 或 twitter ( [@muak_x](https://dev.to/muak_x) )反映。

谢谢你。