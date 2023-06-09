# 查找 PHP 程序中的内存问题

> 原文：<https://dev.to/tddenbraber/finding-memory-issues-in-php-programs-2i50>

# 查找 PHP 程序中的内存问题

"致命错误:允许的内存大小 2097152 字节已用尽(试图分配 528384 字节)."如果这个错误听起来很熟悉，这篇文章是为你写的。这个消息的问题是它没有告诉你太多:它没有告诉你所有的内存被分配到哪里。在大型复杂的系统中，找到消耗大量内存的位置并不容易。幸运的是，有一些工具可以帮助找到有问题的代码。在这篇文章中，我们将介绍两种在程序中寻找大量内存分配位置的方法。

## 运行示例

我们将使用下面的代码作为运行示例。代码的目的是寻找尼莫。有两个函数:一个读取 nemo 可能所在的文件，另一个试图找到一行内容等于' Nemo '的内容。这个片段的问题是它有时会消耗太多内存。并非总是如此，但对于某些文件，程序会崩溃。

```
<?php
function fetch_data_from_file(string $file_path) : iterable {
    $resource = fopen($file_path, 'r');
    $lines = [];
    while (($line = fgets($resource)) !== false) {
        $lines[] = trim($line);
    }
    return $lines;
}

function finding_nemo(string $filepath) : int {
    $lines = fetch_data_from_file($filepath);
    foreach ($lines as $line_number => $line) {
        if ($line === "nemo") {
            return $line_number;
        }
    }
    return -1; //nemo not found
} 
```

解决这个问题并不需要所有描述的技术和工具(你已经发现问题了吗？)，但它使我们能够看到那些工具和技术在实践中是如何工作的。

### memory_get_usage()

PHP 有两个函数可以告诉你程序的内存使用情况:`memory_get_usage`和`memory_get_peak_usage`。
`memory_get_usage`仅在*中提供了在函数调用时有多少*内存在使用的信息。`memory_get_peak_usage()`返回函数调用前程序分配的最大字节数。这两个函数都有一个`boolean`参数:`$real_usage`。如果`$real_usage`设置为`true`，`memory_get_usage`返回操作系统实际分配的内存总量，但其中一些可能(还)没有被您的程序使用。如果设置为`false`，则返回 PHP 从操作系统请求(和接收)的字节数，以及程序实际使用的字节数。以下说法始终成立:`memory_get_usage(true) >= memory_get_usage(false)`。内存是以块为单位请求的，这些块并不总是被完全使用。

使用这些功能的一个优点是它们非常容易使用。发现内存泄漏的一种可能的方法是在代码中分散对`memory_get_usage`的调用，并记录其输出。然后，您可以尝试找到一种模式:内存使用在哪里增加？
这些函数的一个缺点是它们的使用受到限制，因为它们不能洞察哪些函数或类使用了所有的内存。

让我们使用这些函数来了解一下我们当前的问题可能出在哪里。在下面的例子中，我使用了标记字符，如“A”、“B”等。为了能够跟踪日志条目到代码中的某个位置。另一个选择是在日志输出中包含[【神奇常数】](http://php.net/manual/en/language.constants.predefined.php)，比如`__FILE__`和`__LINE__`。

```
<?php

function fetch_data_from_file(string $file_path) : iterable {
    error_log(sprintf("A: %d bytes used\n", memory_get_usage()));
    /** original code... **/
    error_log(sprintf("B: %d bytes used\n", memory_get_usage());
    return $lines;
}

function finding_nemo(string $filepath) : int {
    error_log(sprintf("C: %d bytes used\n", memory_get_usage()));
    /** original code... **/
    error_log(sprintf("D: %d bytes used\n", memory_get_usage()));
    return -1; //nemo not found
}

finding_nemo("the_sea.txt"); 
```

当我们现在运行我们的例子时，我们有如下的日志输出:

```
C: 406912 bytes used
A: 406912 bytes used
B: 3599952 bytes used
D: 3591384 bytes used 
```

这很有趣:直到行标记 A，没有问题。在 A 线和 B 线之间，内存突然开始增加。这些标记对应于`fetch_data_from_file`功能的开始和结束。让我们试着用另一种方法来证实这个假设。

### Xdebug profiler

作为一名 PHP 程序员，您可能听说过(并使用过)Xdebug。如果你还没有，检查一下，并确保安装它。你可能不知道的是，它还附带了一个[分析器](https://en.wikipedia.org/wiki/Profiling_(computer_programming)):一个提供程序运行时行为洞察的工具。这个分析器比前面提到的 PHP 函数要复杂得多:它不仅仅提供关于使用了多少内存的信息，还提供了关于哪些函数实际上分配了内存的信息。这相对于前面的技术是一个优势，因为如果你真的不知道在哪里寻找你的内存问题，你将不得不在你的代码库中分散大量的对`memory_get_usage`的调用。在能够使用 profiler 之前，需要在您的`php.ini`中配置一些东西。注意，这些选项中的大部分不能在运行时使用`ini_set`来设置。
首先，您必须启用分析器。有两种方法可以做到这一点:要么使用`xdebug.profiler_enable = 1`，要么使用`xdebug.profiler_trigger_enable = 1`。使用第一个选项时，每次运行程序时，都会为*生成一个配置文件。第二个选项仅在有一个名为`XDEBUG_PROFILER`的`GET` / `POST`变量或`COOKIE`集合的情况下创建您正在运行的程序的配置文件。您还必须使用`xdebug.profiler_output_dir`告诉 Xdebug 它必须将生成的文件存储在哪里。
还有[更多的东西](https://xdebug.org/docs/profiler)要配置，但是有了这些设置你就已经可以开始了。*

现在，在启用 Xdebug profiler 的情况下再次运行脚本。如果我们查看我们配置的输出目录，我们可以在那里找到生成的概要文件。然而，在我们打开它之前，我们需要另一个工具:【Windows 版的 qCachegrind 或者 Linux 版的 [kCachegrind](https://kcachegrind.github.io/html/Home.html) 。我现在将使用 qCachegrind。

当用 qCachegrind 打开配置文件时，您会看到类似下图的内容。

[![qCachgegrind screenshot](img/1204a703c9e08c3a2bf7e44429948e23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2jY2-T3S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vkj9aj9xdw3zegfwzoq1.PNG)

确保在窗口顶部的下拉菜单中选择“内存”，而不是“时间”(如果性能有问题，此选项会很有用)。
当查看函数列表中`{main}`条目的“被调用者映射”时，可以从块的大小看出被调用的函数是如何分配内存的。较大的块是最有趣的:这些是分配最多内存的函数。在被调用者映射中，每个被调用的函数都位于调用者内部。
在左侧的“平面轮廓”部分，您可以看到功能列表。对于每个函数，都有一个“包含”和一个“自身”列。“Incl”表示由该函数分配的内存量，包括由该函数的被调用方分配的所有内存。“Self”显示由函数本身分配的内存。
最有趣的函数是那些在“Self”列中具有相对较高值的函数。
正如我们所见，有两个函数本身占用了大量内存:`php:fgets`和`php:trim`。但是等等...`trim()`一次只修剪一行，`fgets`一次只读取一行，对吗？为什么这些函数会占用这么多内存？在这里，我们看到了 Xdebug profiler 的一个缺点:它生成了一个“累积内存配置文件”，也就是说，当一个函数被调用多次时，它会显示在它被调用的不同时间内使用的所有内存的总和。

尽管 Xdebug 配置文件有其缺点，但它使我们能够看到(潜在的)大量内存被分配到哪里。我们可以证实我们的假设，即`fetch_data_from_file`似乎有问题，因为这个函数调用了两个分配了大量内存的 PHP 函数。

## 修复脚本

请注意，分析器或记录内存使用情况的工具几乎不会给出内存问题的确切答案。手动分析将永远是您调试过程的一部分。然而，工具*做*帮助你建立一个问题可能在哪里的想法。此时，我们知道哪个函数可能有问题。进一步分析`fetch_data_from_file`函数，我们可以看到它使用一个数组来缓冲整个文件。如果文件很大，程序将耗尽内存。现在我们有足够的信息来修复它。
让我们假设`fetch_data_from_file`也在其他地方使用，并且它的行为不会改变。幸运的是，这个问题有一个解决方案:我们实际上不必加载完整的文件。

解决这个问题的一个相对简单的方法是使用一个[生成器](http://php.net/manual/en/language.generators.overview.php)。这篇[的精彩文章](https://blog.ircmaxell.com/2012/07/what-generators-can-do-for-you.html)更详细地描述了这个概念。

简而言之，生成器使您能够编写一个基本的迭代器，您可以控制内存中需要什么信息。在迭代器上循环时，循环控制何时从迭代器中获取下一项。由于迭代器知道如何获取下一项，它不需要将所有项都存储在内存中。在这个例子中，这意味着一次只有一行文件在内存中。

让我们看看上面的示例代码，带有一个生成器:

```
<?php

function fetch_data_from_file(string $file_path) : iterable {
    $resource = fopen($file_path, 'r');
    while (($line = fgets($resource)) !== false) {
        yield trim($line);
    }
}

function finding_nemo(string $filepath) : int {
    $lines = fetch_data_from_file($filepath);
    foreach ($lines as $line_number => $line) {
        if ($line === "nemo") {
            return $line_number;
        }
    }
    return -1; //nemo not found
} 
```

有趣的是，`finding_nemo`函数不需要改变:`foreach`循环对生成器没有问题。`fetch_data_from_file`函数确实发生了变化:它现在包含了`yield`语句。

当我们记录这段代码的内存使用情况时，我们可以看到使用量一直很低。但是，因为 Xdebug 生成一个累积的内存配置文件，所以 Xdebug 配置文件看起来或多或少是一样的。这是因为总的来说，`fetch_data_from_file`函数确实分配了相同数量的内存。但是，该函数现在可以更快地释放分配给它的内存，导致内存使用量总体上比以前的版本低得多。这是使用 Xdebug 探查器的缺点之一。在后续的文章中，我将展示如何使用 [php-memory-profiler](https://github.com/arnaud-lb/php-memory-profiler/) ，它生成另一种类型的内存概要。

## 结论

在这篇文章中，我们看到了两种定位 PHP 程序中分配了大量内存的位置的方法:首先，使用 PHP 的`memory_get_usage`函数，然后使用 Xdebug 生成一个内存配置文件，并使用 qCachegrind 对其进行分析。需要记住的一点是，没有任何工具或技术可以不确定地指出问题所在。因此，您的调试过程将总是至少部分包含手动分析。在下一篇文章中，我将展示 [php 内存分析器](https://github.com/arnaud-lb/php-memory-profiler/)如何帮助你找到程序中的内存泄漏。

*最初发布于[莫 xio 公司博客](https://www.moxio.com/blog/33/)。*