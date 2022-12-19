# Kotlin 异常处理

> 原文：<https://dev.to/tedhagos/kotlin-exception-handling-1n30>

Kotlin 处理异常的方法类似于 Java。有点吧。它使用 *try-catch-finally* ，就像在 Java 中一样。所以，你关于*试捕*如何工作的知识很好地适用于科特林。下面的代码应该很熟悉。它展示了如何打开一个文件的典型代码

```
import java.io.FileNotFoundException
import java.io.FileReader
import java.io.IOException

fun main(args: Array<String>) {
  var fileReader: FileReader
    try {
    fileReader = FileReader("README.txt")
    var content = fileReader.read()
    println(content)
 }
  catch (ffe: FileNotFoundException) {
    println(ffe.message)
  }
  catch(ioe: IOException) {
    println(ioe.message)
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，有什么不同呢？在 Kotlin 中，一切都是未检查的异常。这意味着，try-catch 块是可选的。要不要用由程序员决定。所以，上面的代码，可以这样写(用 Kotlin)。

```
import java.io.FileReader  

fun main(args: Array<String>) {
  var fileReader = FileReader("README.txt")  
  var content = fileReader.read()  
  println(content)
} 
```

Enter fullscreen mode Exit fullscreen mode

那么你如何知道你是否需要使用 try-catch 呢？你现在可能不喜欢这些答案，但从长远来看，这些都是值得遵循的好建议；

1.  你必须知道你正在使用的 API。我知道这对初学者来说不是好消息，但是从这个角度来看，你不会成为初学者太久。你真的应该知道你正在使用的 API。TL；博士在这方面不会为你服务好的
2.  你必须养成对代码进行单元测试的习惯。无论如何，这是一个需要养成的好习惯