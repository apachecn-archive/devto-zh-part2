# 面向 Java 开发人员的 C#:枚举

> 原文：<https://dev.to/rnowif/c-for-the-java-developer-enums-2864>

枚举是一个非常常见的概念。当然，它也存在于 Java 和 C#中。但是，Java 和 C#枚举没有相同的功能。这篇博文旨在展示他们的不同之处。

在 Java 中，枚举非常像常规类:它们可以实现接口并拥有方法。但是，它们不能继承其他类或被显式实例化。它们可以被视为已经继承了虚拟“enum”类的`final`类(或 C#中的`sealed`类)，只有私有构造函数和一组预定义的实例(enum 的值)。

例如，让我们以 HTTP 状态代码为例。在 Java 中，可以写这样的代码:

```
enum HttpStatus implements Comparable<HttpStatus> {
  OK(200), SERVER_ERROR(500), NOT_FOUND(404);

  private final int code;

  HttpStatus(int code) {
      this.code = code;
  }

  // This is a regular instance method
  int code() {
      return code;
  }

  // This is the implementation of the Comparable interface
  @Override
  int compareTo(HttpStatus o) {
      // Http statuses will be sorted by status code
      return Integer.compare(code, o.code);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
HttpStatus status = HttpStatus.OK;

// The 'code' method can be invoked like any other method
int code = status.code(); 
```

Enter fullscreen mode Exit fullscreen mode

在 C#中，枚举只是伪装的整数。之前的代码片段可以在 C#中模拟，只是因为`code`属性恰好是一个`int`。否则，这将是非常复杂的有同样的行为:

```
enum HttpStatus {
  // int value of the enum can be forced to a specific value
  OK = 200,
  NOT_FOUND = 404,
  SERVER_ERROR = 500
} 
```

Enter fullscreen mode Exit fullscreen mode

```
HttpStatus status = HttpStatus.OK;

// The enum can be casted to an int to get its value
int code = (int) status; 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，Java 枚举比它们的 C#对应物强大得多。当我写 Java 代码时，我经常使用这些特性，如果我每天都要写 C#代码，我想我会想念它们。