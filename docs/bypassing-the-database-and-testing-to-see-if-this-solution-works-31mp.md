# 绕过数据库并测试这个解决方案是否有效

> 原文：<https://dev.to/chenge/bypassing-the-database-and-testing-to-see-if-this-solution-works-31mp>

[![](img/22632943b91adfc1c3bd8de5ccb0e3ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uL_N-0Iq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yu6elcnwi6e83k5w9vd8.png)

几年前我就在思考如何在测试中不依赖数据库，但一直没有找到可行的解决方案。今天看到这篇文章，感觉作者提出的建议似乎可行，也比较简单。
[如何在不使用数据库的情况下测试你正在创建一个记录](https://dev.to/bhserna/how-to-test-that-you-are-creating-a-record-without-using-the-database-3jk7)

我简单验证了一下，没问题。

我打算以后做一个真实的项目验证。

```
class Product < ActiveRecord::Base
end

store = Product

Catalog.add_product(attrs, store) 
```

这是一个例子。这样做的好处是可读性更强，可以模拟存储，避免数据库测试。

嘿，这是测试代码

```
 ###
class DummyProductsStore
  Def self.create(attrs)
  End
end

def store
  DummyProductsStore
end

###

it "creates a record" do
  attrs = { name: "P1", description: "Super Product" }

  # This is the code that expects "store.create(attrs)" to be called
  expect(store).to receive(:create).with(attrs)

  Catalog.add_product(attrs, store)
end 
```

代码中用 store 代替 Product，测试时替换 store。

我用 rspec 测试可行，但问题是模拟后验证码不执行，不知道怎么解决。

# 干净的建筑

如图，业务模型要居中，用例组织代码，控制器和数据库是外围接口。
我感觉这段代码是把模型作为实体，对应的产品类和类方法作为数据库接口。更接近插图的建筑理念。最初的 rails 是以数据为中心组织的，或者说是以数据库为中心。

[![](img/272eeed26fad1e3e5b3f65dd8268ef67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m6KJ9wnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images0.cnblogs.com/i/492619/201403/291649188125475.jpg)

[洁净建筑描述](http://www.cnblogs.com/happyframework/p/3632703.html)

看代码，感觉如何？详细代码请看原代码。