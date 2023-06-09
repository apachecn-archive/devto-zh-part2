# 用 Ruby 压缩一个文件

> 原文：<https://dev.to/philnash/gzip-a-file-in-ruby-ig7>

今年年初，我研究了如何更好地压缩 Jekyll 网站的输出。我很快就会把结果写出来。现在，这里是如何使用 Ruby 压缩一个文件。

## 输入 zlib

包含在 Ruby 标准库中的是 [`Zlib`模块](http://ruby-doc.org/stdlib-2.5.0/libdoc/zlib/rdoc/Zlib.html)，它提供了对底层 [zlib 库](https://zlib.net/)的访问。它用于读写 gzip 格式的文件。这里有一个小程序可以读入一个文件，压缩它并保存为 gzip 文件。

```
require "zlib"

def compress_file(file_name)
  zipped = "#{file_name}.gz"

  Zlib::GzipWriter.open(zipped) do |gz|
    gz.write IO.binread(file_name)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用任何 IO 或类似 IO 的对象与`Zlib::GzipWriter`。

我们可以通过将文件的原始名称添加到压缩输出中来增强这一点。此外，将文件的修改时间设置为与原始时间相同也是有益的，特别是当您使用带有 [nginx 的`gzip_static`模块](http://nginx.org/en/docs/http/ngx_http_gzip_static_module.html)的文件时。

```
require "zlib"

def compress_file(file_name)
  zipped = "#{file_name}.gz"

  Zlib::GzipWriter.open(zipped) do |gz|
    gz.mtime = File.mtime(file_name)
    gz.orig_name = file_name
    gz.write IO.binread(file_name)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了看看这有多有效，我们可以打印一些来自压缩的统计数据。

```
require "zlib"

def print_stats(file_name, zipped)
  original_size = File.size(file_name)
  zipped_size = File.size(zipped)
  percentage_difference = ((original_size - zipped_size).to_f/original_size)*100
  puts "#{file_name}: #{original_size}"
  puts "#{zipped}: #{zipped_size}"
  puts "difference - #{'%.2f' % percentage_difference}%"
end

def compress_file(file_name)
  zipped = "#{file_name}.gz"

  Zlib::GzipWriter.open(zipped) do |gz|
    gz.mtime = File.mtime(file_name)
    gz.orig_name = file_name
    gz.write IO.binread(file_name)
  end

  print_stats(file_name, zipped)
end 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以选择一个文件并用 Ruby 和 gzip 压缩它。对于这个快速测试，我选择了未压缩、未统一的 jQuery 版本 3.3.1。结果是:

```
./jquery.js: 271751
./jquery.js.gz: 80669
difference - 70.32% 
```

Enter fullscreen mode Exit fullscreen mode

相当不错，但是如果我们再努力一点，我们可以从中榨取更多。

## 压缩级别

`Zlib::GzipWriter`向`open`传递第二个参数，这是 zlib 应用的压缩级别。0 表示不压缩，9 表示可能的最佳压缩，从 0 到 9，在速度和压缩之间有一个折衷。默认压缩是速度和压缩之间的一个很好的折衷，但是如果时间对你来说不是一个问题，那么你最好使文件尽可能的小，特别是如果你想通过网络提供它。要将压缩级别设置为 9，可以使用整数，但是`Zlib`有一个方便的常数:`Zlib::BEST_COMPRESSION`。

将带有`Zlib::GzipWriter`的行改为:

```
Zlib::GzipWriter.open(zipped, Zlib::BEST_COMPRESSION) do |gz| 
```

Enter fullscreen mode Exit fullscreen mode

对 jQuery 再次运行该文件会得到:

```
./jquery.js: 271751
./jquery.js.gz: 80268
difference - 70.46% 
```

Enter fullscreen mode Exit fullscreen mode

相差 0.14 个百分点！好吧，不是一个巨大的胜利，但是如果生成文件的时间不重要，那么你也可以。对于更大的文件，差异会更大。

## 流媒体 gzip

你可能还想补充最后一点。如果你正在压缩非常大的文件，将它们全部加载到内存中并不是最有效的工作方式。但是 Gzip 是一种流格式，所以你可以一次写一大块。在这种情况下，您只需要读取您正在增量压缩的文件，并将块写入`GzipWriter`。下面是以 16kb 为单位读取文件的样子:

```
def compress_file(file_name)
  zipped = "#{file_name}.gz"

  Zlib::GzipWriter.open(zipped, Zlib::BEST_COMPRESSION) do |gz|
    gz.mtime = File.mtime(file_name)
    gz.orig_name = file_name
    File.open(file_name) do |file|
      while chunk = file.read(16*1024) do
        gz.write(chunk)
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## gzip 中的 Ruby

这就是如何使用 Ruby 和 zlib 压缩文件，以及如何添加额外的信息和控制压缩级别来平衡速度和最终文件大小。所有这些都放入了我最近创建的一个 gem 中，以便对 Jekyll 站点的输出使用最大限度的 gzip 压缩。这个 gem 叫做 [jekyll-gzip](https://github.com/philnash/jekyll-gzip) ,我很快会写更多关于它的内容，以及其他比 gzip 的 zlib 实现更好的工具。

<small>*标题图标:[钻石由爱德华船夫](https://thenounproject.com/term/diamond/315/)和[副由丹尼尔卢夫特](https://thenounproject.com/search/?q=vice&i=1554537)从名词项目。*</small>

* * *

*[Gzip 红宝石](https://philna.sh/blog/2018/02/25/gzip-file-ruby/)中的一个文件最初发布于 2018 年 2 月 25 日 [philna.sh](https://philna.sh) 。*