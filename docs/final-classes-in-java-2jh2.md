# Java 中的最终类

> 原文：<https://dev.to/bertilmuth/final-classes-in-java-2jh2>

已经有[一些](https://dev.to/amarlearning/why-it-is-not-possible-to-extend-a-final-class-in-java-5467) [讨论](https://dev.to/vasilvestre/explain-what-really-happening-when-you-put-final-in-a-class-like-im-five-2n26)关于`final`关键字应用于 Java 中的类。简单地说:当一个类被声明为 final 时，你不能创建它的子类。

现在举个例子会有帮助。
说这是你的班级:

```
public final class PopBand {
    public void hereIAm() {
        System.out.println("Here I am");
    }
    public void willYouSendMeAnAngel() {
        System.out.println("Will you send me an angel?");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你试图创建一个子类，编译器会给你一个错误:

```
// This will cause a compile error
public class RockBand extends PopBand {
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可以通过使用 composition 来扩展这个类。也就是说:你创建了另一个包含原始类的对象的类。然后你委托给原作者(对于原作者的方法)。你添加新的方法。像这样:

```
public class PopRockBand {
    /**
     * Create an object of the class PopBand and assign it to containedBand
     */
    PopBand containedBand = new PopBand();

    /**
     * Delegate to methods of PopBand 
     */

    public void hereIAm() {
        containedBand.hereIAm();
    }

    public void willYouSendMeAnAngel() {
        containedBand.willYouSendMeAnAngel();
    }

    /**
     * This method is specific to PopRockBand class
     */
    public void rockYouLikeAHurricane() {
        System.out.println("Rock you like a hurricane");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是证明作文有效的主要类:

```
public class Gig {
    public static void main(String[] args) {
        PopRockBand scorpions = new PopRockBand();

        // Play "Send me an angel"
        scorpions.hereIAm();
        scorpions.willYouSendMeAnAngel();

        // Play "Rock you like a hurricane"
        scorpions.hereIAm();
        scorpions.rockYouLikeAHurricane();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode