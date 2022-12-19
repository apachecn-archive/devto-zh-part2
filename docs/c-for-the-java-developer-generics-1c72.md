# 面向 Java 开发人员的 C#:泛型

> 原文：<https://dev.to/rnowif/c-for-the-java-developer-generics-1c72>

在我进入 C#世界的旅程中，我想谈谈泛型。Java 和 C#语言中都存在泛型，但是它们的实现*非常*不同。这篇博文旨在解释两者的异同。

TL；DR Java 泛型是骗人的，C#泛型不是。

## Java 泛型是个谎言

Java 5 中引入了泛型。在此之前，你必须操作`Objects`并将它们转换成想要的类型:

```
List apples = new ArrayList();
apples.add(new Apple());
// This is really a list of objects, so the cast is required
Apple firstApple = (Apple) apples.get(0); 
```

Enter fullscreen mode Exit fullscreen mode

在前面的代码片段中，您应该注意到，绝对没有什么可以阻止您向列表中添加一个`Banana`并使程序在运行时崩溃。同样，这段代码在最新版本的 Java(也就是我们所说的 Java 11)中仍然有效。至少可以说，这种方法不是很安全，并且没有像我们预期的那样充分利用类型系统。

从 Java 5 开始，可以使用通用版本的`List`类:

```
List<Apple> apples = new ArrayList<Apple>();
apples.add(new Apple());
// The cast is not required any more thanks to the generics
Apple firstApple = apples.get(0); 
```

Enter fullscreen mode Exit fullscreen mode

事情是这样的，在运行时，前面的两个片段是严格等价的。事实上，Java 泛型在编译过程中被移除，产生的字节码只处理`Objects`和类型转换。这叫做*型擦除*。因此，在 Java 中不可能写出这样的代码:

```
/**
 * Filter objects of the given type
 */
<T> List<T> filterObjectsOfType(List<Object> objects) {
  List<T> filteredObjects = new ArrayList<>();
  for (Object o : objects) {
      if (o instanceof T) {
          filteredObjects.add((T) o);
      }
  }
  return filteredObjects;
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上，`T`的类型在运行时被删除，并且`instanceof`操作不能被执行。这就是为什么类对象经常作为方法的参数传递:

```
/**
 * Filter objects of the given type
 */
<T> List<T> filterObjectsOfType(List<Object> objects, Class<T> clazz) {
  List<T> filteredObjects = new ArrayList<>();
  for (Object o : objects) {
      if (clazz.isInstance(o)) {
          filteredObjects.add((T) o);
      }
  }
  return filteredObjects;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法可以这样调用:

```
List<String> stringsOnly = filterObjectsOfType(Arrays.asList("hello", 2), String.class);
// stringsOnly contains only "hello" 
```

Enter fullscreen mode Exit fullscreen mode

## C#泛型是一个运行时特性

另一方面，C#泛型是完全不同的东西。实际上，真正的类型是在运行时保存的，使用这种类型来编写这种代码是可能的:

```
/**
 * Filter objects of the given type
 */
public IEnumerable<T> FilterObjectsOfType<T>(IEnumerable<object> objects)
{
    List<T> filteredObjects = new List<T>();
    foreach (var obj in objects)
    {
        if (obj is T)
        {
            filteredObjects.Add((T) obj);
        }
    }
    return filteredObjects;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法可以这样调用:

```
IEnumerable<String> stringsOnly = FilterObjectsOfType<String>(new List<object>(new object[] { "hello", 2 }));
// stringsOnly contains only "hello" 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在 Java 中，泛型用于编写类型安全的代码，但是这种特性是有限的，并且由于类型擦除机制，代码有时会很笨拙。在 C#中，它是一个运行时特性，使用起来更加简单。

PS:我意识到我在片段中写的代码并不地道，但是我想让 Java 和 C#代码尽可能地相似，以便能够只关注泛型的用法。