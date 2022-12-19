# 使用 LazyInitializer 的示例。确保初始化

> 原文：<https://dev.to/briannipper/example-of-using-lazyinitializerensureinitialized-3e6g>

[![](../Images/29fffa83ea6883a80af56a3282d43bab.png)](https://2.bp.blogspot.com/-I6pWygiwGZ8/W4xk7uMLyaI/AAAAAAAAHKU/n3QXOFMoZtk8ZiLqt59Hwgq8WEnC8TVTgCLcBGAs/s1600/LazyMeme.jpg) 
当想让系统更有效率时，想想懒惰是有帮助的。中的一个有用工具。NET 工具带是系统下的静态类。线程命名空间 LazyInitializer。特别是这个类包含了一个方法[确保初始化](https://docs.microsoft.com/en-us/dotnet/api/system.threading.lazyinitializer.ensureinitialized?view=netframework-4.7.1)

该方法使用起来非常简单，并提供了一种方便的方法来确保基于已经填充的目标属性值只调用一次初始化。

例如，如果需要加载一个文件作为应用程序中值设置的一部分，可以使用 EnsureInitialized 方法。

下面是一个使用类来阐释使用模式的派生示例。

使用系统；使用系统。集合。泛型；命名空间示例。ensure initialized { class Program { static void Main(string[]args){ var configuration service = new configuration service()；var configInfo = configuration service。ConfigInfo()；foreach(config info 中的 var config item){ Console。WriteLine(configItem。值)；}控制台。read key()；} } class configuration service { private Dictionary _ simple example；public configuration service(){ System。threading . lazy initializer . ensure initialized(ref _ simple example，Init)；} private Dictionary Init() { //下面的内容将被替换为一些开销很大的操作，如从磁盘读取文件。返回新词典{ {1，" Hello"}，{2，" World " } }}公共字典 ConfigInfo(){ return _ simple example；} } }