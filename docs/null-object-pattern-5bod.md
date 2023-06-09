# 空对象模式

> 原文：<https://dev.to/avraammavridis/null-object-pattern-5bod>

很多时候，函数接受可以为 null 的对象引用，我们倾向于添加 if 语句来处理将 null 传递给函数的特殊情况，或者提供默认响应，或者什么都不做。在下面的例子中，我们的 calculateSpeed 函数需要一个具有 getSpeed 函数的对象。在 Javascript 中，我们必须做类似这样的事情:

```
class Car {
  getSpeed(){
    return 100;
  }
}

const calculateSpeed = function(vehicle){
  if(vehicle && typeof vehicle.getSpeed === 'function'){
    return vehicle.getSpeed();
  } else {
    return 50;
  }
}

const car1 = new Car();

console.log(calculateSpeed(car1)); // 100
console.log(calculateSpeed()); // 50 
```

但是有一个更好的方法来实现它。使用[空对象模式](https://en.wikipedia.org/wiki/Null_object_pattern)我们可以创建一个充当 vehicle 的类，姑且称之为`DefaultMovable`。

```
class DefaultMovable {
  getSpeed(){
    return 50;
  }
} 
```

我们的`DefaultMovable`类提供了默认功能(也就是我们前面代码片段中的 else)，这样我们就可以避免 if/else 语句。

```
class Car {
  getSpeed(){
    return 100;
  }
}

class DefaultMovable {
  getSpeed(){
    return 50;
  }
}

const calculateSpeed = function(vehicle = new DefaultMovable()){
  return vehicle.getSpeed();
}

const car1 = new Car();

console.log(calculateSpeed(car1)); // 100
console.log(calculateSpeed()); // 50 
```

这种模式的 UML 图如下所示:

[![](img/cd1108039c4360537cf6b994273edccd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0m3SAYoU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ac5vmqznoh60opulysdv.png)

Ruby 中的相同示例如下所示:

```
class Car
  def get_speed
    100
  end
end

class DefaultMovable
  def get_speed
    50
  end
end

def getSpeed(vehicle = DefaultMovable.new)
  vehicle.get_speed();
end 
```

这只是一个模式，因为每个模式都有它的优点和缺点，所以请根据您的用例仔细应用它。(该示例是为了演示模式的抖动而虚构的)