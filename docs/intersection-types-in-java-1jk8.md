# Java 中的交集类型

> 原文：<https://dev.to/rnowif/intersection-types-in-java-1jk8>

这篇博文旨在解释当我们期望一个对象实现不同的接口时，如何在 Java 中使用交集类型。

## 界面偏析原理

接口隔离原则(ISP)规定接口应该包含尽可能少的方法。换句话说，一个接口的客户端应该使用这个接口的所有方法。

例如，让我们看看这个`File`接口:

```
interface File {
  Collection<String> readLines();
  void write(String line);
  void deleteFile();
}

class LocalFile implements File {
  // ...
} 
```

作为这个接口的客户，我不太可能需要所有的方法。我可能只想读取文件，在这种情况下，我当然不想删除它。如果我只想删除文件，我可能不想读取它的所有行。

为了避免这种情况，最好将这个接口分成 3 个独立的部分:

```
interface FileReader {
  Collection<String> readLines();
}

interface FileWriter {
  void write(String line);
}

interface FileDestroyer {
  void deleteFile();
}

class LocalFile implements FileReader, FileWriter, FileDestroyer {
  // The concrete class can implement all 3 interfaces
  // ...
} 
```

现在，客户可以只需要它需要的接口，而忽略其他的。

## 界面组合

编写微小的接口有利于增强 ISP 并降低代码的耦合性。然而，当一个客户端想同时读取一个文件*和*写的时候会发生什么呢？

前两个代码片段不会编译，因为其中一个接口没有实现:

```
void readAndWrite(FileReader reader) {
  reader.readLines();
  reader.write("Hello"); // That won't compile since reader does not implement FileWriter
}

void readAndWrite(FileWriter writer) {
  reader.readLines(); // That won't compile since writer does not implement FileReader
  reader.write("Hello");
} 
```

或者，可以传递一个`LocalFile`的实例，但是它在方法和`LocalFile`具体类之间引入了高度耦合，完全违背了接口的全部目的。

```
void readAndWrite(LocalFile file) {
  file.readLines();
  file.write("Hello");
  // That will compile but it is not recommended
} 
```

自从 Java 1.5 和泛型的引入，一个被称为交集类型的特性允许在这种情况下组合接口。

## 路口救援类型

下面的代码使用交集类型来解决需要一个实现几个接口的对象的问题:

```
<T extends FileReader & FileWriter> void readAndWrite(T file) {
  file.readLines();
  file.write("Hello");   
} 
```

符号`&`意味着该方法期望类型`T`同时实现`FileReader`和`FileWriter`接口。

## 结论

交集类型是一个没有在 Java 中广泛使用的特性。然而，它非常强大，因为它允许编写非常小的接口，并根据需要组合它们。从现在开始，再也没有借口去写有几十个完全不相关的方法的大胖接口了！