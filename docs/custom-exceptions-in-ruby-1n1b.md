# Ruby 中的自定义异常

> 原文：<https://dev.to/appsignal/custom-exceptions-in-ruby-1n1b>

不久前，我们讨论了 Ruby 中的[异常。这一次，我们将探索根据您的应用需求创建自定义异常的方法。](https://blog.appsignal.com/2018/03/13/exceptions-in-ruby.html)

假设我们有一个方法，可以处理图片的上传，同时只允许 100 千字节到 10 兆字节的 JPEG 图片。为了执行这些规则，每当图像违反这些规则时，我们都会引发一个异常。

```
class ImageHandler
  def self.handle_upload(image)
    raise "Image is too big" if image.size > 10.megabytes
    raise "Image is too small" if image.size < 100.kilobytes
    raise "Image is not a JPEG" unless %w[JPG JPEG].include?(image.extension)

    #… do stuff
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

每当用户上传不符合规则的图像时，我们的(Rails) web 应用程序就会显示默认的 Rails 502 错误页面，显示未捕获的错误。

```
class ImageUploadController < ApplicationController
  def upload
    @image = params[:image]
    ImageHandler.handle_upload(@image)

    redirect_to :index, :notice => "Image upload success!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Rails 通用错误页面并没有给用户提供太多帮助，所以让我们看看是否可以改进这些错误。我们有两个目标:当文件大小超出设定界限时通知用户，并通过返回一个`403`禁止状态码来防止黑客上传潜在的恶意(非 JPEG)文件。

## 自定义错误类型

Ruby 中的几乎所有东西都是对象，错误也不例外。这意味着我们可以从任何错误类中派生出子类，并创建自己的错误类。我们可以在我们的`handle_upload`方法中使用这些定制的错误类型来进行不同的验证。

```
class ImageHandler
  # Domain specific errors
  class ImageExtensionError < StandardError; end
  class ImageTooBigError < StandardError
    def message
      "Image is too big"
    end
  end
  class ImageTooSmallError < StandardError
    def message
      "Image is too small"
    end
  end

  def self.handle_upload(image)
    raise ImageTooBigError if image.size > 10.megabytes
    raise ImageTooSmallError if image.size < 100.kilobytes
    raise ImageExtensionError unless %w[JPG JPEG].include?(image.extension)

    #… do stuff
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们在处理程序中添加了三个从`StandardError`扩展而来的新类。对于图像尺寸错误，我们用一条可以显示给用户的错误消息覆盖了`StandardError`的`message`方法。在`handle_upload`方法中调用`raise`的方式也发生了变化，通过用不同的错误类型替换定制的`StandardError`消息，我们可以产生不同的、更具体的错误。

现在，我们可以在控制器中使用这些自定义错误类型来返回不同的错误响应。例如，我们可以返回特定的错误消息或特定的响应代码。

```
class ImageUploadController < ApplicationController
  def upload
    @image = params[:image]
    ImageHandler.handle_upload(@image)

    redirect_to :index, :notice => "Image upload success!"

  rescue ImageHandler::ImageTooBigError, ImageHandler::ImageTooSmallError => e
    render "edit", :alert => "Error: #{e.message}"

  rescue ImageHandler::ImageExtensionError
    head :forbidden
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这已经比使用标准的`raise`调用好多了。再多一点子类化，我们可以通过拯救整个错误组而不是单独拯救每个错误类型，使它更容易使用。

```
class ImageHandler
  class ImageExtensionError < StandardError; end
  class ImageDimensionError < StandardError; end
  class ImageTooBigError < ImageDimensionError
    def message
      "Image is too big"
    end
  end
  class ImageTooSmallError < ImageDimensionError
    def message
      "Image is too small"
    end
  end

  def self.handle_upload(image)
    raise ImageTooBigError if image.size > 10.megabytes
    raise ImageTooSmallError if image.size < 100.kilobytes
    raise ImageExtensionError unless %w(JPG JPEG).include?(image.extension)

    #… do stuff
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以拯救父类`ImageDimensionError`，而不是拯救每一个单独的图像维度异常。这将拯救我们的`ImageTooBigError`和`ImageTooSmallError`。

```
class ImageUploadController < ApplicationController
  def upload
    @image = params[:image]
    ImageHandler.handle_upload(@image)

    redirect_to :index, :notice => "Image upload success!"

  rescue ImageHandler::ImageDimensionError => e
    render "edit", :alert => "Error: #{e.message}"

  rescue ImageHandler::ImageExtensionError
    head :forbidden
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用自己的错误类最常见的情况是在编写 gem 时。mongo-ruby-driver gem 是使用自定义错误的一个很好的例子。每个可能导致异常的操作都有自己的异常类，这使得处理特定的用例以及生成清晰的异常消息和类变得更加容易。

使用自定义异常类的另一个优点是，当使用像 AppSignal 这样的异常监控工具时。这些工具让您更好地了解异常发生的位置，并在用户界面中对类似的错误进行分组。

如果你喜欢这篇文章，可以看看我们在 [AppSignal Academy](https://blog.appsignal.com/category/academy.html?utm_source=DevTo&utm_medium=DevToBlogPost&utm_campaign=DevToContentPosting_CustomExceptions) 上写的更多内容。AppSignal 致力于构建更好的应用。在我们的学院系列中，我们将探索应用程序的稳定性和性能，并解释核心编程概念。

我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你想了解 Ruby 的神奇之处，请不要犹豫，留下你的评论。