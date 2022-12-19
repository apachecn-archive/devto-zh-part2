# Java 重写等于重写 Java 中的“等于”方法

> 原文：<https://dev.to/havarem/override-equals-method-in-java-2b3j>

在 Java 中,`==`操作符是一个虚假的朋友。它只能在 primitive 上正常工作，如果你在任何其他引用的`Object`上使用它，它会让你哭。

## 基本

根据经验，每个`equals`方法都应该这样开始:

```
 @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }

        if (!(obj instance ClassName)) {
            return false;
        }

        // custom logic here
    } 
```

Enter fullscreen mode Exit fullscreen mode

ClassName 应该被你的类的类型所取代。之后，我通常会应用以下解决方案之一。

### 该类只作为一个相关字段

在类只有一个相关字段的情况下，我将自动返回字段的`==`操作(对于原语)或`equals`。

```
 public class Person

    private String fullname;

    @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }

        if (!(obj instance Person)) {
            return false;
        }

        Person converted = (Person)obj;

        return this.fullname.equals(converted.fullname);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 该类为多个相关字段

我通常有两种选择。

#### `toString()`包含所有相关字段

如果有东西可以帮助你编写代码，就没有必要增加类的复杂性。不需要测试`instanceof`，因为`toString`到处都有定义，也不需要因为同样的原因进行强制转换。

```
 public class Person

    private String firstname;
    private String lastname;

    @Override
    public String toString() {
        return this.firstname + " " + this.lastname;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }

        return this.toString().equals(obj.toString());
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

不强制成为`toString`。有时，其他方法也能完成同样的工作。您需要记住，在比较之前，其他方法需要测试和转换对象。

#### 没有方法帮助

在这种情况下，我通常会层叠`if`直到最后一个相关字段。

```
 public class Person

    private String firstname;
    private String lastname;
    private Date dob;

    @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }

        if (!(obj instance Person)) {
            return false;
        }

        Person converted = (Person)obj;

        if (!(this.firstname.equals(converted.firstname)) {
            return false;
        }

        if (!(this.lastname.equals(converted.lastname)) {
            return false;
        }

        return this.dob.equals(converted.dob);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我知道我大概可以做这样的事情:

```
 return this.firstname.equals(converted.firstname) && this.lastname.equals(converted.lastname) && this.dob.equals(converted.dob); 
```

Enter fullscreen mode Exit fullscreen mode

我不太喜欢这个，因为它主要是很难阅读。我知道 Java 可能会用这些语句来优化字节码，然而，我相信在大多数情况下，这并不重要。

别忘了，为了能够使用`HashMap`之类的东西，你还需要覆盖`hashcode`。以下是主要规则:

*   如果两个实例相等，那么两个`hashcode`必须相等；
*   如果两个实例不相等，那么两个`hashcode`不应该相等。

符合第二条规则的好的`hashcode`算法将使`HashMap`和`HashSet`的使用更加有效。