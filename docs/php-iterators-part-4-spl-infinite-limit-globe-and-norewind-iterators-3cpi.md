# PHP 迭代器第 4 部分:SPL 无限、极限、全局和无风迭代器

> 原文：<https://dev.to/webmobtuts/php-iterators-part-4-spl-infinite-limit-globe-and-norewind-iterators-3cpi>

继续我们的系列 **(PHP 迭代器)**来演示 SPL PHP 迭代器，我们将讨论无限迭代器、极限迭代器、全局迭代器和 noRewind 迭代器。

要查看之前的部分，请点击此处[第一部分](http://webmobtuts.com/backend-development/php-iterators-part-1-traversable-iterable-iterator-and-iterator-aggregate/)、[第二部分](http://webmobtuts.com/backend-development/php-iterators-part-2-spl-array-iterator-directory-iterator-and-filesystem-iterator/)、[第三部分](https://dev.to/webmobtuts/php-iterators-part-3-spl-filter-iterator-append-iterator-and-multiple-iterator-5c8f-temp-slug-9062051)

## 无限迭代器

无限迭代器有点像一个无限循环，允许执行需要一些递归的操作，或者当我们需要无限迭代迭代器而不停止时。

```
InfiniteIterator extends IteratorIterator implements OuterIterator { /\* Some methods \*/ public \_\_construct ( Iterator $iterator ) public void next ( void ) /\* Inherited methods \*/ public IteratorIterator::\_\_construct ( Traversable $iterator ) public mixed IteratorIterator::current ( void ) public Traversable IteratorIterator::getInnerIterator ( void ) public scalar IteratorIterator::key ( void ) public void IteratorIterator::rewind ( void ) } 
```

注意，当使用无限迭代器时，必须有一个终止条件来终止循环，否则循环将继续无限执行，浏览器将崩溃。

让我们看一个例子:

```
\<?php $programming\_languages = array('C++', 'PHP', 'C#', 'Java', 'Swift'); $iterator = new InfiniteIterator(new ArrayIterator($programming\_languages)); $counter = 0; echo "\<pre\>"; echo "\<ol\>"; foreach ($iterator as $value) { $counter++; print("\<li\>".$value.PHP\_EOL."\</li\>"); if($counter == 500) { break; } } echo "\</ol\>"; echo "\</pre\>"; 
```

在这个简单的例子中，我们创建了一个无限迭代器的新实例，将一个数组迭代器传递给构造函数，然后我们遍历它并显示每一项。之后，我们添加了这样的终止条件:

```
if($counter == 500) { break; } 
```

如果您删除终止条件，循环将继续，浏览器将崩溃。

## 极限迭代器

**极限迭代器**是另一种迭代器，总是与**无限迭代器**一起使用，它很像 mysql 数据库中的极限子句，换句话说，它允许迭代迭代器中的项目子集。

```
LimitIterator extends IteratorIterator implements OuterIterator { /\* Some methods \*/ public \_\_construct ( Iterator $iterator [, int $offset = 0 [, int $count = -1]] ) public mixed current ( void ) public Iterator getInnerIterator ( void ) public int getPosition ( void ) public mixed key ( void ) public void next ( void ) public void rewind ( void ) } 
```

**极限迭代器**构造函数有三个参数:

*   迭代器对象
*   起始偏移量，如果不包括，它将默认为 0。
*   要限制的项目数，如果不包括，它将默认为所有集合，直到结束。

举例说明极限迭代器:

```
\<?php // Create an iterator to be limited $programming\_languages = new ArrayIterator(array('C++', 'PHP', 'C#', 'Java', 'Swift')); // Loop starting from the third item and get 3 items echo "\<pre\>"; echo "\<ol\>"; foreach (new LimitIterator($programming\_languages, 2, 3) as $langauge) { print("\<li\>".$langauge.PHP\_EOL."\</li\>"); } echo "\</ol\>"; echo "\</pre\>"; echo "\<hr/\>"; // Loop starting from second item until the end // Note: offset starts from zero echo "\<pre\>"; echo "\<ol\>"; foreach (new LimitIterator($programming\_languages, 1) as $langauge) { print("\<li\>".$langauge.PHP\_EOL."\</li\>"); } echo "\</ol\>"; echo "\</pre\>"; 
```

如果你还记得上面我们说过**无限迭代器**需要一个终止条件，我们可以使用**极限迭代器**作为**无限迭代器**的终止条件，所以我们可以这样修改上面的例子:

```
\<?php $programming\_languages = array('C++', 'PHP', 'C#', 'Java', 'Swift'); $iterator = new InfiniteIterator(new ArrayIterator($programming\_languages)); echo "\<pre\>"; echo "\<ol\>"; foreach (new LimitIterator($iterator, 0, 500) as $value) { print("\<li\>".$value.PHP\_EOL."\</li\>"); } echo "\</ol\>"; echo "\</pre\>"; 
```

(adsbygoogle = window . adsbygoogle | |[])。推送({ })；

## 全局迭代器

顾名思义，glob 迭代器继承了 php 函数 [glob()](http://php.net/manual/en/function.glob.php) 的行为来遍历文件系统文件和目录。

```
GlobIterator extends FilesystemIterator implements SeekableIterator , Countable { /\* Some constants \*/ const integer FilesystemIterator::CURRENT\_AS\_PATHNAME = 32 ; const integer FilesystemIterator::CURRENT\_AS\_FILEINFO = 0 ; const integer FilesystemIterator::CURRENT\_AS\_SELF = 16 ; const integer FilesystemIterator::CURRENT\_MODE\_MASK = 240 ; /\* Some methods \*/ public \_\_construct ( string $pattern [, int $flags = FilesystemIterator::KEY\_AS\_PATHNAME | FilesystemIterator::CURRENT\_AS\_FILEINFO] ) public int count ( void ) /\* Some inherited methods \*/ public mixed FilesystemIterator::current ( void ) } 
```

如类结构所示，glob 迭代器从文件系统迭代器扩展而来，因此文件系统迭代器中的每个属性和方法也适用于 glob 迭代器。

glob 迭代器几乎类似于 glob()函数，如下例所示:

```
\<?php // using globe to list all jpg files foreach (glob("\*.jpg") as $filename) { echo "$filename size " . filesize($filename) . "\n"; } echo "\<hr/\>"; // using GlobIterator to list all jpg files $images = new GlobIterator("\*.jpg"); echo "\<pre\>"; echo "Number of images: " . $images-\>count() . PHP\_EOL; foreach ($images as $image) { echo $image . PHP\_EOL; } echo "\</pre\>"; 
```

如上例所示， **GlobIterator** 构造函数采用一种模式，以与 **glob()** 相同的方式搜索文件。

另一个检索所有文件以及其他各种信息的例子，如果您需要在 web UI 中构建一个文件管理器，这个例子很有用:

```
\<?php set\_time\_limit(0); // list all files $files = new GlobIterator("\*"); echo "\<pre\>"; echo "Number of files: " . $files-\>count() . "\n"; echo "\<ul\>"; foreach ($files as $file) { echo "\<li\>\<h2\>".$file-\>getFilename()."\</h2\>\<br/\> Extension: ".$file-\>getExtension()." Basename: ".$file-\>getBasename()." Size: ".$file-\>getSize()." bytes is Directory :".($file-\>isDir()?"yes":"no")." is File :".($file-\>isFile()?"yes":"no")." is Writable :".($file-\>isWritable()?"yes":"no")." is Readable :".($file-\>isReadable()?"yes":"no")." is Executable :".($file-\>isExecutable()?"yes":"no")." \<hr/\> \</li\>"; } echo "\</ul\>"; echo "\</pre\>"; 
```

注意，这里的一些方法可能会抛出这样的异常:

> ```
> Fatal error: Uncaught RuntimeException: SplFileInfo::getSize(): stat failed for 
> ```

要解决这个异常，你需要确保 php.ini 指令`upload_max_filesize`和`post_max_size`有一个大值，并且`post_max_size`必须大于`upload_max_filesize`。

## NoRewind Iterator

在* *NoRewind 迭代器** 中，Rewind 方法从未在迭代开始时调用，或者当它反转时调用，这意味着该迭代器从未回卷。

```
NoRewindIterator extends IteratorIterator { /\* Some methods \*/ public \_\_construct ( Iterator $iterator ) public mixed current ( void ) public iterator getInnerIterator ( void ) public mixed key ( void ) public void next ( void ) public void rewind ( void ) public bool valid ( void ) /\* Some inherited methods \*/ public IteratorIterator::\_\_construct ( Traversable $iterator ) public mixed IteratorIterator::current ( void ) } 
```

为了说明这种行为，请考虑以下示例:

```
\<?php set\_time\_limit(0); $places = new ArrayIterator(["USA", "Germany", "France", "Turkey", "England"]); $iterator = new NoRewindIterator($places); foreach ($iterator as $key =\> $value) { echo "Key: ".$key."----"; echo "Value: ".$value."\<br/\>"; } $iterator-\>rewind(); echo "\<hr/\>"; foreach ($iterator as $key =\> $value) { echo "Key: ".$key."----"; echo "Value: ".$value."\<br/\>"; } 
```

如果您运行上面的示例，您会注意到第一个 foreach 只执行和输出值，但是在调用$iterator->rewind()之后，什么也没有发生，这是因为 rewind 没有将循环光标重置在开始处。

```
\<?php \<?php $programming\_languages = array("PHP", "Java", "Go", "Swift"); $noRewindIterator = new NoRewindIterator(new ArrayIterator($programming\_languages)); echo $noRewindIterator-\>current() . "\n"; $noRewindIterator-\>next(); // now rewind the iterator (nothing should happen) $noRewindIterator-\>rewind(); echo $noRewindIterator-\>current() . "\n"; ?\> // output PHP Java 
```

## 结论

在本系列教程(PHP 迭代器)中，我们介绍了另外四种 SPL 迭代器，无限执行某些代码的无限迭代器，处理有限集合的极限迭代器，处理文件系统的全局迭代器和 noRewind 迭代器。

帖子 [PHP 迭代器第 4 部分:SPL 无限、极限、全局和无风迭代器](http://webmobtuts.com/backend-development/php-iterators-part-4-spl-infinite-limit-globe-and-norewind-iterators/)最早出现在[网络和移动教程](http://webmobtuts.com)上。