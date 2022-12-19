# Parcelize:用 Kotlin 进行样板文件自由打包

> 原文：<https://dev.to/xuhaibahmad/parcelize-boilerplate-free-parcelization-with-kotlin-16ka>

[序列化](https://en.wikipedia.org/wiki/Serialization)是将一个对象转换成字节流的过程，以便于不同的 I/O 操作使用。示例包括写入数据库、内存或文件。在 Android 环境中，最常见的用例是在活动和片段之间传递对象。

主要思想是能够以最原始的方式保存对象的状态，并能够在需要时重新创建它。相反的过程称为反序列化。

## Java 序列化& Android

我们的老伙计 Java 处理序列化的方式非常简单——你只需要实现一个[标记接口](https://stackoverflow.com/questions/25850328/marker-interfaces-in-java)，也就是`java.io.Serializable`，这个类的对象将自动序列化。

```
public class MyObject implements Serializable {
    private int data;
} 
```

> 这看起来很完美，让我们把它留在这里，在任何地方都使用这个令人敬畏的界面。

但是等等！这种过度简化的代价是巨大的。这个过程包括在幕后反射和创建大量的辅助对象。这会导致过多的垃圾收集和较差的性能。

为了解决这个问题，Android 自带了机制——[Parcelable](https://developer.android.com/reference/android/os/Parcelable.html)。尽管它更健壮，但为了达到这个目的，它牺牲了简单性。这导致了更多的样板代码，但却是完全无反射的。

实现这一点的关键是，我们必须使用`writeToParcel`方法显式定义序列化过程。下面是普通的具有单个字段的可打包类的样子:

```
public class MyObject implements Parcelable {
    private int data;

     public int describeContents() {
         return 0;
     }

     public void writeToParcel(Parcel out, int flags) {
         out.writeInt(data);
     }

     public static final Parcelable.Creator<MyObject> CREATOR
             = new Parcelable.Creator<MyObject>() {

         public MyObject createFromParcel(Parcel in) {
             return new MyObject(in);
         }

         public MyObject[] newArray(int size) {
             return new MyObject[size];
         }
     };

     private MyObject(Parcel in) {
         data = in.readInt();
     }
 } 
```

我们已经使用它很多年了，但是如果你一直在使用类似于 [Parceler](https://github.com/johncarl81/parceler) 的东西，或者如果你现在已经习惯了最小化的 Kotlin 代码，那么你的反应可能是这样的:

[![Gross!](../Images/214c0f5a37adb19dea583c09cf19e22e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dZBM-5_t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.giphy.com/media/pVAMI8QYM42n6/giphy.gif%23center)

## 科特林来救援了(又来了？！)

Kotlin 1.1.4 的发布增加了使用`@Parcelize`注释生成 Android Parcelable 实现的实验支持。它带回了`Serializable`的简单，但没有反射。相反，它使用注释处理来实现这一点。

### 设置

因为它现在是一个实验性的特性，所以您需要通过将下面的代码片段添加到您的`gradle`文件中来启用对它的支持:

```
androidExtensions {
    experimental = true
} 
```

另外，如果 gradle 文件中没有`apply plugin: ‘kotlin-android-extensions’`的话，把它添加进去。

### 用法

使任何类可打包的过程只涉及添加两件事——一个`@Parcelize`注释和用`Parcelable`实现标记类。

下面是我们的`MyObject`类的样子:

```
@Parcelize
data class MyObject (val data: Int) : Parcelable 
```

就这样了！当与数据类结合时，我们得到了比 Java 的`Serializable`更简单的代码。

* * *

对于建议和疑问，只需[联系我](http://linkedin.com/in/xuhaibahmad)。