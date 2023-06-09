# 如何使用 square 的 picasso library 和 firebase storage

> 原文：<https://dev.to/premithk/how-to-use-squares-picasso-library-with-firebase-storage-1j21>

最近，我正在将一个应用的后端从 parse 迁移到 firebase。向 firebase 的实时数据库的转移只需很小的调整就能轻而易举。当我想将 firebase 存储中的图像加载到 android Imageview 时，遇到了一个障碍。现有的代码已经使用了 square 中优秀的 picasso 来进行图像缓存等等。早些时候，我将图像作为 blobs 存储在我的解析数据库中。Firebase 存储有这种格式的图像位置。GS://appname . app spot . cimg/1 . jpg。

一种选择是将图像位置(基于 http)加载到实时数据库的一个字段中，并使用 picasso 访问它。这个特别的应用程序有很多图片，我不想走这条路。在阅读毕加索的文档时，我发现了毕加索的请求处理程序。[https://square . github . io/Picasso/2 . x/Picasso/com/square up/Picasso/request handler . html](https://square.github.io/picasso/2.x/picasso/com/squareup/picasso/RequestHandler.html)

实际上，这个实现很容易编码，也足够简单。以下是要点