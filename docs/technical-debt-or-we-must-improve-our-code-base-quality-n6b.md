# 技术债务，或者我们必须提高我们的代码质量

> 原文：<https://dev.to/vrnsky/technical-debt-or-we-must-improve-our-code-base-quality-n6b>

## 在这篇文章中，我将讲述一个重要的代码质量——维护。

1.  问题。
2.  两种方法解决你的技术债务。
3.  静态方法
4.  getter 和 setter
5.  避免 null。
6.  结论。

## 问题

在技术世界中存在着技术债务这样的事实。看起来我们必须花时间改进我们产品的可维护代码库。例如，我们开发 Android 应用程序。我们必须尽快将应用程序交付给最终客户。但总有一天，当你已经不明白应用程序中发生了什么，有许多依赖或冗余的 if - else 块，尽管有可能使一些事情变得更简单。所以花时间改进你的代码库可能是一个好的决定。首先，对你个人来说——你会为此感激你自己，而其他人也会为此感激你。最近我读了约书亚·布洛赫的《有效的 Java》和叶戈尔·布加恩科的《优雅的对象》。这两本书彼此相反。

## 解决技术债务的两种方法

### 静态方法

静态方法很好，可以用构造函数代替。例如:

```
public class Car {

    //Client of this does not may create object by this way. Only static
    private Car() {
    }

    //Create a default car for client.
    public static Car getDefaultCar() {
        return new Car();
    }
} 
```

相反，它优雅的对象告诉我们:

> 静态方法是邪恶的，不要使用它！但是如果你的程序程序员你可以使用它:)

### getter 和 setter

多年来，我们一直认为 getter 和 setter 方法是对象状态的封装。但这不是真的。我们可以通过 setter 方法来影响这个内部状态。例如:

```
public class Cash {
    private int dollars;

    public Cash(int dollars) {
        this.dollars = dollars;
    }

    //Influence on the internal state on the object
    public void setDollars(int dollars) {
        this.dollars = dollars;
    }

    public int getDollars() {
        return this.dollars;
    }
} 
```

t 不是一个拥有可变对象的好方法，更好的方法是拥有不可变的对象，比如`java.lang.String`

```
public class Cash {
    private final Integer dollars;

    public Cash(final Integer dollars) {
        this.dollars = dollars;
    }

    public Integer usd() {
        return this.dollars;
    }
} 
```

在这种情况下，我们没有机会改变对象的内部状态。要展示一种新的现金，我们必须创建一个新的对象。

### 避免`null`

有时候你有一个方法，它把`null`作为参数。例如:

```
public class FileFinder {
    private final File file;

    public FileFinder(final File file) {
        this.file = file;
    }

    public List<String> findByMask(IMask mask) {
        if (mask == null) {
            return mask.all();
        }
        return mask.find(file);
    }

    public static void main(String[] args) {
       IMask mskMask = new Mask("*.msk");
       List<String> mskFiles = new FileFinder(args[0]).find(mskMask);

       //Get all files
       List<String> allFiles = new FileFinder(args[0]).find(null); 
    }
} 
```

这段代码看起来是正确的，但它有许多无用的词。不要使用 null，使用 stub 对象。对于存根对象，上面的代码将如下所示:

```
public class FileFinder {
    private final File file;

    public FileFinder(final File file) {
        this.file = file;
    }

    public List<String> findByMask(IMask mask) {
        return mask.find(file);
    }

    public static void main(String[] args) {
        IMask withouAnyMask = new WithoutAnyMaskImpl();
        List<String> allFiles = new FileFinder(args[0]).find(withoutAnyMask); 

    }
} 
```

对我个人来说，第二种选择更具可读性和可理解性。也不产生难看的`NullPointerException`。

## 结论

我建议把这两本书都看一遍，对如何提高代码质量有更深的理解。我还建议考虑代码库的可维护性，因为这真的很重要。

跟着我；)