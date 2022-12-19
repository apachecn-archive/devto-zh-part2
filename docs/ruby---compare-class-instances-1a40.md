# 代码提示- Ruby:比较类实例

> 原文：<https://dev.to/avraammavridis/ruby---compare-class-instances-1a40>

在之前的文章中，我写了如何在 Javascript 中比较对象。在 Ruby 中，我们可以使用 Comparable 模块做同样的事情。我们在类中包含了可比较的模块，并定义了一个伪操作符方法`<=>`。假设我们又有一个汽车类，看起来像这样:

```
class Car
  attr :speed

  def initialize(speed)
    @speed = speed
  end
end 
```

现在让我们通过包含可比较的模块和定义`<=>`伪操作符来使它们的实例具有可比性。

```
class Car
  include Comparable
  attr :speed

  def <=>(other)
    self.speed <=> other.speed
  end

  def initialize(speed)
    @speed = speed
  end
end

car1 = Car.new(100)
car2 = Car.new(120)
car3 = Car.new(90)

p car2 > car1 # true
p car3 > car2 # false

cars = [car1, car2, car3]

p cars.sort() # [#<Car:0x000055aec8add4b0 @speed=90>, #<Car:0x000055aec8add500 @speed=100>, #<Car:0x000055aec8add4d8 @speed=120>] 
```