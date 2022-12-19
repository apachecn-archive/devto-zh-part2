# Codetip - Javascript:移除参数顺序依赖性并提供默认值

> 原文：<https://dev.to/avraammavridis/codetip---javascript-remove-argument-order-dependency-and-provide-defaults-hph>

函数签名中的参数顺序是错误和挫折的常见来源。假设您有下面的 Vehicle 类，它的构造函数接受值来实例化一个对象。以错误的顺序传递值很容易导致不容易捕捉的错误。

```
class Vehicle {
  constructor(speed, weight, power, color, brand){
    this.speed = speed;
    this.weight = weight;
    this.power = power;
    this.color = color;
    this.brand = brand;
  }
} 
```

显然，我们可以通过向函数传递一个对象来避免顺序依赖性。

```
class Vehicle {
  constructor(args = {}){
    Object.keys(args)
      .forEach(argument => this[argument] = args[argument]);
  }
} 
```

但是有一个问题，如果我们希望我们的构造函数有一些缺省值，万一没有提供呢？在第一个例子中，我们将这样写我们的类:

```
class Vehicle {
  constructor(speed = 100, weight = 1500, power = 111, color = "red", brand = "Kia"){
    this.speed = speed;
    this.weight = weight;
    this.power = power;
    this.color = color;
    this.brand = brand;
  }
} 
```

为了在我们使用一个对象作为单个参数的情况下提供默认值，我们可以创建一个返回默认值的 defaults 属性，并将传递的 args 对象与构造函数中的默认值合并。defaults 属性也是我们的类所期望的属性的一种文档形式。

```
class Vehicle {
  constructor(args = {}){
    args = { ...this.defaults, ...args };
    Object.keys(args)
      .forEach(argument => this[argument] = args[argument]);
  }

  get defaults(){
    return {
      speed: 100,
      power: 1000
    }
  }
} 
```

子类可以提供自己的`defaults`

```
class Car extends Vehicle {
  get defaults(){
    return {
      speed: 150,
    }
  }
} 
```

或者甚至扩展基类
提供的`defaults`

```
class Car extends Vehicle {
  get defaults(){
    return {
      ...Vehicle.prototype.defaults,
      speed: 150,
    }
  }
} 
```