# 隐藏的红宝石

> 原文：<https://dev.to/katafrakt/hidden-jewels-of-ruby-stdlib-ifn>

这篇文章最初发布在我的个人博客 上 **[。](http://katafrakt.me/2018/06/06/hidden-jewels-ruby-stdlib/)**

* * *

Ruby 附带了一个庞大的标准库。我们通常只使用其中的一小部分。大家都知道`date`、`set`、`json`，甚至可能还有`csv`。但是那里隐藏着更多的东西。

前段时间某个地方的一个讨论(可能是 Reddit？)促使我深入研究 Ruby stdlib，在这篇文章中，我描述了我的发现。并不是所有的事情对我来说都是新的，有些只是被遗忘了。我选择了我认为最有趣、最有趣或者在其他方面最突出的。

一边通读一边问自己“为什么我要在 web 应用程序中使用它？”请记住，Ruby 并不是被设计成支持历史上最重要的 web 框架之一的语言。这里列出的东西更适合系统脚本等。

## 解析命令行选项

如今，当我们写一个 Ruby 脚本时，它通常是一个 Rake 任务。但是即使它是一个独立的文件，它通常也是以类似的方式被操纵的:通过环境变量或者仅仅通过由`ARGV`数组访问的位置参数。然而，在 stdlib 中，我们可以找到两个库来处理更复杂的输入。

### getptlong

其中一个就是`GetoptLong`。让我们看看它的作用:

```
require 'getoptlong'

opts = GetoptLong.new(
    [ '--url', GetoptLong::REQUIRED_ARGUMENT ],
    [ '--count', '-c', GetoptLong::OPTIONAL_ARGUMENT ],
    [ '--verbose', GetoptLong::NO_ARGUMENT ]
)

opts.each do |option, value|
    p [option, value]
end 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我定义了三个选项:

*   这是一个必需的参数
*   `count` -这是可选的
*   哪一面充当旗帜

之后是为每个选项打印其名称和值的代码。所以当我用`ruby getoptlong.rb -c 5 --verbose --url http://github.com`测试它时，我得到:

```
["--count", "5"]
["--verbose", ""]
["--url", "http://github.com"] 
```

Enter fullscreen mode Exit fullscreen mode

有几个有趣的怪癖。例如，如果我完全省略`url`，什么都不会发生。只有当我把它作为一个标志(`ruby getoptlong.rb --url`)时，我才会得到一个异常。此外，如果我使用一些未定义的选项，它也会抛出一个错误。

你可以在这里找到 GetoptLong [的文档。](https://ruby-doc.org/stdlib-2.5.0/libdoc/getoptlong/rdoc/GetoptLong.html)

### 选项解析器

这个解决方案更加健壮和先进。让我们用一个类似的例子来看看它的作用:

```
require 'optparse'

OptionParser.new do |opts|
    opts.banner = 'OptionParser example script'

    opts.on('--url URL') do |url|
        puts "url: #{url}"
    end

    opts.on('-c N', '--count N') do |n|
        puts "#{n} times"
    end

    opts.on('--verbose') do
        puts 'Verbose mode ON'
    end

    opts.on('-h', '--help') do
        puts opts
    end
end.parse! 
```

Enter fullscreen mode Exit fullscreen mode

这里的代码更加地道。结果在意料之中。关于额外选项等的行为。与 GetoptLong 相同。我们在这里(几乎)免费得到一个帮助信息。用`ruby optparse.rb -h` :
试试

```
OptionParser example script
        --url URL
    -c, --count N
        --verbose
    -h, --help 
```

Enter fullscreen mode Exit fullscreen mode

但是 OptionParser 远不止这些——强制类型、所谓的约定等等。在文档中阅读更多[。](https://ruby-doc.org/stdlib-2.5.0/libdoc/optparse/rdoc/OptionParser.html)

## 简单的持久键值存储

当我们 Ruby 开发人员考虑键值存储时，我们通常会有某种基于服务器的解决方案，比如 Redis 或 Riak。然而，当编写简单的应用程序时，使用嵌入式存储通常更合理。最近，来自脸书的 RocksDB 作为这样的解决方案之一而出名。但是对于 Ruby，我们很幸运在标准库中嵌入了键值存储。

而且，还有更多...不是一 KV 店。是他们三个: **DBM** 、 **GDBM** 和 **SDBM** 。它们彼此非常相似，因此我将仅快速概述不同之处:

*   依赖于你系统上安装的东西。它可以使用许多东西，并且大多数情况下，它在不同的机器之间是不兼容的(当系统配置改变时，甚至在同一台机器上也是如此)。因此，它不太适合持久存储，但适合临时应用。
*   是基于 KV store call 的一种特殊实现，不出意外，。在某些情况下，上述 DBM 可能会选择使用 GDBM 作为其底层存储。它应该兼容不同的系统。
*   的代码，和以前的相反，是和 Ruby 一起发货的，所以应该对所有机器都一样。

我们如何使用它？例如 SDBM(因为我们不需要安装任何额外的东西来拥有它):

```
require 'sdbm'

SDBM.open 'fruits' do |db|
  db['apple'] = 'fruit'
  db['pear'] = 'fruit'
  db['carrot'] = 'vegetable'
  db['tomato'] = 'vegetable'

  db.update('peach' => 'fruit', 'tomato' => 'fruit')

  db.each do |key, value|
    puts "Key: #{key}, Value: #{value}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这将在当前目录下创建两个文件。`fruits.dir`是空的(我真的不知道为什么)，但是真实的数据在`fruits.pag`里。可以用`hexdump -C fruits.pag` :
偷看一下

```
00000000  08 00 fb 03 f6 03 f2 03  ed 03 e7 03 de 03 d8 03  |................|
00000010  cf 03 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
000003c0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 76  |...............v|
000003d0  65 67 65 74 61 62 6c 65  74 6f 6d 61 74 6f 76 65  |egetabletomatove|
000003e0  67 65 74 61 62 6c 65 63  61 72 72 6f 74 66 72 75  |getablecarrotfru|
000003f0  69 74 70 65 61 72 66 72  75 69 74 61 70 70 6c 65  |itpearfruitapple|
00000400 
```

Enter fullscreen mode Exit fullscreen mode

数据是真实存在的。

这种解决方案的实用性可能非常有限。当您希望在脚本运行之间保持某种状态时，可以使用它。或者你真的在乎内存的时候。一直在 RAM 中加载一些大的哈希值会降低程序的速度。使用(S/G)DBM，您可以将暂时不用的数据转储到磁盘，并在以后需要时提取。

## 用 PStore 保持整个对象层次结构

说到坚持...在上面的例子中，我们只能使用字符串。这在很多情况下是可以的，但并不总是如此。如果您想保存应用程序状态的一部分，包括对象、它们的状态和关系，该怎么办呢？

Ruby stdlib 已经覆盖了您！PStore 正是你要找的。在这个例子中，我们将创建一些非常简单的类似有限状态机的结构，其状态通过命名的边相互连接:

```
class State
    def initialize(name)
        @name = name
        @edges = {}
    end

    def connect_to(word, state)
        @edges[word] = state
    end

    def traverse(indent = 0)
        tab = " " * indent
        puts "#{tab}State #{@name}:"
        @edges.each do |word, state|
            puts "#{tab} '#{word}':"
            state.traverse(indent + 4)
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

一个`traverse`方法简单地显示从开始节点到结束节点的连接(注意，我们不处理循环！).所以现在让我们创建一些结构并遍历它:

```
s0 = State.new('start')
s1 = State.new('first')
s2 = State.new('second')
s3 = State.new('third')
s4 = State.new('fourth')

s0.connect_to('aa', s1)
s0.connect_to('aaa', s2)
s1.connect_to('b', s3)
s3.connect_to('c', s4)
s2.connect_to('d', s4)

s0.traverse 
```

Enter fullscreen mode Exit fullscreen mode

我们得到的是:

```
State start:
  'aa':
    State first:
      'b':
        State third:
          'c':
            State fourth:
  'aaa':
    State second:
      'd':
        State fourth: 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用`PStore`将它保存到磁盘上的一个文件:

```
require "pstore"

storage = PStore.new('fsm.pstore')
storage.transaction do
    storage['start'] = s0
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，在不同的脚本中，我们加载并遍历:

```
class State
    # omitting, definition same as above
end

require "pstore"

storage = PStore.new('fsm.pstore')
storage.transaction do
    start = storage['start']
    start.traverse
end 
```

Enter fullscreen mode Exit fullscreen mode

和输出完全一样！如果你像我一样好奇，你可以再次使用`hexdump`来查看`fsm.pstore`文件:

```
00000000  04 08 7b 06 49 22 0a 73  74 61 72 74 06 3a 06 45  |..{.I".start.:.E|
00000010  54 6f 3a 0a 53 74 61 74  65 07 3a 0a 40 6e 61 6d  |To:.State.:.@nam|
00000020  65 49 22 0a 73 74 61 72  74 06 3b 00 54 3a 0b 40  |eI".start.;.T:.@|
00000030  65 64 67 65 73 7b 07 49  22 07 61 61 06 3b 00 54  |edges{.I".aa.;.T|
00000040  6f 3b 06 07 3b 07 49 22  0a 66 69 72 73 74 06 3b  |o;..;.I".first.;|
00000050  00 54 3b 08 7b 06 49 22  06 62 06 3b 00 54 6f 3b  |.T;.{.I".b.;.To;|
00000060  06 07 3b 07 49 22 0a 74  68 69 72 64 06 3b 00 54  |..;.I".third.;.T|
00000070  3b 08 7b 06 49 22 06 63  06 3b 00 54 6f 3b 06 07  |;.{.I".c.;.To;..|
00000080  3b 07 49 22 0b 66 6f 75  72 74 68 06 3b 00 54 3b  |;.I".fourth.;.T;|
00000090  08 7b 00 49 22 08 61 61  61 06 3b 00 54 6f 3b 06  |.{.I".aaa.;.To;.|
000000a0  07 3b 07 49 22 0b 73 65  63 6f 6e 64 06 3b 00 54  |.;.I".second.;.T|
000000b0  3b 08 7b 06 49 22 06 64  06 3b 00 54 40 13        |;.{.I".d.;.T@.|
000000be 
```

Enter fullscreen mode Exit fullscreen mode

有用吗？也许不是，但也许是？例如，我可以看到这样保存一个简单游戏状态的潜力。

## 观察者模式

Ruby 的`Observable`的用法实际上是第一个(？)2008 年我学习 Ruby 的那本书(？).所以这对我来说并不新鲜，但值得提醒的是，我们已经内置了这样的东西。在某些情况下，它实际上可以使代码更干净。

为了说明它是如何工作的，我将实现另一个 FizzBuzz(虽然会有点不正确，因为每次都会打印一个数字):

```
require 'observer'

class Incrementor
    include Observable

    def initialize
        @number = 0
    end

    def runto(num)
        loop do
            @number += 1
            changed # note this!
            print "#{@number} "
            notify_observers(@number)
            puts ""
            break if @number >= num
        end
    end
end

class FizzObserver
    def update(num)
        print "Fizz" if num % 3 == 0
    end
end

class BuzzObserver
    def update(num)
        print "Buzz" if num % 5 == 0
    end
end

inc = Incrementor.new
inc.add_observer(FizzObserver.new)
inc.add_observer(BuzzObserver.new)
inc.runto(30) 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这段代码，你会发现它是有效的。只需要记住两件事:调用`changed`来表明对象已经改变，当你想发出新值时调用`notify_observers`。

为什么有用？你可以在你的主类之外抽象一些东西(比如日志)。但是，请注意，滥用它将导致回调地狱，这将很难调试和理解。就像 ActiveRecord 回调一样。

## DRb

DRb 或 dRuby 是标准库中真正的瑰宝。简单描述为“Ruby 的分布式对象系统”，它能给你带来很多乐趣。为了现场观看，我决定使用一些真正有用的东西:每隔`@interval`秒打印从 0 到`@max_num`的随机数的服务。这里的代码，包括 DRb:

```
require 'drb/drb'

class RandomService
    def initialize
        set_max_num(100)
        set_interval(1)
    end

    def run
        while @should_stop.nil?
            puts rand(@max_num)
            sleep(@interval)
        end
    end

    def set_max_num(num)
        @max_num = num
    end

    def set_interval(time)
        @interval = time
    end

    def stop!
        @should_stop = true
    end
end

service = RandomService.new
DRb.start_service('druby://localhost:9394', service)
service.run 
```

Enter fullscreen mode Exit fullscreen mode

这个类本身非常简单，我不打算详细介绍它。这里唯一(希望)不熟悉的是对 DRb 的调用，在这里我们用 dRuby 协议包装我们的服务。基本上，它所做的是在本地主机的端口 9394 上公开我们的接口。现在，记住了，我推荐启动服务，把你的终端一分为二(iTerm 在 Mac 上可以做到，我推荐 [Tilix](https://github.com/gnunn1/tilix) 用于 Linux)。

现在，当我们运行我们的小服务时，在第二个终端中启动 irb 并键入:

```
irb(main):001:0> require 'drb/drb'
=> true
irb(main):002:0> service = DRbObject.new_with_uri('druby://localhost:9394')
=> #<DRb::DRbObject:0x007fd51a8072c0 @uri="druby://localhost:9394", @ref=nil> 
```

Enter fullscreen mode Exit fullscreen mode

完成后，就可以通过调用`service`上的方法开始玩了。将间隔减小到`0.1`，将`max_num`设置为 1000——随你怎么想。最后，通过运行`service.stop!`停止显示。您所做的一切都会立即反映在您在不同终端的完全不同的进程中运行的进程中！不用说，如果你愿意，你也可以通过网络来完成。

你现在可能认为这只是一个不错的玩具。但实际上我已经看到了实践中使用的类似的东西。可能最显著的例子是 IRC bot，在那里你可以从 Ruby 控制台做很多事情，从临时添加管理员到一些通常在启动时填充的数组开始(因此，不需要停机重启！)，以定义全新的方法结束，并建议在实际将它们放入代码之前进行测试。我还可以想象暴露这样的接口，例如，操纵一些工人池的大小等。实际上，天空几乎是这里的极限。

## 其他

stdlib 里还有很多东西。我将提到他们中的几个，但没有如此详细的描述。

### tsort

我有点难以理解 tsort 的真正用途。它做的是有向无环图的拓扑排序。如果这听起来很具体，那是因为它确实如此。当您有一个依赖关系图(A 依赖于 B 和 C，B 依赖于 D，E 依赖于 A)并且您需要确定安装这些依赖关系的顺序时，这种排序在依赖关系排序中非常有用，这样每个项目在安装时都已经安装了它的依赖关系。

劳森·库尔茨(Lawson Kurtz)有一篇很棒的文章解释了如何在 Bundler 中使用它。

### 数学

Ruby 标准库中的一些数学相关类:

*   `Matrix`有矩阵运算的方法，例如(但不限于):`conjugate`、`determinant`、`eigensystem`、`inverse`等等(见[文档](https://ruby-doc.org/stdlib-2.5.1/libdoc/matrix/rdoc/Matrix.html))
*   [`Prime`](https://ruby-doc.org/stdlib-2.5.1/libdoc/prime/rdoc/Prime.html) 代表所有素数的无限集合。你不需要自己实现这个厄拉多塞筛！
*   【sidenote】我很惊讶 stdlib 里居然没有`Complex`类，尤其是在我了解到它原来是有的，但是被移除了之后。事实证明，它实际上成功到达了核心(因此它是自动必需的)。打开你的`irb`，写下:`(2 + 3i) * (-6i)`(剧透:不会是`NameError`，因为 fo 未定义`i`)

### abbrev

这可能更像是一个真正有用的工具，但如果你需要它，它就在那里。 [`Abbrev`](https://ruby-doc.org/stdlib-2.5.1/libdoc/abbrev/rdoc/Abbrev.html) 模块有一个方法`abbrev`，它接受一个字符串列表，并返回可能的非多义性缩写。比如:

```
Abbrev.abbrev(%w[ruby rubic russia])
#=> {"ruby"=>"ruby", "rubic"=>"rubic", "rubi"=>"rubic", "russia"=>"russia", "russi"=>"russia", "russ"=>"russia", "rus"=>"russia"} 
```

Enter fullscreen mode Exit fullscreen mode

所以，你知道不能用`ru`做缩写。

### zlib

最后还有 [`zlib`](https://ruby-doc.org/stdlib-2.5.1/libdoc/zlib/rdoc/Zlib.html) 。引用一下:

> Zlib 被设计成一个可移植的、免费的、通用的、不受法律约束的——也就是说，不受任何专利保护的——无损数据压缩库，几乎可以在任何计算机硬件和操作系统上使用。

对我来说，听起来挺好的。与 gzip 相比:

> zlib 格式被设计成紧凑和快速的，用于内存和通信信道。gzip 格式是为文件系统上的单个文件压缩而设计的，它有一个比 zlib 更大的头来维护目录信息，并使用一种不同的、比 zlib 更慢的检查方法。

所以当你通过网络发送东西时，zlib 实际上是一个减少开销的好选择。为了检查一下，我从古腾堡那里拿了 *[《傲慢与偏见》](https://www.gutenberg.org/files/1342/1342-0.txt)《T3》，检查了一下怎么压缩:* 

```
require 'zlib'
source = File.read('path/to/pride-and-prejudice.txt')
compressed = Zlib::Deflate.deflate(source)
decompressed = Zlib::Inflate.inflate(compressed)

puts "Source size: #{source.bytesize}"
puts "Compressed: #{compressed.bytesize}"
puts "Decompressed: #{decompressed.bytesize}"
puts "Compression: #{(1 - (compressed.bytesize.to_f / source.bytesize)).round(4)}" 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
Source size:  724725
Compressed:   260549
Decompressed: 724725
Compression:  0.6405 
```

Enter fullscreen mode Exit fullscreen mode

我说这是相当令人印象深刻的！

## 更？

是啊！Ruby stdlib 中隐藏了更多。我错过了什么吗？你觉得有什么更有趣的吗？让我知道。