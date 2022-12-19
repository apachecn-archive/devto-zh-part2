# 为什么 Java 接口不可怕(只是严格)

> 原文：<https://dev.to/bertilmuth/why-java-interfaces-arent-terrible-just-strict-3de2>

最近的一篇文章描述了处理 Java 接口时的一些问题，这些问题来自一种允许鸭式输入的语言。

底线是这样的。有一个界面:

```
public interface DynamoImpl {
  public PutItemResult putItem(PutItemRequest putItemRequest);

  public GetItemResult getItem(GetItemRequest getItemRequest);
} 
```

Enter fullscreen mode Exit fullscreen mode

而且还有一类`AmazonDynamoDB`。那个类有上面两个方法，还有更多的。现在，程序员 John 只想将`AmazonDynamoDB`的这两个方法暴露给他程序的其余部分。他想在课堂上使用它们`Database` :

```
public class Database {
    public DynamoImpl db;

    // code removed for simplicity

    public Database(DynamoImpl db) {
        this.db = db;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在:在一种支持 duck 类型的语言中，您可以将类`AmazonDynamoDB`的一个实例传递给构造函数，并调用这两个方法(因为该类拥有它们)。但在 Java 中不是这样。你需要传入*字面上的*`implements``DynamoImpl`接口的一个类的实例(或者扩展一个这样做的类)。但是`AmazonDynamoDB`没有实现那个接口。

在 Java 中实现相同目标的最简单的方法是创建一个包装器，委托给一个`AmazonDynamoDB`实例:

```
public class AmazonDynamoDbWrapper implements DynamoImpl{

    private AmazonDynamoDB amazonDynamoDB;

    public AmazonDynamoDbWrapper(AmazonDynamoDB amazonDynamoDB) {
        this.amazonDynamoDB = amazonDynamoDB;
    }

    @Override
    public PutItemResult putItem(PutItemRequest putItemRequest) {
        return amazonDynamoDB.putItem(putItemRequest);
    }

    @Override
    public GetItemResult getItem(GetItemRequest getItemRequest) {
        return amazonDynamoDB.getItem(getItemRequest);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

是的，有点冗长，但是一旦你理解了模式，就非常简单了。现在可以将这个类的一个实例传递给`Database`的一个实例。

生产代码到此为止。测试呢？

最简单的解决方案是提供一个`DynamoImpl`接口的最小实现，比如:

```
public class StubbedDynamoImpl implements DynamoImpl {

    @Override
    public PutItemResult putItem(PutItemRequest putItemRequest) {
        return new PutItemResult(/* Test data here */);
    }

    @Override
    public GetItemResult getItem(GetItemRequest getItemRequest) {
        return new GetItemResult(/* Test data here */);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，在测试中，您可以将这个类的一个实例传递给`Database`实例的构造函数:

```
public class DatabaseTest {

    @Test
    public void createsDatabase() {
        Database database = new Database(new StubbedDynamoImpl());
        // Whatever your test assertions are, here
    }
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您只想公开两个方法，就不需要花哨的模仿框架。这是我能想到的最简单的解决办法。希望有用。

关于 Java 约定的一句话:请不要对接口使用 Impl 后缀。这应该用于具体的实现类(如果有的话)，否则你可能会让很多人感到困惑。