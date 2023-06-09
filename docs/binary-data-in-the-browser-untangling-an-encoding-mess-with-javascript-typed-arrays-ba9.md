# 浏览器中的二进制数据:用 JavaScript 类型化数组解决编码混乱

> 原文：<https://dev.to/bnevilleoneill/binary-data-in-the-browser-untangling-an-encoding-mess-with-javascript-typed-arrays-ba9>

【MIDI 文件损坏的情况

[![](img/7df1b4bc3660c49bfb81afa057287497.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wS2ggbjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMPpfSHYOlpIBcdaPQXBBRQ.png)

直到最近，我还不太熟悉使用类型化数组，因为我倾向于避免在 web 上使用二进制数据的魔法。

但是一个人并不总是有奢侈的选择。例如，当我开始开发一个需要在没有服务器的情况下运行的音乐应用程序时，猜猜现在是什么时候？

如果你猜“类型化数组时间”，你是一个天才。回想一下，[类型化数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)允许您在 JavaScript 中存储和访问原始二进制数据。

该应用程序无法生成有效的音乐文件，我不知道为什么。我开始怀疑是否有可能完全在浏览器中完成。

事实证明，这不仅是可能的，而且非常简单！这种情况多长时间发生一次？

你只需要知道一点关于 JavaScript 如何存储字符串，如何将这些字符串表示为字节，以及当你将这些字节放入一个文件时会发生什么。

如果调试一个损坏的二进制文件听起来令人望而生畏，我希望这个故事将消除一些神秘感，这样您就可以自信地处理任何类型的二进制数据，而无需求助于服务器。

我正在做的项目是一个[音乐应用](https://github.com/tiburzi/cync),探索非西方音乐传统中常见的循环模式。

[![](img/429304174fbf2a1dd1fbfe4799f63547.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eeroPh3A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1016/1%2AT3rBlURk6VQqLE7mXSWnQw.png) 

<figcaption>这里有一首来自[加纳邮报工人之歌](https://www.youtube.com/watch?v=por5SopwHDc)的复调，你可以在 [app](https://tiburzi.github.io/cync/?state=eyJvcmJpdHMiOls2MCwxMjAsMjQwXSwibm90ZXMiOlt7Im9JbmRleCI6MCwidGhldGEiOjIuMjg3OTU5NTk3Mzc3ODQxNywic0luZGV4IjowfSx7Im9JbmRleCI6MCwidGhldGEiOjEuNjU3NTM3MjY2NDk1NTU0Mywic0luZGV4IjoyfSx7Im9JbmRleCI6MCwidGhldGEiOjIuOTU3NDI4ODkzMzgyNjg0LCJzSW5kZXgiOjJ9LHsib0luZGV4IjoxLCJ0aGV0YSI6LTIuMDIxNjg3Njk2Njc2Njg1Miwic0luZGV4IjowfSx7Im9JbmRleCI6MSwidGhldGEiOi0xLjU2MjcwNTg4ODgzMjY4NjQsInNJbmRleCI6M30seyJvSW5kZXgiOjEsInRoZXRhIjoxLjU3MDc5NjMyNjc5NDg5NjYsInNJbmRleCI6M30seyJvSW5kZXgiOjEsInRoZXRhIjotMC4wMTMzMzI1OTQxNDc3MzUzNDYsInNJbmRleCI6M30seyJvSW5kZXgiOjIsInRoZXRhIjotMi4zMTAzODkzMDcyNjc0MzgsInNJbmRleCI6MH1dLCJ0ZW1wbyI6Nzh9) 中探索。彩色点是用户可以放置的音符。</figcaption>

这是一个班级项目，所以我希望它存在，而不必担心保持服务器运行。如果我能让它只是一个静态的 JavaScript 应用程序，我就能把它永久免费地放在 Github 页面上！所以我和我的合作伙伴拒绝增加后端服务器。即使当我们希望人们能够保存和加载他们创建的模式时，我们也选择将状态保存为 URL 中的一个长 base64 编码字符串，该字符串可以共享以避免使用数据库。

我们差一点就可以在没有服务器的情况下运行整个系统了。我们需要的最后一件事是让用户下载他们的创作作为一个 MIDI 文件。如果我们能做到这一点，那么人们就可以将自己的乐器运用到音符中，将它们嵌入自己的旋律中，或者进行自己的进一步音乐分析。那时我们的应用程序会有用得多。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 用 JavaScript 生成 MIDI 文件

jsmidgen 库看起来确实满足了我们的需求。这是一个纯 JavaScript 实现&，它将音符作为输入，并创建一个 MIDI 文件。

问题是他们所有的例子都在 Nodejs 中运行。这是他们用一些音符创建一个音轨并将其写入文件的例子。

```
var fs = require('fs');
var Midi = require('jsmidgen');

var file = new Midi.File();
var track = new Midi.Track();
file.addTrack(track);

track.addNote(0, 'c4', 64);
track.addNote(0, 'd4', 64);
track.addNote(0, 'e4', 64);
track.addNote(0, 'f4', 64);
track.addNote(0, 'g4', 64);
track.addNote(0, 'a4', 64);
track.addNote(0, 'b4', 64);
track.addNote(0, 'c5', 64);

fs.writeFileSync('test.mid', file.toBytes(), 'binary'); 
```

它看起来不像是为在客户端使用而设计的，但是在那个代码片段中，我唯一不能在浏览器中做的事情就是写入文件系统。因此，作为一种变通方法，我使用 [FileSaver.js](https://github.com/eligrey/FileSaver.js/) 为包含 jsmidgen 返回的二进制数据的 [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) 生成一个下载链接。

是这样的:

```
\<script type="text/javascript" src="jsmidgen.js"\>\</script\>
\<script type="text/javascript" src="[FileSaver.js](https://fastcdn.org/FileSaver.js/1.1.20151003/FileSaver.min.js)"\>\</script\>

...

var file = new Midi.File();
var track = new Midi.Track();
file.addTrack(track);

track.addNote(0, 'c4', 64);
track.addNote(0, 'd4', 64);
track.addNote(0, 'e4', 64);
track.addNote(0, 'f4', 64);
track.addNote(0, 'g4', 64);
track.addNote(0, 'a4', 64);
track.addNote(0, 'b4', 64);
track.addNote(0, 'c5', 64);

var bytes = file.toBytes()
var blob = new Blob([bytes], {type:'audio/midi'})
saveAs(blob,"music.midi") 
```

看到一切正常运行，浏览器中弹出下载提示，我松了一口气。这看起来很有希望！

我双击下载的 MIDI 文件，心一沉。

[![](img/5f81c50557d27316870ae0b7790a5474.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nlOPFjo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAyyzh9C1QqVcCXe__qO6xQ.png)

啊哦。

### 调试二进制文件

我没想到会发生这种事。我希望至少得到一些我可以尝试和调查的错误。代码中没有警告或错误。生成的文件只是…不工作。

我如何开始调试一个损坏的 MIDI 文件？它可能会丢失一些字节，可能会有一些额外的字节，或者可能只是有错误的字节。我不知道它是被我正在使用的下载库损坏了，还是被浏览器的 Blob 接口损坏了，还是完全被别的东西损坏了。我对 MIDI 格式知之甚少，学习如何辨别为什么给定的字节序列是无效的似乎令人生畏。

在这一点上，很难拒绝添加服务器。它在 Node 中工作得非常好，而且将数据发送到服务器进行处理并发送回来是非常容易的。但是在没有服务器的情况下，我们已经做到了这一步，我现在不打算放弃。

我深吸一口气，卷起袖子，决定在 Sublime 中检查损坏的 MIDI 文件。

```
4d54 6864 0000 0006 0000 0001 00c2 804d
5472 6b00 0000 4400 c290 3c5a 40c2 803c
5a00 c290 3e5a 40c2 803e 5a00 c290 405a
40c2 8040 5a00 c290 415a 40c2 8041 5a00
c290 435a 40c2 8043 5a00 c290 455a 40c2
8045 5a00 c290 475a 40c2 8047 5a00 c290
485a 40c2 8048 5a00 c3bf 2f00 
```

对我来说，看起来像一个完美的二进制文件表示为十六进制。但是我们当然知道它有问题。此序列的某些部分不符合 MIDI 期望的字节序列。为了比较，下面是节点版本代码生成的文件，可以正确播放。

```
4d54 6864 0000 0006 0000 0001 0080 4d54
726b 0000 0044 0090 3c5a 4080 3c5a 0090
3e5a 4080 3e5a 0090 405a 4080 405a 0090
415a 4080 415a 0090 435a 4080 435a 0090
455a 4080 455a 0090 475a 4080 475a 0090
485a 4080 485a 00ff 2f00 
```

这当然是不同的，但不是以一种非常系统的方式。损坏的文件中有更多的字节，但两个文件中的前几个字节是相同的！事实上，如果您仔细阅读，您会发现 c2 是损坏文件的第一行中唯一的额外字节。

这更加神秘了。损坏的文件大部分是正确的，但只是稍微有些偏差；这里和那里多了一个字节。很明显，如果在 Node 中运行代码和在浏览器中运行代码的输出不同，那么 MIDI 数据就会发生变化。如果我能输出每一步的数据来查看是谁出了错，那就太好了，但是我查看这些数据的唯一方法是将其打印到控制台(当打印为字符串时，它看起来就像是乱码文本)或者将其写入文件，就我所知，这可能是破坏数据的操作！

就好像我不能在不改变它的情况下看二进制数据。我是盲目的，我没有任何好的证据来反对代码的任何部分。所以我只是胡乱猜测了一下:可能 jsmidgen 有 bug，在浏览器中运行时没有生成有效的 MIDI。我至少知道无效字节是什么。也许我可以搜索 C2 是何时生成的(或者它的十进制表示，194)，并跟踪产生它的逻辑。

### Jsmidgen 被证明无罪

几个小时后，我在图书馆里没有发现任何缺陷。事实上，194 这个数字甚至从未出现在 jsmidgen 生成的数字列表中。这最终是一个死胡同搜索。我仍然不知道为什么数据会被破坏，但我知道 jsmidgen 创建了一个字节列表，就像一个常规的数字数组，然后对每个数字调用 String.fromCharCode 将其转换为一个字符。这是它返回的最终“字节”值。只是一串而已。

因此，如果我想查看原始字节，我所要做的就是将这个字符串中的每个字符转换回生成它的字符代码。

```
var bytes = file.toBytes()
for(var i=0;i\<bytes.length;i++){
 console.log(bytes[i].charCodeAt(0))
} 
```

这正式宣告 jsmidgen 无罪。我可以看到我在浏览器中得到的字节序列与 Node 中的字节序列完全相同！就是这个！我有正确的输出——它只是被困在我的应用程序中。任何将它作为文件发送到外界的尝试都会导致这种奇怪的混乱。我觉得真相就在我的掌握之中，但我却无能为力。这就好像我在犯罪现场，知道谁是凶手，并大声喊着答案，但没有人能听到我。

仅仅是将这些字节转换成 JavaScript 字符串就能改变它们吗？原则上，如果我存储的数字超出了字符串可以容纳的范围，它们可能会被截断或溢出。但是 JavaScript 字符串[使用 UCS-2 或 UTF-16](https://mathiasbynens.be/notes/javascript-encoding) 编码，这或多或少意味着它用 2 个字节表示值，而 [MIDI 将每个值存储在 1 个字节](https://en.wikipedia.org/wiki/MIDI#Technical_specifications)中。我通过确保 jsmidgen 生成的字节数组中的所有值都小于 256 来验证这一点，因此它们肯定都适合一个字节，并且不会溢出。

### 强制编码

当时我不太明白原因，但似乎不同的编码方案是问题的一部分。JavaScript 在其字符串中每个值使用 2 个字节，而 MIDI 使用 1 个字节。

如果 JavaScript 字符串为每个字符存储 2 个字节，为什么在结果文件中我只看到每个值 1 个字节？每个值旁边应该有空的 0 字节。

显然，由于文件中没有这些内容，所以一定发生了某种转换！我能强迫它写出每个值最初的 2 个字节吗？

答案是[类型化数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)。如果我向 Blob 传递一个类型化数组，它不会尝试做任何转换。所以不用:

```
var bytes = file.toBytes()
var blob = new Blob([bytes], {type:'audio/midi'})
saveAs(blob,"music.midi") 
```

我创建了一个数组，用 2 个字节(或 16 位)存储每个值，并复制所有的值。

```
var bytes = file.toBytes() // remember, this is just a string
var u16 = new Uint16Array(bytes.length)
// Copy over all the values
for(var i=0;i\<bytes.length;i++){
 u16[i] = bytes[i].charCodeAt(0)
}
// Now we write the typed array to the Blob instead of the string
var blob = new Blob([u16], {type:'audio/midi'})
saveAs(blob,"music.midi") 
```

这样，我得到了一个仍然损坏的文件，但是有正确的值！请记住，正确文件的第一行包含这些值:

```
4d54 6864 0000 0006 0000 0001 0080 4d54 
```

我的新文件看起来像:

```
4d00 5400 6800 6400 0000 0000 0000 0600 0000 0000 0000 0100 
0000 8000 4d00 5400 
```

如果你去掉每个字节后面多余的 00，那就完全一样了！我现在要做的就是把这两个字节的值压缩成一个字节。如果我将所有的值复制到 Uint8Array 而不是 Uint16Array，它会生成完全正确的文件！

终于成功了！我们不需要服务器，我们现在可以生成和下载有效的 MIDI 文件！

但这仍然令人困惑。总结一下:我们有一个原始的数字列表，每个数字可以放在一个字节中(它们都小于 256)。我们试图将这些编码为字符串的数字放入一个每个 1 字节的文件中，这导致了额外字节的添加。为了解决这个问题，我们将这些数字放入一个类型化的数组中，每个数组 1 个字节，得到的文件具有正确的原始字节列表。感觉就像我们什么都没做，好像只是把我们的数据包装在一个类型化数组中就神奇地修复了它！

### 这不是魔法

秘密在于 [Blob 构造器](https://developer.mozilla.org/en-US/docs/Web/API/Blob/Blob)。如果你仔细观察，你会发现**如果你传递一个字符串，它将被编码为 UTF-8** 。但是，如果我们所有的值都放在一个字节里，那又有什么关系呢？

我的困惑来自于认为 UTF-8 编码对每个字符只使用了 8 位(所以只有 1 个字节)，而 UTF-16 对每个字符使用了 16 位。所以这就像把东西分别放在 uint 8 数组和 uint 16 数组中一样。这不是真的。 **UTF-8 是一种可变长度的编码方案**，它可以表示超过 256 个字符(事实上超过一百万)。

要了解这是如何工作的，让我们试着将简单的字符串“Hello World”放入一个文件中。我强烈建议您亲自尝试一下— [这里有一个文件](https://gist.github.com/OmarShehata/a8b4edf25af99f855fc2e36550e2976d)您可以在本地运行来尝试一下。

```
var sample\_string = "Hello World!"
saveAs(new Blob([sample\_string]),"output.txt") 
```

如果我们使用 [HexViewer](https://facelessuser.github.io/HexViewer/) 查看结果文件，我们可以看到每个字符都存储在一个字节中。

[![](img/323b01ad4f2cbf8b2ab74f312a5a4529.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sSeVpi3Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjI8RU5mkvr-1qAcAPHVmRg.png)

所有这些字符的值都小于 256。现在，如果我们试图对一个字符串进行编码，而这个字符串中的一个字符的相关值无法用一个字节来表示，会发生什么呢？让我们试着把“Hello✨World！”在一个文件里。“✨”字符的 Unicode 值为 10，024。

```
var sample\_string = "Hello" + String.fromCharCode(10024) + "World!"
saveAs(new Blob([sample\_string]),"output.txt") 
```

所有其他字符仍然存储在 1 个字节中，但是这个新字符占用了 3 个字节！

[![](img/2c0f16d4c9f4cbba30e3a5801d0bb538.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yAwgjUzC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHf0-05cKpc-cTCkAd4zqxg.png)

如果你把 10024 转换成二进制，你会得到一个 14 位数，所以我们只需要 2 个字节来存储它。但是让我们看看 UTF 8 号是如何做到的。

它用于“✨”的 3 个字节是 e2 9c a8。在二进制中，那就是:

```
11100010 10011100 10101000 
```

本页的表格描述了如何解码 UTF-8。它说如果这个字符被编码成 3 个字节，那么我们取:

*   第一个字节的最后 4 位，
*   第二字节的最后 6 位，
*   和第三字节的最后 6 位。

这给我们留下了:

```
0010 011100 101000 
```

如果转换成十进制，结果是 10024！由于 10024 适合 2 个字节，我们也可以将每个字符存储在 2 个字节中。

```
var sample\_string = "Hello" + String.fromCharCode(10024) + "World!"
var bytes = []
for(var i = 0;i \< sample\_string.length; i++){
 bytes.push(sample\_string[i].charCodeAt(0))
}
var u16 = new Uint16Array(bytes)
saveAs(new Blob([u16]),"output.txt") 
```

我们得到了:

[![](img/4cd9bb3f9bafcf07509740b773ac34b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VNkNRznX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6Kwvj5ygt9LYNcCJQ-ki7A.png)

这肯定更简单(要恢复原始值，您只需将每对字节连接起来，第二个字节在左边)，但您可以看到，如果您的大部分值都在英文字母范围内，会浪费更多的空间。

### 解开谜团

那么神秘的 c2 从何而来？如果你回头看看描述 UTF-8 的页面，你会注意到它用 1 个字节表示 127 以下的任何内容，之后用 2 个字节。所以这里的第一行原来的**损坏的 MIDI** 再次:

```
4d54 6864 0000 0006 0000 0001 00c2 804d 
```

那里不应该有 c2。应该就 80 吧。记住这是十六进制的，所以十六进制的 80 是十进制的 128。让我们试着用之前的方法解码。二进制的两个字节 c2 80 是:

```
11000010 10000000 
```

对于两个字节，UTF-8 解码表说我们删除第一个字节的前 3 位，第二个字节的前 2 位，留给我们:

```
00010000000 
```

也就是 128。因此，尽管值 128 完全适合存储在 1 个字节中，但 UTF-8 编码将它存储在 2 个字节中(你明白为什么它不能在 1 个字节中存储 128 了吗？)我们知道这两个字节一起表示单个值 128 而不是两个值 194 和 128 的唯一原因是因为我们知道它被编码为 UTF-8(这就是为什么声明文件的编码总是至关重要的)。但是 MIDI 解码器不知道这一点。它一直一次读取一个字节，即 194 后跟 128，这就是文件损坏的原因。

### 结论

我希望您发现这种对二进制数据和编码的探究很有启发性！我认为知道你可以控制你的数据在 JavaScript 中如何用类型化数组存储是很有力量的，有时你需要这种控制来使事情完全在浏览器中工作。

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *