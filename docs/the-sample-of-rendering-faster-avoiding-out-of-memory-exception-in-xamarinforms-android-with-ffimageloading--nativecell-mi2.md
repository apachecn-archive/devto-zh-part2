# Xamarin 中避免内存不足异常的快速渲染示例。带有 ffimageloaging+native cell 的表单(Android)

> 原文：<https://dev.to/muak_x/the-sample-of-rendering-faster-avoiding-out-of-memory-exception-in-xamarinforms-android-with-ffimageloading--nativecell-mi2>

Xamarin。Android ListView 在使用图片时性能很差。使用快速渲染器时也是如此。

由于在继续使用 ListView 时，渲染速度很慢，或者由于内存泄漏导致应用程序崩溃，因此有必要采取一些解决方法。

为了解决这个问题，我采取了结合 NativeCell(每个平台单元的一个实现)和 FImageLoading 的措施。

这就是为什么本文使用 NativeCell + FFImageLoading 来描述示例，以便舒适地使用 ListView。

## 一个已完成的样品项目

*   [https://github.com/muak/FFNativeCell](https://github.com/muak/FFNativeCell)

## 所需库

*   [f 图像加载](https://github.com/luberda-molinet/FFImageLoading)

这是一个很好的库，可以帮助我们做很多事情，比如缓存图像和异步加载。

## 在没有采取措施的情况下

作为样本使用的图像是中等到大的每张图片 20 张 500KB〜1000KB 的图像。这些被放置在 ViewCell 上，并依次在 ListView 上显示 20 行。

```
<ListView x:Name="listview" ItemsSource="{Binding Items}" HasUnevenRows="false" RowHeight="400">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <StackLayout Spacing="0" Margin="0,0,0,6" BackgroundColor="Silver">
                    <StackLayout.GestureRecognizers>
                        <TapGestureRecognizer Command="{Binding BindingContext.GoDetailCommand,Source={x:Reference listview}}" CommandParameter="{Binding PhotoUrl}" />
                    </StackLayout.GestureRecognizers>
                    <Image Aspect="AspectFill" Source="{Binding PhotoUrl}" HorizontalOptions="FillAndExpand" HeightRequest="300" />
                    <Label Text="{Binding Title,StringFormat='Title{0:N}'}" />
                    <Label Text="{Binding Date,StringFormat='Taken at：{0:N}'}" />
                </StackLayout>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView> 
```

Enter fullscreen mode Exit fullscreen mode

[https://www.youtube.com/embed/2Kp9rOFbFF8](https://www.youtube.com/embed/2Kp9rOFbFF8)

像这样，应用程序很快就会崩溃。

## 使用 FFImageLoading

FFImageLoading 的 CachedImage 用于避免前面部分提到的问题。

使用 CachedImage 视图而不是 Image 视图，其缩减取样属性设置为 320。这使得显示的图像不是原来的图像，而是宽度缩小到 320 的图像。

```
<ListView x:Name="listview" ItemsSource="{Binding Items}" HasUnevenRows="false" RowHeight="400">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <StackLayout Spacing="0" Margin="0,0,0,6" BackgroundColor="Silver">
                    <StackLayout.GestureRecognizers>
                        <TapGestureRecognizer Command="{Binding BindingContext.GoDetailCommand,Source={x:Reference listview}}" CommandParameter="{Binding PhotoUrl}" />
                    </StackLayout.GestureRecognizers>
                    <ff:CachedImage DownsampleWidth="320" Aspect="AspectFill" Source="{Binding PhotoUrl}" HorizontalOptions="FillAndExpand" HeightRequest="300" />
                    <Label Text="{Binding Title,StringFormat='Title{0:N}'}" />
                    <Label Text="{Binding Date,StringFormat='Taken at：{0:N}'}" />
                </StackLayout>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView> 
```

Enter fullscreen mode Exit fullscreen mode

这是加载图像的页面。

[https://www.youtube.com/embed/zm-493acniw](https://www.youtube.com/embed/zm-493acniw)

这里的页面完全加载了图片。

[https://www.youtube.com/embed/cMBHbfidv38](https://www.youtube.com/embed/cMBHbfidv38)

应用程序没有崩溃，然后显示图像变得流畅。我认为，内存不足的问题通常是通过这种方式解决的。

## f image loading+native cell+内存不足错误的其他解决方法

即使使用了 CachedImage，当出现内存不足的问题时，也可以通过使用本机单元来解决。

这里是使用视图单元格作为本地单元格的示例。
[https://developer . xa marin . com/guides/xa marin-forms/application-fundamentals/custom-renderer/view cell/](https://developer.xamarin.com/guides/xamarin-forms/application-fundamentals/custom-renderer/viewcell/)

实现原生单元的方法是

1.  在. NET 标准项目中定义从 ViewCell 派生的 MyViewCell 类。
2.  在资源/布局中为布局创建一个 XML 文件，并定义布局。
3.  在 Android 平台项目中定义一个从 LinearLayout 派生的适当的 native cell 类，并对其实现 INativeElementView。
4.  在 Android 平台项目中定义从 ViewCellRenderer 派生的 MyViewCellRenderer 类，并附加 ExportRederer 属性。

内存不足错误的另一个解决方法是

1.  在 AndroidManifest.xml 中启用 largeHeap 选项(最有效)。
2.  向 MainActivity 类添加 OnTrimMemory 重写方法。
3.  定义从 ImageViewAsync 派生的 MyImageViewAsync 类。

有关更多信息，请参见官方 wiki[https://github . com/luber da-molinet/ffimage loading/wiki/Advanced-Usage # clear-cache-and-memory-considerations](https://github.com/luberda-molinet/FFImageLoading/wiki/Advanced-Usage#clear-cache-and-memory-considerations)。

具体代码在下面几节中给出。

### 定义一个. NET 标准项目中从 ViewCell 派生的 PhotoViewCell 类。

```
public class PhotoViewCell : ViewCell
{
    public static BindableProperty PhotoItemProperty =
        BindableProperty.Create(
            nameof(PhotoItem),
            typeof(MainPageViewModel.PhotoItem),
            typeof(PhotoViewCell),
            null,
         defaultBindingMode: BindingMode.OneWay
        );

    public MainPageViewModel.PhotoItem PhotoItem {
        get { return (MainPageViewModel.PhotoItem)GetValue(PhotoItemProperty); }
        set { SetValue(PhotoItemProperty, value); }
    }

    public static BindableProperty CommandProperty =
        BindableProperty.Create(
            nameof(Command),
            typeof(ICommand),
            typeof(PhotoViewCell),
            null,
         defaultBindingMode: BindingMode.OneWay
        );

    public ICommand Command {
        get { return (ICommand)GetValue(CommandProperty); }
        set { SetValue(CommandProperty, value); }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是与平台项目沟通的内容。虽然可以设置单独的标题和 URL，但是传递它们很麻烦，所以我决定传递下面这个类的一个单元。

```
public class PhotoItem
{
    public string PhotoUrl { get; set; }
    public string Title { get; set; }
    public string Date { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 在资源/布局中创建布局的 XML 文件，并定义布局。

布局是用 XML 定义的。然后，将其命名为“PhotoViewCell.axml ”,并保存在 Resources/Layout 中。
在此示例中，自定义 ImageView 用于更有效地使用内存。但是您可以使用任何 ImageView。

自定义 ImageView 将在下面的章节[定义从 ImageViewAsync](#define_custom_imageasync) 派生的 MyImageViewAsync 类中详细讨论。

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="406dp"
    android:minHeight="406dp">
    <sample.droid.cells.MyImageView
        android:id="@+id/PhotoViewImage"
        android:scaleType="centerCrop"
        android:layout_alignParentTop="true"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:minHeight="300dp" />
    <TextView
        android:id="@+id/PhotoViewTitle"
        android:layout_below="@id/PhotoViewImage"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
    <TextView
        android:id="@+id/PhotoViewDate"
        android:layout_below="@id/PhotoViewTitle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
    <LinearLayout
        android:background="#FFFFFF"
        android:layout_alignParentBottom="true"
        android:layout_width="match_parent"
        android:layout_height="6dp" />
</RelativeLayout> 
```

Enter fullscreen mode Exit fullscreen mode

### 定义原生细胞类

这个单元格实际上就是在原生 ListView 中显示的内容。
在构造函数中，对应的布局被虚化，对每个控件的引用被 get，并添加了 OnClickListener。

在 UpdateCell 方法中，在清除前一个任务后更新当前单元格的内容，以便进行适当的更新。具体来说，如果前一个加载任务正在运行，则取消它，然后执行当前任务。
如果没有这个进程，性能会很差，因为不必要的无效进程会继续运行。

在 CellPropertyChanged 方法中，如果 Xamarin 更新了 forms 单元格，则调用 UpdateCell 方法。Listview 缓存策略是 RecycleElement。
当动态改变 Xamarin 时也调用它。形成单元格值。
如果缓存策略是 RetainElement，则不需要。

INativeElementView 是 RecycleElement 所需要的，在这种情况下必须实现它。

```
public class PhotoNativeCell : LinearLayout, INativeElementView, Android.Views.View.IOnClickListener
{
    public PhotoViewCell PhotoViewCell { get; set; }
    public Element Element => PhotoViewCell;
    public IScheduledWork CurrentTask { get; set; }

    public MyImageView ImageView { get; set; }
    public TextView Title { get; set; }
    public TextView Date { get; set; }

    public PhotoNativeCell(Context context, PhotoViewCell formsCell) : base(context)
    {
        var view = (context as FormsAppCompatActivity).LayoutInflater.Inflate(Resource.Layout.PhotoViewCell, this, true);

        PhotoViewCell = formsCell;

        ImageView = view.FindViewById<MyImageView>(Resource.Id.PhotoViewImage);
        Title = view.FindViewById<TextView>(Resource.Id.PhotoViewTitle);
        Date = view.FindViewById<TextView>(Resource.Id.PhotoViewDate);

        SetOnClickListener(this);
    }

    public void CellPropertyChanged(object sender,PropertyChangedEventArgs e)
    {
        if (e.PropertyName == PhotoViewCell.PhotoItemProperty.PropertyName) {
            // Update for when the cache strategy of Xamarin.Forms.ListView is RecycleElement
            // or changing dynamically Xamarin.Forms cell value.
            UpdateCell();
        }
    }

    public void UpdateCell()
    {

        // If the previous task is no completed and no canceled, the task is canceled. 
        if (CurrentTask != null && !CurrentTask.IsCancelled && !CurrentTask.IsCompleted) {
            CurrentTask.Cancel();
        }

        // An alternate image until the image is completely loaded (here is transparent).
        ImageView.SetImageResource(global::Android.Resource.Color.Transparent);

        // Begin loading the image
        CurrentTask = ImageService.Instance.LoadUrl(PhotoViewCell.PhotoItem.PhotoUrl).DownSample(320).Into(ImageView);
        // Set the key in order to clear the CachedImage memory cache when finalizing.
        ImageView.Key = PhotoViewCell.PhotoItem.PhotoUrl;

        // Update the cell's text
        Title.Text = PhotoViewCell.PhotoItem.Title;
        Date.Text = PhotoViewCell.PhotoItem.Date;
    }

    public void OnClick(Android.Views.View v)
    {
        PhotoViewCell.Command?.Execute(PhotoViewCell.PhotoItem.PhotoUrl);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 定义 CustomCellRenderer

这个类将一个表单单元格与一个本地单元格相关联。

在 GetCellCore 方法中，项被赋予 forms 单元格，convertView 被赋予 null 或原生单元格。

convertView 为空意味着它是一个新的单元格；否则意味着它是一个循环单元。
所以前者得创建一个新的原生细胞，后者得清理先前的细胞。

在这两种情况下，都需要更新引用的 forms 单元格和 PropertyChanged 事件。

具体过程是在原生细胞旁完成的。关于更多信息，参见上面的章节[定义原生细胞类](#define_native_cell)。

```
[assembly: ExportRenderer(typeof(PhotoViewCell), typeof(PhotoViewCellRenderer))]
namespace Sample.Droid.Cells
{
    public class PhotoViewCellRenderer : ViewCellRenderer
    {
        protected override Android.Views.View GetCellCore(Xamarin.Forms.Cell item, Android.Views.View convertView, Android.Views.ViewGroup parent, Android.Content.Context context)
        {
            var formsCell = item as PhotoViewCell;
            var nativeCell = convertView as PhotoNativeCell;

            if (nativeCell == null) {
                // Creating a new real native cell. 
                nativeCell = new PhotoNativeCell(context, formsCell);
            }

            // Unsubscribe the privious formscell propertychanged event on the nativecell.
            nativeCell.PhotoViewCell.PropertyChanged -= nativeCell.CellPropertyChanged;

            // Update the formscell reffered by the nativecell. 
            nativeCell.PhotoViewCell = formsCell;

            // Subscribe the current formscell propertychanged event on the nativecell.
            nativeCell.PhotoViewCell.PropertyChanged += nativeCell.CellPropertyChanged;

            // Update the nativecell contents with the current formscell contents.
            nativeCell.UpdateCell();

            return nativeCell;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### Xaml 为原生细胞(Android)

在这个示例中，由于 native cell 仅在 Android 上使用，因此 Xaml 使用 OnPlatform 标记分为 native cell 和 ViewCell。

下面是原生细胞如何只在 Android 上使用。

```
<ListView x:Name="listview" CachingStrategy="RecycleElement" ItemsSource="{Binding Items}" HasUnevenRows="false" RowHeight="400">
    <ListView.ItemTemplate>
        <DataTemplate>
            <cell:PhotoViewCell PhotoItem="{Binding}" Command="{Binding BindingContext.GoDetailCommand,Source={x:Reference listview}}">
                <!-- Above properties are just available on Android -->
                <!-- Below ViewCell content is just available on iOS -->
                <ViewCell.View>
                    <OnPlatform x:TypeArguments="View">
                        <On Platform="iOS">
                            <StackLayout Spacing="0" Margin="0,0,0,6" BackgroundColor="Silver">
                                <StackLayout.GestureRecognizers>
                                    <TapGestureRecognizer Command="{Binding BindingContext.GoDetailCommand,Source={x:Reference listview}}" CommandParameter="{Binding PhotoUrl}" />
                                </StackLayout.GestureRecognizers>
                                <ff:CachedImage DownsampleWidth="320" Aspect="AspectFill" Source="{Binding PhotoUrl}" HorizontalOptions="FillAndExpand" HeightRequest="300" />
                                <Label Text="{Binding Title,StringFormat='Title：{0:N}'}" />
                                <Label Text="{Binding Date,StringFormat='Taken at：{0:N}'}" />
                            </StackLayout>
                        </On>
                    </OnPlatform>
                </ViewCell.View>
            </cell:PhotoViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView> 
```

Enter fullscreen mode Exit fullscreen mode

[https://www.youtube.com/embed/puQueNnn94A](https://www.youtube.com/embed/puQueNnn94A)

这个速度，据我所见，和 view cell+ffimageloaging 一样好。但是我认为记忆会被有效地利用。

### 在 AndroidManifest.xml 中启用大型堆选项

如果一个应用程序使用的内存大小需要很大，那么在 AndroidManifest.xml 中编写如下的`android:largeHeap="true"`。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="kamusoft.sample">
    <uses-sdk android:minSdkVersion="21" />
    <application android:label="Sample" android:largeHeap="true"></application>
</manifest> 
```

Enter fullscreen mode Exit fullscreen mode

虽然这与内存效率无关，但我认为这对于内存不足错误是最有效的。

### 向 MainActivity 类添加 OnTrimMemory 覆盖方法

欲了解更多信息，请参见[https://github . com/luberda-molinet/ffimage loading/wiki/Advanced-Usage # clear-cache-and-memory-considerations](https://github.com/luberda-molinet/FFImageLoading/wiki/Advanced-Usage#clear-cache-and-memory-considerations)

```
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    ...
    public override void OnTrimMemory([GeneratedEnum] TrimMemory level)
    {
        ImageService.Instance.InvalidateMemoryCache();
        GC.Collect(GC.MaxGeneration, GCCollectionMode.Forced);
        base.OnTrimMemory(level);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 定义从 ImageViewAsync 派生的 MyImageViewAsync 类

这个自定义的 ImageViewAsync 在 Java 完成时清除内存缓存。
Register 属性支持 XML 中的自定义视图。

欲了解更多信息，请参见[https://github . com/luberda-molinet/ffimage loading/wiki/Advanced-Usage # clear-cache-and-memory-considerations](https://github.com/luberda-molinet/FFImageLoading/wiki/Advanced-Usage#clear-cache-and-memory-considerations)

```
[Android.Runtime.Preserve(AllMembers = true)]
[Register("sample.droid.cells.MyImageView")]
public class MyImageView : ImageViewAsync
{
    public string Key { get; set; }

    public MyImageView(Context context) : base(context)
    {
    }

    public MyImageView(IntPtr javaReference, JniHandleOwnership transfer) : base(javaReference, transfer)
    {
    }

    public MyImageView(Context context, IAttributeSet attrs) : base(context, attrs)
    {
    }

    protected override void JavaFinalize()
    {
        SetImageDrawable(null);
        SetImageBitmap(null);
        ImageService.Instance.InvalidateCacheEntryAsync(Key, FFImageLoading.Cache.CacheType.Memory);
        base.JavaFinalize();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

本文介绍了几种内存不足的 FFImageLoading 解决方法。

通常情况下，只要用 CachedImage 替换默认的图像视图就可以解决问题。

尽管如此，如果发生了内存不足的错误，正如后面这一节所讨论的，您可以将诸如本机单元和 lerge 堆这样的内存设置放在一起。

在这种情况下，你不得不创建更多的类。但是设计越复杂，使用原生单元的优势就越大。
所以我认为，还是以自己想要的为主来判断比较好。

谢谢你看了这篇太长的文章。

## 关于本样本 app 使用的图片。

[http://sozai-free.com/](http://sozai-free.com/)
感谢。