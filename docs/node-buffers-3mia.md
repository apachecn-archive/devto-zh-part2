# 节点缓冲区

> 原文：<https://dev.to/flaviocopes/node-buffers-3mia>

## 什么是缓冲？

缓冲区是内存的一个区域。JavaScript 开发人员对这个概念并不熟悉，远不如 C、C++或 Go 开发人员(或任何使用系统编程语言的程序员)，他们每天都要与内存进行交互。

它表示在 V8 JavaScript 引擎之外分配的固定大小的内存块(不能调整大小)。

您可以将缓冲区想象成一个整数数组，每个整数代表一个字节的数据。

它由节点[缓冲类](https://nodejs.org/api/buffer.html)实现。

## 我们为什么需要缓冲？

在传统上只处理字符串而不处理二进制数据的生态系统中，引入缓冲区是为了帮助开发人员处理二进制数据。

缓冲区与[流](https://flaviocopes.com/nodejs-streams/)深度链接。当流处理器接收数据的速度超过其消化能力时，它会将数据放入缓冲区。

一个简单的缓冲可视化是当你正在观看一个 YouTube 视频，红线超出了你的可视化点:你下载数据的速度比你观看它的速度快，你的浏览器缓冲它。

## 如何创建缓冲区

使用 buffer 类构造函数创建一个缓冲区，传递一个字符串:

```
const buf = new Buffer('Hey!') 
```

Enter fullscreen mode Exit fullscreen mode

您可以选择在第二个参数中传递编码(默认为 UTF-8)。

如果第一个参数是一个整数而不是一个字符串，也可以只通过传递大小来初始化缓冲区。这将创建一个 1KB 的缓冲区:

```
const buf = new Buffer(1024)

//or

const buf = Buffer.alloc(1024) 
```

Enter fullscreen mode Exit fullscreen mode

## 使用缓冲器

### 访问一个缓冲区的内容

缓冲区是一个字节数组，可以像数组一样访问:

```
const buf = new Buffer('Hey!')
console.log(buf[0]) //72
console.log(buf[1]) //101
console.log(buf[2]) //121 
```

Enter fullscreen mode Exit fullscreen mode

这些数字是标识缓冲区位置中的字符的 Unicode 代码(H => 72，e => 101，y => 121)

您可以使用`toString()`方法:
打印缓冲区的全部内容

```
console.log(buf.toString()) 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，如果你用一个设置大小的数字初始化一个缓冲区，你将访问包含随机数据的预初始化内存，而不是一个空缓冲区！

### 获取一个缓冲区的长度

使用`length`属性:

```
const buf = new Buffer('Hey!')
console.log(buf.length) 
```

Enter fullscreen mode Exit fullscreen mode

### 迭代缓冲区的内容

```
const buf = new Buffer('Hey!')
for (const item of buf) {
  console.log(item) //72 101 121 33
} 
```

Enter fullscreen mode Exit fullscreen mode

### 改变缓冲区的内容

您可以使用`write()`方法:
向缓冲区写入一整串数据

```
const buf = new Buffer(4)
buf.write('Hey!') 
```

Enter fullscreen mode Exit fullscreen mode

就像你可以用数组语法访问一个缓冲区一样，你也可以用同样的方式设置缓冲区的内容:

```
const buf = new Buffer('Hey!')
buf[1] = 111 //o
console.log(buf.toString()) //Hoy! 
```

Enter fullscreen mode Exit fullscreen mode

### 复制缓冲区

使用`copy()`方法可以复制缓冲区:

```
const buf = new Buffer('Hey!')
let bufcopy = new Buffer(4) //allocate 4 bytes
buf.copy(bufcopy) 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，你复制整个缓冲区。另外 3 个参数允许您定义起始位置、结束位置和新的缓冲长度:

```
const buf = new Buffer('Hey!')
let bufcopy = new Buffer(2) //allocate 4 bytes
buf.copy(bufcopy, 0, 2, 2)
bufcopy.toString() //'He' 
```

Enter fullscreen mode Exit fullscreen mode

### 切片缓冲区

如果要创建缓冲区的部分可视化，可以创建切片。切片不是拷贝:原始缓冲区仍然是真实的来源。如果这种情况发生变化，你的份额也会发生变化。

使用`slice()`方法创建它。第一个参数是起始位置，你可以用结束位置指定一个可选的第二个参数:

```
const buf = new Buffer('Hey!')
buf.slice(0).toString() //Hey!
const slice = buf.slice(0, 2)
console.log(slice.toString()) //He
buf[1] = 111 //o
console.log(slice.toString()) 
```

Enter fullscreen mode Exit fullscreen mode