# Android-Kotlin Digest #4:简单的读写方式

> 原文：<https://dev.to/itscoderslife/android-kotlin-digest-4--a-simple-way-to-read-and-write-5fi2>

在这里，我将向您概述:

*   如何从命令行读取用户输入？

*   如何从文件中读取数据？

*   如何写入文件？

如何从命令行读取用户输入？

```
println(" ->Welcome to our Bakery<-")

println("============================")

println("Hello! How can I help you?")

var userResponse = readLine()

println("Sure!!" + "It's fresh prepared just now!")

var userNextResponse = readLine()

println("You are very welcome! Have a nice day!")

println("================== ====================")

println(" :: Your Conversation with the Clerk :: ")

println("Clerk: Hello! How can I help you?")

println("Customer: $userResponse")

println("Clerk: Sure!! It's fresh prepared just now!")

println("Customer: $userNextResponse")

println("Clerk: You are very welcome! Have a nice day!") 
```

Enter fullscreen mode Exit fullscreen mode

如何从文件中读取数据？

```
val reader = FileReader("itsdamslife.txt")
var char: Int?
try {
    do {
        char = reader.read()
        print(char.toChar())
    } while (char != -1)
} catch (exception: Exception) {
    print("There was an exception : $exception.message")
} 
```

Enter fullscreen mode Exit fullscreen mode

如何写入文件？

```
try {

    var writer = FileWriter("itsdamslife.txt", true)
    writer.write(message + "\n")
    writer.close()
} catch (exception: Exception) {
    println("There was an exception :  $exception")
} 
```

Enter fullscreen mode Exit fullscreen mode