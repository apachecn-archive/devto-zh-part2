# 在幕后:Ruby 中的“啧啧”和流式文件

> 原文：<https://dev.to/appsignal/under-the-hood-slurping-and-streaming-files-in-ruby-h24>

在这一期的 Ruby Magic 中，我们将了解 Ruby 中的流文件，`IO`类如何处理读取文件而不完全加载到内存中，以及它如何通过缓冲读取字节来读取每行文件。让我们开始吧！

## 【啧啧有声】和流式文件

Ruby 的`File.read`方法读取一个文件并返回其全部内容。

```
irb(main):001:0>  content = File.read("log/production.log")
=> "I, [2018-06-27T16:45:02.843719 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Started GET \"/articles\" for 127.0.0.1 at 2018-06-27 16:45:02 +0200\nI, [2018-06-27T16:45:02.846719 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Processing by ArticlesController#index as HTML\nI, [2018-06-27T16:45:02.848212 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Rendering articles/index.html.erb within layouts/application\nD, [2018-06-27T16:45:02.850020 #9098] DEBUG -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Article Load (0.3ms)  SELECT \"articles\".* FROM \"articles\"\nI, [2018-06-27T16:45:02.850901 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Rendered articles/index.html.erb within layouts/application (1.7ms)\nI, [2018-06-27T16:45:02.851633 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Completed 200 OK in 5ms (Views: 3.4ms | ActiveRecord: 0.3ms)\n" 
```

Enter fullscreen mode Exit fullscreen mode

在内部，这将打开文件，读取其内容，关闭文件，并将内容作为单个字符串返回。通过一次“吮吸”文件的内容，它会一直保存在内存中，直到被 Ruby 的垃圾收集器清理掉。

举个例子，假设我们想将一个文件中的所有字符都大写，并将其写入另一个文件。使用`File.read`，我们可以获取内容，在结果字符串上调用`String#upcase`，并将大写字符串传递给`File.write`。

```
irb>  upcased = File.read("log/production.log").upcase
=> "I, [2018-06-27T16:45:02.843719 #9098]  INFO -- : [86A5D18C-19DD-4CBF-9D7A-461C79E98C22] STARTED GET \"/ARTICLES\" FOR 127.0.0.1 AT 2018-06-27 16:45:02 +0200\nI, [2018-06-27T16:45:02.846719 #9098]  INFO -- : [86A5D18C-19DD-4CBF-9D7A-461C79E98C22] PROCESSING BY ARTICLESCONTROLLER#INDEX AS HTML\nI, [2018-06-27T16:45:02.848212 #9098]  INFO -- : [86A5D18C-19DD-4CBF-9D7A-461C79E98C22]   RENDERING ARTICLES/INDEX.HTML.ERB WITHIN LAYOUTS/APPLICATION\nD, [2018-06-27T16:45:02.850020 #9098] DEBUG -- : [86A5D18C-19DD-4CBF-9D7A-461C79E98C22]   ARTICLE LOAD (0.3MS)  SELECT \"ARTICLES\".* FROM \"ARTICLES\"\nI, [2018-06-27T16:45:02.850901 #9098]  INFO -- : [86A5D18C-19DD-4CBF-9D7A-461C79E98C22]   RENDERED ARTICLES/INDEX.HTML.ERB WITHIN LAYOUTS/APPLICATION (1.7MS)\nI, [2018-06-27T16:45:02.851633 #9098]  INFO -- : [86A5D18C-19DD-4CBF-9D7A-461C79E98C22] COMPLETED 200 OK IN 5MS (VIEWS: 3.4MS | ACTIVERECORD: 0.3MS)\n"
irb>  File.write("log/upcased.log", upcased)
=> 896 
```

Enter fullscreen mode Exit fullscreen mode

虽然这对于小文件有效，但是在处理大文件时，将整个文件读入内存可能会有问题。例如，当解析一个 14gb 的日志文件时，一次读取整个文件将是一个开销很大的操作。文件的内容保存在内存中，因此应用程序的内存占用量会大幅增加。这最终会导致内存交换和操作系统杀死应用程序的进程。

幸运的是，Ruby 允许使用`File.foreach`逐行读取文件。它不是一次读取文件的全部内容，而是对每一行执行一个传递的块。

它的结果是[可枚举的](https://blog.appsignal.com/2018/05/29/ruby-magic-enumerable-and-enumerator.html)，因此它或者为每一行产生一个块，或者如果没有块被传递，则返回一个枚举器对象。这样就可以读取更大的文件，而不必一次将所有内容都加载到内存中。

```
irb>  File.foreach("log/production.log") { |line| p line }
"I, [2018-06-27T16:45:02.843719 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Started GET \"/articles\" for 127.0.0.1 at 2018-06-27 16:45:02 +0200\n"
"I, [2018-06-27T16:45:02.846719 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Processing by ArticlesController#index as HTML\n"
"I, [2018-06-27T16:45:02.848212 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Rendering articles/index.html.erb within layouts/application\n"
"D, [2018-06-27T16:45:02.850020 #9098] DEBUG -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Article Load (0.3ms)  SELECT \"articles\".* FROM \"articles\"\n"
"I, [2018-06-27T16:45:02.850901 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Rendered articles/index.html.erb within layouts/application (1.7ms)\n"
"I, [2018-06-27T16:45:02.851633 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Completed 200 OK in 5ms (Views: 3.4ms | ActiveRecord: 0.3ms)\n" 
```

Enter fullscreen mode Exit fullscreen mode

要大写整个文件，我们从输入文件逐行读取，将其大写，并将其附加到输出文件。

```
irb>  File.open("upcased.log", "a") do |output|
irb*  File.foreach("production.log") { |line| output.write(line.upcase) }
irb>  end
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

那么，如何逐行读取一个文件而不必先读取整个文件呢？为了理解这一点，我们必须剥开读取文件周围的一些层。让我们仔细看看 Ruby 的`IO`类。

## I/O 和 Ruby 的`IO`类

尽管`File.read`和`File.foreach`存在，但是`File`类的[文档并没有列出它们。事实上，你不会在`File`类文档中找到任何文件读写方法，因为它们是从父`IO`类继承的。](https://ruby-doc.org/core-2.5.1/File.html)

> ## 输入输出
> 
> *I/O 设备*是一种向或从计算机传输数据的设备，例如键盘、显示器和硬盘驱动器。它通过读取或产生数据流来执行 [*输入/输出*](https://en.wikipedia.org/wiki/Input/output) ，或*输入/输出*。
> 
> 从硬盘读写文件是你会遇到的最常见的 I/O。其他类型的 I/O 包括套接字通信、记录终端输出和键盘输入。

Ruby 中的`IO`类处理所有的输入和输出，比如读写文件。因为读取文件与从任何其他 I/O 流中读取没有什么不同，`File`类直接继承了像`IO.read`和`IO.foreach`这样的方法。

```
irb>  IO.foreach("log/production.log") { |line| p line }
"I, [2018-06-27T16:45:02.843719 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Started GET \"/articles\" for 127.0.0.1 at 2018-06-27 16:45:02 +0200\n"
"I, [2018-06-27T16:45:02.846719 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Processing by ArticlesController#index as HTML\n"
"I, [2018-06-27T16:45:02.848212 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Rendering articles/index.html.erb within layouts/application\n"
"D, [2018-06-27T16:45:02.850020 #9098] DEBUG -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Article Load (0.3ms)  SELECT \"articles\".* FROM \"articles\"\n"
"I, [2018-06-27T16:45:02.850901 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22]   Rendered articles/index.html.erb within layouts/application (1.7ms)\n"
"I, [2018-06-27T16:45:02.851633 #9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Completed 200 OK in 5ms (Views: 3.4ms | ActiveRecord: 0.3ms)\n" 
```

Enter fullscreen mode Exit fullscreen mode

`File.foreach`相当于`IO.foreach`，因此`IO`类版本可以用来获得与我们之前所做的相同的结果。

## 通过内核读取 I/O 流

在内部，Ruby 的`IO`类的读写能力基于围绕[内核系统调用](https://en.wikipedia.org/wiki/System_call)的抽象。操作系统的[内核](https://en.wikipedia.org/wiki/Kernel_(operating_system))负责读取和写入 I/O 设备。

### 打开文件

通过请求内核在文件表中放置一个文件的引用并在进程的文件描述符表中创建一个文件描述符来打开一个文件。

> ## 文件描述符和文件表
> 
> [打开](https://en.wikipedia.org/wiki/Open_(system_call))一个文件返回一个[文件描述符](https://en.wikipedia.org/wiki/File_descriptor)——一个用来访问 I/O 资源的整数。
> 
> 每个进程都有自己的文件描述符表来保存内存中的文件描述符，每个描述符都指向系统级*文件表*中的一个条目。
> 
> 为了读取或写入 I/O 资源，进程通过系统调用将文件描述符传递给内核。然后内核代表进程访问文件，因为进程不能访问文件表。

打开文件将*而不是*把它们的内容保存在内存中，但是文件描述符表会被填满，所以在打开文件后总是关闭它们是一个好习惯。像`File.read`那样包装`File.open`的方法会自动完成这项工作，那些采用块的方法也是如此。

在这个例子中，我们将更进一步，直接调用`IO.sysopen`方法。通过传递文件名，该方法创建了一个文件描述符，我们可以用它来引用以后打开的文件。

```
irb>  IO.sysopen("log/production.log")
=> 9 
```

Enter fullscreen mode Exit fullscreen mode

为了创建一个供 Ruby 读写的`IO`实例，我们将文件描述符传递给`IO.new`

```
irb>  file_descriptor = IO.sysopen("log/production.log")
=> 9
irb>  io = IO.new(file_descriptor)
=> #<IO:fd 9> 
```

Enter fullscreen mode Exit fullscreen mode

要关闭一个 I/O 流并从 files 表中删除对该文件的引用，我们在`IO`实例上调用`IO#close`。

```
irb> io.close
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

### 读取字节和移动光标

`IO#sysread`从`IO`对象中读取一些字节。

```
irb>  io.sysread(64)
=> " [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Started GET \"/articles\" " 
```

Enter fullscreen mode Exit fullscreen mode

这个例子使用了我们之前通过将文件描述符整数传递给`IO.new`而创建的`IO`实例。它通过以 64 为参数调用`IO#sysread`从文件中读取并返回前 64 个字节。

```
irb>  io.sysread(64)
=> "for 127.0.0.1 at 2018-06-27 16:45:02 +0200\nI, [2018-06-27T16:45:" 
```

Enter fullscreen mode Exit fullscreen mode

我们第一次从文件中请求字节时，光标是自动移动的，所以在同一个实例上再次调用`IO#sysread`将产生文件的下一个 64 字节。

### 移动光标

`IO.sysseek`手动将光标移动到文件中的一个位置。

```
irb>  io.sysseek(32)
=> 32
irb>  io.sysread(64)
=> "9098]  INFO -- : [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Started "
irb>  io.sysseek(0)
=> 0
irb>  io.sysread(64)
=> " [86a5d18c-19dd-4cbf-9d7a-461c79e98c22] Started GET \"/articles\" " 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们移动到位置 32，然后使用`IO#sysread`读取 64 个字节。通过用 0 再次调用`IO.sysseek`，我们跳回到文件的开头，允许我们再次读取前 64 个字节。

## 逐行读取文件

现在，我们知道了`IO`类的便利方法如何打开 IO 流，从其中读取字节，以及它们如何移动光标的位置。

像`IO.foreach`和`IO#gets`这样的方法可以逐行请求行，而不是按字节数。没有一种有效的方法来寻找下一个新行并获取该位置之前的所有字节，所以 Ruby 需要负责拆分文件的内容。

```
class MyIO
  def initialize(filename)
    fd = IO.sysopen(filename)
    @io = IO.new(fd)
  end

  def each(&block)
    line = ""

    while (c = @io.sysread(1)) != $/
      line << c
    end

    block.call(line)
    each(&block)
  rescue EOFError
    @io.close
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个示例实现中，`#each`方法使用`IO#sysread`从文件中一次获取一个字节，直到该字节为`$/`，表示换行。当它发现一个新行时，它停止获取字节，并使用该行调用传递的块。

这种解决方案是可行的，但是效率很低，因为它为文件中的每个字节调用`IO.sysread`。

### 缓冲文件内容

Ruby 通过保存文件内容的内部缓冲区更聪明地做到了这一点。它不是一次读取一个字节，而是一次读取 512 个字节，并检查返回的字节中是否有换行符。如果有，它将返回换行符之前的部分，并将其余部分作为缓冲区保存在内存中。如果缓冲区不包含换行符，它会再获取 512 个字节，直到找到一个。

```
class MyIO
  def initialize(filename)
    fd = IO.sysopen(filename)
    @io = IO.new(fd)
    @buffer = ""
  end

  def each(&block)
    @buffer << @io.sysread(512) until @buffer.include?($/)

    line, @buffer = @buffer.split($/, 2)

    block.call(line)
    each(&block)
  rescue EOFError
    @io.close
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`#each`方法以 512 字节的块向内部`@buffer`变量添加字节，直到`@buffer`变量包含一个换行符。当这种情况发生时，它通过第一个换行符分割缓冲区。第一部分是`line`，第二部分是新的缓冲区。

然后用该行调用传递的块，剩下的`@buffer`保留下来供下一个循环使用。

通过缓冲文件的内容，在将文件划分为逻辑块时，I/O 调用的数量减少了。

## 流式文件

总而言之，流式文件的工作原理是要求操作系统的内核打开一个文件，然后一点一点地从中读取字节。当在 Ruby 中每行读取一个文件时，每次从文件中取出 512 个字节的数据，然后分成“行”。

这就结束了我们对 Ruby 中 I/O 和流文件的概述。我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你想了解 Ruby 的神奇之处，请不要犹豫，留下你的评论。