# 代码提示- Javascript:比较类实例

> 原文：<https://dev.to/avraammavridis/javascript--compare-class-instances-1i3f>

很多时候，我们想要比较一个类的实例，也就是对象，例如在你开发一个游戏的时候，你想要比较一个游戏对象的实例，例如`Car` :

```
class Car {
  constructor(speed){
    this.speed = speed
  }
} 
```

假设我们想要比较一辆汽车的实例(或者对它们的数组进行排序/过滤)。显然我们可以做:

```
const car1 = new Car(100)
const car2 = new Car(120)
const car3 = new Car(90)

console.log(car2.speed > car1.speed) // true

const sorted = [car1, car2, car3].sort((a,b) => {
  return a.speed - b.speed;
}); 
```

这工作得非常好，但是我们可以做得更好，所以我们不必每次想要比较两辆车，或者排序/过滤它们的数组时都键入`.speed`。我们可以在 Car 类中定义一个 valueOf 方法，该方法返回实例的“值”。

```
class Car {
  constructor(speed){
    this.speed = speed
  }

  valueOf(){
    return this.speed;
  }
}

const car1 = new Car(100)
const car2 = new Car(120)
const car3 = new Car(90)

console.log(car2 > car1) // true 
```