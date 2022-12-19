# 在 C#中不处理任何东西——空对象模式

> 原文：<https://dev.to/balazsbotond/dealing-with-nothing-in-c---the-null-object-pattern-2e9c>

<small>这是我的[原帖](https://blog.lambdaklub.hu/2016/11/21/a-semmi-kifejezese-c-nyelven-1-resz-a-nullobjektum/)的匈牙利语英文版。塞缪尔·泽勒在 [Unsplash](https://unsplash.com) 上的照片。</small>

空引用无处不在，是我们程序员经验中不可或缺的一部分，很难想象一个没有它的世界。然而，它是如此多的挫折和实际财务损失的来源，因此非常值得努力思考一些替代方案。

但是到底是什么问题呢？

让我们想象一下，我们有一个客户可以下订单的系统。订单包含引用产品的行项目，它们存储订购的数量。他们还可以根据指定的单价和数量来计算价格。

```
public class LineItem
{
    public Product Product { get; }
    public decimal Quantity { get; }
    public decimal Price => Product.UnitPrice * Quantity;

    public LineItem(Product product, decimal quantity)
    {
        if (quantity <= 0)
            throw new ArgumentOutOfRangeException(nameof(quantity));
        if (product == null)
            throw new ArgumentNullException(nameof(product));

        Product = product;
        Quantity = quantity;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不同类型的单个折扣可以应用于一个订单，因此我们使用[策略模式](https://en.wikipedia.org/wiki/Strategy_pattern)将订单从折扣的计算中分离出来。

```
public interface IDiscount
{
    decimal Calculate(decimal orderTotal);
}

public class CouponDiscount : IDiscount
{
    public CouponDiscount(string couponCode, decimal rate) { /* ... */ }

    public decimal Calculate(decimal orderTotal) => orderTotal * rate;
}

public class FrequentBuyerDiscount : IDiscount { /* ... */ } 
```

Enter fullscreen mode Exit fullscreen mode

给定订单总数，折扣对象可以根据自己的规则集计算降价。

order 类提供了添加行项目和折扣的方法，以及一个在应用折扣后返回总数的属性。

```
public class Order
{
    public IEnumerable<LineItem> LineItems => lineItems.AsReadOnly();
    public decimal Total => discount.Calculate(totalBeforeDiscount);

    private decimal totalBeforeDiscount => lineItems.Sum(i => i.Price);
    private IDiscount discount; // initialized to null
    private List<LineItem> lineItems;

    public Order()
    {
        lineItems = new List<LineItem>();
    }

    public void AddLineItem(LineItem lineItem)
    {
        if (lineItem == null) throw new ArgumentNullException(nameof(lineItem));

        lineItems.Add(lineItem);
    }

    public void AddDiscount(IDiscount discount)
    {
        if (discount == null) throw new ArgumentNullException(nameof(discount));

        this.discount = discount;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面那个班好像完全无辜。什么会出错？我们甚至为所有参数添加了空检查。但还是很有可能会抛出一个 *NullReferenceException* 。你能发现这个漏洞吗？

```
var order = new Order();

order.AddLineItem(new LineItem(product1, 2));
order.AddLineItem(new LineItem(product2, 1));

Console.WriteLine($"Total: {order.Total}"); // BOOM!!! 
```

Enter fullscreen mode Exit fullscreen mode

哦，我们忘记加上折扣了！

但是等等。创建没有折扣的订单是完全有效的需求。让我们修改代码来支持它。

```
public decimal Total =>
    discount == null ? totalBeforeDiscount : discount.Calculate(totalBeforeDiscount); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码可以工作，但是它肯定不漂亮。即使我们把美学问题放在一边，每次访问折扣字段都必须检查 null 也是一个灾难。哪怕忘记一次，也会出现一个 *NullReferenceException* 。此外，我们需要复制默认逻辑，每次都返回打折前的总数。这种逻辑没有很好地封装。

幸运的是，我们可以做得更好。让我们看看在添加行项目时，我们是如何处理完全相同的问题的。我们在构造函数中将 *lineItems* 字段初始化为一个安全的默认值，即空列表。

```
public Order()
{
    lineItems = new List<LineItem>();
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做之后，就不需要检查 *AddLineItem* 方法中的 *lineItems* 是否为空。

```
lineItems.Add(lineItem); 
```

Enter fullscreen mode Exit fullscreen mode

我们能想象这样一个安全的折扣默认值吗？当然我们可以，那将是一个 *NoDiscount* 类，其中 *Calculate* 方法简单地返回 *orderTotal* 参数而不做任何修改。

```
public class NoDiscount : IDiscount
{
    public decimal Calculate(decimal orderTotal) => orderTotal;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样的默认对象被称为空对象。现在我们可以用它作为构造函数
中*折扣*的初始值

```
public Order()
{
    lineItems = new List<LineItem>();
    discount = new NoDiscount();
} 
```

Enter fullscreen mode Exit fullscreen mode

并去掉 *Total* 属性中有问题的空检查。

```
public decimal Total => discount.Calculate(totalBeforeDiscount); 
```

Enter fullscreen mode Exit fullscreen mode

作为最后一条建议，建议在应用空对象模式时保持克制。当默认行为无害且可预测时，这是一个非常好的解决方案。但是，如果空对象的行为对消费者来说不明显，那么获得一个 *NullReferenceException* 实际上比让一个设计糟糕的空对象意外行为更有用。