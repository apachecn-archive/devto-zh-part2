# ★ laravel-medialibrary v7 已经发布

> 原文：<https://dev.to/freekmurze/laravel-medialibrary-v7-has-been-released-56c2>

laravel-medialibrary 是一个强大的软件包，可以帮助处理 laravel 应用程序中的媒体。它可以[跨多个文件系统](https://docs.spatie.be/laravel-medialibrary/v6/advanced-usage/working-with-multiple-filesystems)、[组织你的文件，生成缩略图](https://docs.spatie.be/laravel-medialibrary/v6/converting-images/defining-conversions)、[优化图像](https://docs.spatie.be/laravel-medialibrary/v6/converting-images/optimizing-converted-images)等等。在 Spatie，我们几乎在每个项目中都使用这个包。

在过去的几个月里，我们的团队一直在努力工作，以创建一个新的主要版本的软件包。我很高兴地告诉大家，本周我在新德里[拉勒维尔现场会议](http://laravellive.in/)的舞台上发布了新款 v7。在这篇博文中，我想向你展示一些新的特性。

## 一般介绍

要了解媒体库的基本功能，您应该观看此视频。

如果你没有时间看完整个视频，这里有一些快速的例子可以帮助你快速上手。本质上，打包允许你将文件与雄辩的模型联系起来。

```
$yourModel
    ->addMedia($filePath)
    ->toMediaCollection(); 
```

这将把位于`$filePath`的文件移动到媒体库，并将一个新的`Media`型号与`$yourModel`相关联。您可以添加任意数量的文件。

要获取所有相关媒体，您可以这样做:

```
$yourModel->getMedia(); 
```

这将返回相关联的`Media`模型的`Collection`。因为我发现第一个关联的媒体通常需要有一个`getFirstMedia`方法，正如它的名字所暗示的那样。

一个媒体对象，您可以确定其关联文件的 url 和路径。

```
$yourModel->getFirstMedia()->getUrl() // returns an url
$yourModel->getFirstMedia()->getPath() // returns the full path 
```

如果您删除与媒体相关联的模型，媒体库也会清理文件。你不需要自己收拾。

```
// all related files will be deleted from the filesystem
$yourModel->delete(); 
```

媒体可以放入所谓的媒体收藏中。这实际上只是给一组介质加上一个名称，以便您可以一起检索它们。

```
$yourModel
    ->addMedia($pathToImage)
    ->toMediaCollection('images');

$yourModel
    ->addMedia($pathToAnotherImage)
    ->toMediaCollection('images');

$yourModel
    ->addMedia($pathToPdf)
    ->toMediaCollection('downloads');

$yourModel
    ->addMedia($pathToAnotherPdf)
    ->toMediaCollection('downloads');

$yourModel->getMedia('images'); // only get media in the images collection

$yourModel->getMedia('downloads'); // only get media in the downloads collection 
```

媒体库也可以在外部文件系统上存储东西。在引擎盖下，使用 Laravel 的原生云文件系统。所以你可以在所有有 FlySystem 驱动文件系统上存储你的媒体(S3，Dropbox，Google Drive，SFTP，...)

在远程文件系统上存储媒体简单得可笑。就用磁盘名作为`toMediaCollection`的第二个参数。

```
// the image will be uploaded to s3
$yourModel
    ->addMedia($pathToImage)
    ->toMediaCollection('images', 's3');

$yourModel->getFirstMedia('images')->getUrl(); // returns an url to the file on s3 
```

媒体库也可以生成派生图像。图像您想要创建一个图像的小缩略图。那很简单！在你的口才模型中，你应该定义一个媒体转换。

```
// in your model
 public function registerMediaConversions()
{
    $this
            ->addMediaConversion('thumb')
        ->width(50)
        ->height(50);
 } 
```

每当您将媒体与您的模型相关联时，将会创建一个适合 50x50 盒子的衍生图像，纵横比将得到考虑。

```
$yourModel
    ->addMedia($pathToImage)
    ->toMediaCollection();

$yourModel->getFirstMedia()->getUrl() // url to original image
$yourModel->getFirstMedia()->getUrl('thumb') // url to the thumbnail version. 
```

默认情况下，所有图像转换都会排队。您可以在一个模型上进行任意多的图像转换。在引擎盖下，我们的[图像包](https://docs.spatie.be/image)被使用，所以你可以对它进行任何支持的操作。

以上例子只是冰山一角。如果你想知道更多关于所有可用特性的信息，去阅读[我们为这个包写的大量文档](https://docs.spatie.be/laravel-medialibrary)。

有了这个一般性的介绍，让我们把重点放在软件包的闪亮的新 v7 中添加的新功能上。

## V7 新增:渐进式图像加载和响应图像

v7 中最重要的新增功能之一是支持响应图像。人们可以在不同的设备上浏览网站，这些设备的屏幕尺寸和连接速度大相径庭。提供图像时，最好不要对所有设备使用相同的图像。一个大的图像在一个有快速互联网连接的台式电脑上可能是好的，但是在一个带宽有限的小型移动设备上，下载可能需要很长时间。

显示图片最常见的方式是使用带有`src`属性的`img`元素。

```
<img src="my-image.jpg"> 
```

使用这个标记，无论屏幕大小如何，浏览器都将始终显示`my-image.jpg`。

正如在 [HTML 规范](https://html.spec.whatwg.org/multipage/embedded-content.html#attr-img-srcset)中所描述的，你也可以使用一个`srcset`属性来表示你的图像的不同版本和它们各自的宽度。

```
<img src="large.jpg" srcset="large.jpg 2400w, medium.jpg 1200w, small.jpg 600w"> 
```

使用`srcset`时，浏览器会自动计算出哪张图片最适合使用。

有反应的图像是很好的补充。当然，要显示图像，它需要先通过网络。在糟糕的网络条件下，即使一个小的图像下载也需要一段时间。当一张图片最终被下载后，页面的布局可能会被重新调整，这会分散注意力。

为了解决这个问题，媒体库中增加了对渐进式图像加载的支持。你可能以前在[中型]博客上见过这种技术(这里有一个例子。你首先看到一个模糊的图像。过了一会儿，那些图像就被真实的图像取代了。

媒体库可以为您生成图像的模糊占位符和所有响应版本(这些放在`srcset`属性中)。向媒体库添加媒体时，只需使用`withResponsiveImages`功能。

```
$yourModel
   ->addMedia($yourImageFile)
   ->withResponsiveImages()
   ->toMediaCollection(); 
```

默认情况下，媒体库使用一种算法来生成响应图像，该图像的文件大小是以前变体的 70%。它将不断产生变化，直到预测的文件大小低于 10 Kb 或目标宽度小于 20 像素。因此，对于尺寸较大的图像，媒体库会比尺寸较小的图像产生更多的变化。

为了显示响应图像，您只需在 Blade view 中输出一个媒体实例。

```
// we use a closure route here, normally you'd do this in a controller

Route::get('showing-responsive-images', function() {
    $yourModel->getFirstMedia();

    return view('showing-responsive-images', compact('media'));
}); 
```

刀片视图可能如下所示:

```
{{-- Yup, there's nothing more to it --}}
{{ $media }} 
```

这将输出如下所示的 html:

```
<img
     srcset="
         /media/1/responsive-images/test___medialibrary_original_2048_1536.jpg 2048w,
         /media/1/responsive-images/test___medialibrary_original_1713_1284.jpg 1713w,
         /media/1/responsive-images/test___medialibrary_original_1433_1074.jpg 1433w,
         /media/1/responsive-images/test___medialibrary_original_1199_899.jpg 1199w,
         /media/1/responsive-images/test___medialibrary_original_1003_752.jpg 1003w, 
         /media/1/responsive-images/test___medialibrary_original_839_629.jpg 839w, 
         /media/1/responsive-images/test___medialibrary_original_702_526.jpg 702w,
         /media/1/responsive-images/test___medialibrary_original_587_440.jpg 587w,
         /media/1/responsive-images/test___medialibrary_original_491_368.jpg 491w,
         /media/1/responsive-images/test___medialibrary_original_411_308.jpg 411w,
         /media/1/responsive-images/test___medialibrary_original_344_258.jpg 344w,
         /media/1/responsive-images/test___medialibrary_original_287_215.jpg 287w,
         /media/1/responsive-images/test___medialibrary_original_240_180.jpg 240w, 
         data:image/svg+xml;base64,PCFET0NUWVBFIHN2ZyBQVUJMSUMgIi0vL1czQy8vRFREIFNWRyAxLjEvL0VOIiAiaHR0cDovL3d3dy53My5vcmcvR3JhcGhpY3MvU1ZHLzEuMS9EVEQvc3ZnMTEuZHRkIj4KPHN2ZyB2ZXJzaW9uPSIxLjEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiIHg9IjAiCiB5PSIwIiB2aWV3Qm94PSIwIDAgMjA0OCAxNTM2Ij4KCTxpbWFnZSB3aWR0aD0iMjA0OCIgaGVpZ2h0PSIxNTM2IiB4bGluazpocmVmPSJkYXRhOmltYWdlL2pwZWc7YmFzZTY0LC85ai80QUFRU2taSlJnQUJBUUVBWUFCZ0FBRC8vZ0E3UTFKRlFWUlBVam9nWjJRdGFuQmxaeUIyTVM0d0lDaDFjMmx1WnlCSlNrY2dTbEJGUnlCMk9UQXBMQ0J4ZFdGc2FYUjVJRDBnT1RBSy85c0FRd0FEQWdJREFnSURBd01EQkFNREJBVUlCUVVFQkFVS0J3Y0dDQXdLREF3TENnc0xEUTRTRUEwT0VRNExDeEFXRUJFVEZCVVZGUXdQRnhnV0ZCZ1NGQlVVLzlzQVF3RURCQVFGQkFVSkJRVUpGQTBMRFJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVVGQlFVRkJRVUZCUVUvOEFBRVFnQUdBQWdBd0VpQUFJUkFRTVJBZi9FQUI4QUFBRUZBUUVCQVFFQkFBQUFBQUFBQUFBQkFnTUVCUVlIQ0FrS0MvL0VBTFVRQUFJQkF3TUNCQU1GQlFRRUFBQUJmUUVDQXdBRUVRVVNJVEZCQmhOUllRY2ljUlF5Z1pHaENDTkNzY0VWVXRId0pETmljb0lKQ2hZWEdCa2FKU1luS0NrcU5EVTJOemc1T2tORVJVWkhTRWxLVTFSVlZsZFlXVnBqWkdWbVoyaHBhbk4wZFhaM2VIbDZnNFNGaG9lSWlZcVNrNVNWbHBlWW1acWlvNlNscHFlb3FhcXlzN1MxdHJlNHVickN3OFRGeHNmSXljclMwOVRWMXRmWTJkcmg0dVBrNWVibjZPbnE4Zkx6OVBYMjkvajUrdi9FQUI4QkFBTUJBUUVCQVFFQkFRRUFBQUFBQUFBQkFnTUVCUVlIQ0FrS0MvL0VBTFVSQUFJQkFnUUVBd1FIQlFRRUFBRUNkd0FCQWdNUkJBVWhNUVlTUVZFSFlYRVRJaktCQ0JSQ2thR3h3UWtqTTFMd0ZXSnkwUW9XSkRUaEpmRVhHQmthSmljb0tTbzFOamM0T1RwRFJFVkdSMGhKU2xOVVZWWlhXRmxhWTJSbFptZG9hV3B6ZEhWMmQzaDVlb0tEaElXR2g0aUppcEtUbEpXV2w1aVptcUtqcEtXbXA2aXBxckt6dExXMnQ3aTV1c0xEeE1YR3g4akp5dExUMU5YVzE5aloydUxqNU9YbTUranA2dkx6OVBYMjkvajUrdi9hQUF3REFRQUNFUU1SQUQ4QStFanBkeG9Gd2wwWTJDNTY0cjE3d0pIZjZyRkhkTEorNlBHMnZxU1g5bXZSOWIrSG9NMEtpZmFTSHgzcjU2c2RKdlBoanJjMWpOSHZ0MWJFZWF2TDhTc1p6Y3NXckcyT3djcVVVMXJjeVBHVnZwbW1TRjdvK1ZKakl6NjE0OTR0dVQ0amxXTzNPNUY0elhzWHhDOEY2aDR6a2p1bGpaVWJvb0ZlT2VNdkMrcStBcGxTZU5vMWZrWkZkdGFqWnR0bkRCT0s1V2o3bDFqOXBJYWI0VVd6aSs4QjFyNXQrSWZ4RHZOZnUwdTFmNXQzRkZGZUxsODVVYWxvUGRuME9JZDZXcFkwZjR3NmhvQzIwdDZxeVc2WU9DSzV2NHkvRVdINHBQRExGQ3NRakdPQlJSWDJGU2pDcXB6bHVqNTlTYmR6LzlrPSI+Cgk8L2ltYWdlPgo8L3N2Zz4= 32w" 
     onload="this.onload=null;this.sizes=Math.ceil(this.getBoundingClientRect().width/window.innerWidth*100)+'vw';" 
     sizes="1px" 
     src="/media/1/test.jpg"
     width="2048"> 
```

您可以在`srcset`属性中看到所有的图像变化。该属性中的最后一项是图像的模糊微小版本的内联版本。它是内联的，所以它可以立即显示，不需要额外的网络请求。

注意，我们将`sizes`属性设置为`1px`。这将使浏览器默认选择模糊的版本。`onload`中的一点点 JavaScript 负责在下载后用真实版本替换模糊版本。一个很酷的副作用是，如果你让你的浏览器窗口变大，它会尝试下载新窗口尺寸的图像。一旦下载完毕，它就会显示更大的图片。

如果您想查看这种行为的示例，请将您的浏览器指向我们文档中的[这一页](https://docs.spatie.be/laravel-medialibrary/demo/responsive-images)。

媒体库使快速响应图像+渐进式图像加载变得非常容易。你唯一要做的就是在添加媒体时使用`withResponsiveImages()`，然后在一个刀片视图中输出一个媒体对象。希望这个功能真的能有所作为。我迫不及待地想看到它在野外被使用。

## V7 新增:多文件下载

在媒体库中，每个文件都与一个`Media`模型相关联。要下载单个文件，您可以在控制器中进行:

```
public function download(Media $media) 
{
   return response()->download($media->getPath());
} 
```

因为`Media` [实现了](https://github.com/spatie/laravel-medialibrary/blob/ac8b8df1d2266260a20dc181b528050e09b1c9b4/src/Media.php#L236-L242) [Laravel 的 Responsable 接口](https://laravel-news.com/laravel-5-5-responsable)你也可以把那个写的稍微短一点:

```
public function download(Media $media) 
{
   return $media;
} 
```

相当甜蜜！但是如果你想一次下载多个文件呢？

Medialibrary v7 包含了一个新的`ZipStreamResponse`类，允许你用一个流来响应。文件将被动态压缩，你甚至可以包含来自多个文件系统的文件。

让我们来看一个如何使用`ZipStreamResponse`的例子。我们将创建两条路线。访问`add-files`会给我们的媒体库添加一些文件，访问`download-files`会下载它们。我在这里使用路线是为了演示，在现实世界的应用程序中，你可能会在控制器中使用`ZipStreamResponse`。

```
Route::get('add-files', function() {
    //create a regular model
    $article = Article::create();

    // add a file to the downloads collection on the local disk
    $article
        ->addMedia($pathToAFile)
        ->toMediaCollection('downloads');

    // add a file to the downloads collection on the s3 disk
    $article
    ->addMedia($pathToABigFile)
    ->toMediaCollection('downloads', 's3');

    return 'files added!';
});

Route::get('download-files', function() {
    //get all files in the download collection
    $allMedia = Article::first()->getMedia('downloads');

    // download them in a streamed way, so no prob if your files are very large
    return ZipStreamResponse::create('my-files.zip')->addMedia($allMedia);
}); 
```

最后一行是最重要的一行。zip 是动态创建的，它从本地磁盘和 s3 中提取数据，这有点酷。

编写代码比我想象的要容易。maennchen/zipstream-php 做了创建 zip 流的艰苦工作。我需要做的就是将提供的`ZipStream`类集成到我们的媒体库中。这里是`Spatie\MediaLibrary\ZipStreamResponse` :
的完整源代码

```
namespace Spatie\MediaLibrary;

use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Contracts\Support\Responsable;
use Spatie\MediaLibrary\Media;
use Symfony\Component\HttpFoundation\StreamedResponse;
use ZipStream\ZipStream;

class ZipStreamResponse implements Responsable
{
    /** string */
    protected $zipName;

    /** Illuminate\Support\Collection */
    protected $mediaItems;

    public static function create(string $zipName)
    {
        return new static($zipName);
    }

    public function __construct(string $zipName)
    {
        $this->zipName = $zipName;
    }

    public function addMedia($mediaItems)
    {
        $this->mediaItems = $mediaItems;

        return $this;
    }

    public function toResponse($request)
    {
        return new StreamedZipResponse(function () {
            $zip = new ZipStream($this->zipName);

            $this->mediaItems->each(function (Media $media) use ($zip) {
                $zip->addFileFromStream($media->file_name, $media->stream());
            });

            $zip->finish();
        });
    }
} 
```

当然，有些情况下(例如，当相同的资源被反复下载时，或者当下载速度非常重要时)，您仍然希望在本地创建一个 zip 文件并存储起来以备后用。但是我相信`StreamedZipResponse`确实为众所周知的 80%提供了一个好的解决方案。

## V7 中的新功能:增压媒体收藏

当前版本的媒体库中已经存在媒体集合。它们允许您将不同类型的文件放入它们自己的集合中。

我们来联想一些媒体:

```
$yourModel
   ->addMedia($pathToImage)
   ->toMediaCollection('images');

$yourModel
   ->addMedia($pathToAnotherImage)
   ->toMediaCollection('images');

$newsItem
   ->addMedia($pathToPdfFile)
   ->toMediaCollection('downloads');

$newsItem
  ->addMedia($pathToAnExcelFile)
  ->toMediaCollection('downloads'); 
```

特定集合中的所有媒体都可以这样检索:

```
// will return media instances for all files in the images collection
$yourModel->getMedia('images');

// will return media instances for all files in the downloads collection
$yourModel->getMedia('downloads'); 
```

在 v7 中，媒体收藏不仅仅是对文件进行分组的名称。通过在您的模型中定义一个媒体集合，您可以将某些行为添加到集合中。

要开始使用媒体收藏，将一个名为`registerMediaCollections`的函数添加到[您准备好的模型](https://docs.spatie.be/laravel-medialibrary/v7/basic-usage/preparing-your-model)中。在该功能中，您可以使用`addMediaCollection`来启动媒体收藏。

```
// in your model

public function registerMediaCollections()
{
    $this->addMediaCollection('my-collection')
        //add options
        ...

    // you can define as much collections as needed
    $this->addMediaCollection('my-other-collection')
        //add options
        ...
} 
```

您可以向`acceptsFile`传递一个回调，它将检查一个文件是否被允许进入集合。在这个例子中，我们只接受`jpeg`文件:

```
use Spatie\MediaLibrary\File;
...
public function registerMediaCollections()
{
    $this
        ->addMediaCollection('only-jpegs-please')
        ->acceptsFile(function (File $file) {
            return $file->mimeType === 'image/jpeg';
        });
} 
```

这会成功:

```
$yourModel
  ->addMedia('beautiful.jpg')
  ->toMediaCollection('only-jpegs-please'); 
```

这将抛出一个`Spatie\MediaLibrary\Exceptions\FileCannotBeAdded\FileUnacceptableForCollection`异常:

```
$yourModel
   ->addMedia('ugly.ppt')
   ->toMediaCollection('only-jpegs-please'); 
```

您可以使用媒体收藏来确保添加到收藏中的文件自动添加到某个磁盘。

```
// in your model

public function registerMediaCollections()
{
    $this
       ->addMediaCollection('big-files')
       ->useDisk('s3');
} 
```

向`my-collection`添加文件时，该文件将被存储在`s3`磁盘上。

```
$yourModel->addMedia($pathToFile)->toMediaCollection('big-files'); 
```

添加介质时，您仍然可以手动指定磁盘名。在本例中，文件将存储在`alternative-disk`而不是`s3`。

```
$yourModel
   ->addMedia($pathToFile)
   ->toMediaCollection('big-files', 'alternative-disk'); 
```

如果您希望一个集合只包含一个文件，您可以在集合上使用`singleFile`。一个很好的用例是在一个`User`模型上收集头像。在大多数情况下，你会希望一个用户只有一个`avatar`。

```
// in your model

public function registerMediaCollections()
{
    $this
        ->addMediaCollection('avatar')
        ->singleFile();
} 
```

第一次向收藏中添加文件时，它将照常存储。

```
$yourModel
   ->add($pathToImage)
   ->toMediaCollection('avatar');

$yourModel->getMedia('avatar')->count(); // returns 1

$yourModel->getFirstUrl('avatar'); // will return an url to the `$pathToImage` file 
```

将另一个文件添加到单个文件集合时，第一个文件将被删除。

```
// this will remove other files in the collection
$yourModel
   ->add($anotherPathToImage)
   ->toMediaCollection('avatar');

$yourModel->getMedia('avatar')->count(); // returns 1

$yourModel->getFirstUrl('avatar'); // will return an url to the `$anotherPathToImage` file 
```

在总体介绍中，我提到过你可以使用`registerMediaConversions`来定义媒体转换。但是，图像转换也可以在媒体集合中注册。

```
public function registerMediaCollections()
{
    $this
        ->addMediaCollection('my-collection')
        ->registerMediaConversions(function (Media $media) {
            $this
                ->addMediaConversion('thumb')
                ->width(100)
                ->height(100);
        });
} 
```

向`my-collection`添加图像时，将创建一个适合 100x100 的缩略图。

```
$yourModel->add($pathToImage)->toMediaCollection('my-collection');

$yourModel->getFirstMediaUrl('thumb') // returns an url to a 100x100 version of the added image. 
```

## 在关闭

除了上述功能，我们还对代码进行了一些重构，改进了测试套件，并完善了一些功能:

*   将文件添加到媒体库时，文件现在会变成小写。
*   出于搜索引擎优化的目的，转换后的图像现在会在文件名中包含原始文件的文件名。
*   `move`和`copy`方法已经被添加到`Media`中，因此你可以很容易地在模型之间移动媒体。

有很多功能在这篇博文中没有涉及到，比如[图像生成器](https://docs.spatie.be/laravel-medialibrary/v7/converting-other-file-types/using-image-generators)，使用[自定义目录结构](https://docs.spatie.be/laravel-medialibrary/v7/advanced-usage/using-a-custom-directory-structure)，[优化图像](https://docs.spatie.be/laravel-medialibrary/v7/converting-images/optimizing-converted-images)，[使用自己的模型](https://docs.spatie.be/laravel-medialibrary/v7/advanced-usage/using-your-own-model)，...请阅读[软件包的文档](https://docs.spatie.be/laravel-medialibrary)来了解这一切。

在接下来的几个月中，我们计划在包中添加一些 Vue 组件。这些组件将使上传内容到媒体库变得容易。我们还将添加能够管理集合中上传的媒体的组件。

最初这些组件是 v7 的一部分。我们需要更多的时间来完成它们。也许我们能够添加组件，而不必引入突破性的变化。

我希望你喜欢这个 v7 新特性的概要。就我个人而言，我迫不及待地想在我们的新项目中使用它。

Medialibrary 并不是我们 Spatie 团队制作的唯一软件包。请务必查看我们公司网站上列出的[所有其他包](https://spatie.be/en/opensource)。如果我们的任何产品进入您的生产环境，别忘了[给我们寄张明信片](https://github.com/spatie/laravel-medialibrary#postcardware)。