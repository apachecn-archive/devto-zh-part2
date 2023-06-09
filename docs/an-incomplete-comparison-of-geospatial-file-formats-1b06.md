# 地理空间文件格式的不完整比较

> 原文：<https://dev.to/diogok/an-incomplete-comparison-of-geospatial-file-formats-1b06>

几年前，我从事地理空间分析工作，并开始研究和比较一些用于存储，尤其是传输此类数据的替代文件格式。

我主要处理矢量数据(多边形和点),并为网络服务。这项工作的动机是测试 TopoJSON 的收益和成本，以及它在大小(对传输时间很重要)和编码时间(对服务器资源使用很重要)方面的表现。

可在 [Shapefile must die 找到可用格式优势的更完整描述！](http://switchfromshapefile.org/)，一个很好的资源，即使在文字上有点强(仅供参考:作为一名开发人员，我真的不喜欢使用 shapefile)。

即使我从来没有完成它，我想我可以分享结果。

### 竞争者文件格式

首先，让我们分组。这里我们将比较三件事:

*   数据结构
*   格式编码
*   压缩

对于**数据结构**，我正在比较数据在文件内部是如何组织的:

*   形状
*   GeoJSON 结构
*   TopoJSON 结构

对于**格式编码**，我正在比较这个结构是如何序列化的:

*   二进制(形状文件)
*   JSON
*   消息包

对于**压缩**，我正在比较压缩算法:

*   没有人
*   放气/GZIP
*   XZ/LZMA

测试内容的选择是基于编程语言(Java/Clojure)的工具可用性和 web(Javascript)的易用性。

未测试格式的几点注意事项:

**CSV** 没有经过测试，因为它不像看起来那样标准，有几个分隔符、封装和记录分隔符的组合。并且还必须比较 WKT 和 WKB 以及其他几何编码。但它可能会表现得很好，因为它可以被流式传输和很好地压缩。

Spatialite 处理起来有点复杂，因为你不仅需要一个 SQLite 库，还需要 Spatialite 的扩展。还必须定义表结构等。

给我更多的时间，我会包括更多的测试。

Shapefile 仅作为基线进行了测试。

### 总体结果

代码和结果可以在 [my github](https://github.com/diogok/geof) 找到。

 ``| Structure | Format | Compress | Size | Time |
|-----------|---------|----------|-------|------|
| Shapefile | - | - | 5MB | - |
| Shapefile | - | zip | 3.2MB | - |
| Geo | JSON | - | 9MB | 10s |
| Geo | JSON | gz | 2.5MB | 11s |
| Geo | JSON | xz | 1.4MB | 21s |
| Geo | MsgPack | - | 5.2MB | 9s |
| Geo | MsgPack | gz | 3.5MB | 11s |
| Geo | MsgPack | xz | 1.7MB | 15s |
| Topo | JSON | - | 524KB | 22s |
| Topo | JSON | gz | 84KB | 20s |
| Topo | JSON | xz | 64KB | 22s |
| Topo | MsgPack | - | 256KB | 21s |
| Topo | MsgPack | gz | 76KB | 20s |
| Topo | MsgPack | xz | 60KB | 22s |`` 

 ``Shapefiles 是基线，它们压缩得不是很好。

正如所料，原始 GeoJSON 文件非常大，但作为文本文件，它们压缩得非常好，编码速度也相当快。

TopoJSON 文件最小，但编码需要很长时间。这个测试中还没有包括拓扑编码占用大量内存的事实，因为它必须保存整个集合才能遍历它。

MessagePack 作为一种格式提供了合理的空间效率，是一种二进制格式，编码速度更快。它们增加了网络的复杂性，并在压缩后失去了大部分收益。它们在服务器上读/写速度较快，但在浏览器上速度较慢。

放气/GZ 提供预期压缩结果。它们在网络上是标准的，这使它成为一个容易的选择，你的服务器已经有了它们，浏览器也有。

LZMA/XZ 有点难以在浏览器上使用，但它能够提供更多。

### 几点结论

这个测试是不完整的，你应该在你的数据集上运行你自己的测试，以获得更实际的结果。

但这是我对此的看法:

*   Shapefiles 很糟糕，因为它们有很多文件，并且有一些限制
*   如果没有别的，至少在你的 geojson 服务上启用 DEFLATE
*   TopoJSON 处理复杂，编码昂贵
*   TopoJSON 提供了疯狂的压缩，特别是在有许多共享线的多边形上
*   MsgPack 对文本进行了很好的压缩，但是大部分压缩都丢失了
*   LZMA/XZ 增加了一点复杂性，但是它对较大的文件有很好的增益

因此，如果您能够负担得起只编码一次，并且有多余的资源，那么使用 XZ 的 TopoJSON 将提供最大的价值。如果你必须实时编码/解码:GeoJSON with XZ。如果你能腾出磁盘，提供两个:TopoJSON 和 GeoJSON 与 XZ 和 GZ。``