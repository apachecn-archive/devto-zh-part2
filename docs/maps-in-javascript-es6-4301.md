# Javascript ES6 中的地图

> 原文：<https://dev.to/damcosset/maps-in-javascript-es6-4301>

## 简介

ES6 引入了很多新东西。我已经写过 [*集*](https://dev.to/damcosset/sets-in-javascript-es6-3fdh) ，所以今天我们就来探索一下*地图*。那些是什么？一个*映射*是一个无序的键-值对列表，其中键**和值**可以是任何类型。

## 问题解决了

开发人员试图在 ES6 之前实现地图，但是由于 Javascript 中对象属性的处理方式，出现了一些问题。在对象中，每个属性都必须是字符串。所以，如果你给一个对象一个不同类型的键，它将被强制转换成一个字符串。

```
let map = {}

map[5] = 4
map[{}] = 'An object'

// { '5': 4, '[object Object]': 'An object' } 
```

Enter fullscreen mode Exit fullscreen mode

可以看到，我们的 5 变成了‘5’，我们的空对象变成了'[object Object]'。这是一些严重的限制！

在 ES6 中，映射使用 Object.is()方法来比较键，就像集合使用它们的值一样。映射也不会将每个键都变成一个字符串，而是允许每种类型。

```
 Object.is(5, '5') // false
Object.is({}, {}) // false 
```

Enter fullscreen mode Exit fullscreen mode

## 构造函数

那么，如何创建新地图呢？通过使用`new Map()`。你也可以用数组初始化一个映射:

```
const map = new Map()
// Map {}

const map = new Map([[5, 42], ["name", "Paul"], ["age", 45]])
// Map { 5 => 42, 'name' => 'Paul', 'age' => 45 } 
```

Enter fullscreen mode Exit fullscreen mode

在数组的数组中，每个数组表示一个键值对。每个数组中的第一项将成为键，第二项将成为值。这种结构可能看起来很奇怪，但是这是确保我们允许任何类型的数据作为键的最好方法。

## 绘制地图方法

要与地图进行交互，有几种方法可供您使用。

*   `set(key, value)`方法将一对添加到地图中。
*   `get(key)`方法从地图中检索一个值。如果什么都没有找到，`get`方法将返回`undefined`。
*   `has(key)`方法检查键是否存在于映射中。返回真或假。
*   `delete(key)`方法从映射中删除键及其值。
*   `clear()`方法从映射中删除所有的键和值。
*   最后，映射有一个`size`属性，它返回映射中键/值对的数量。

```
const map = new Map()

map.set(5, "Hello")
map.set("5", "World")
map.set("John", "The revelator")
map.size // 3
// Map { 5 => 'Hello', '5' => 'World', 'John' => 'The revelator' }

map.get(5) // Hello
map.has('5') // true
map.get('Random') // undefined
map.has('John') // true

map.delete('5')
map.size // 2
// Map { 5 => 'Hello', 'John' => 'The revelator' }

map.clear()
map.size // 0
// Map {} 
```

Enter fullscreen mode Exit fullscreen mode

## 地图中的对象关键点

正如我前面提到的，对象可以用作地图中的键。

```
const map = new Map()
let obj1 = {}
let obj2 = {}

map.set(obj1, 12)
map.set(obj2, "OBJECT")
map.size // 2
// Map { {} => 12, {} => 'OBJECT' } 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，尽管我们使用了两个空的对象作为键，但是我们在地图中使用了这些对象的*引用*。因此，用于比较键的 Object.is()返回 false。再次注意，对象没有被强制转换成字符串。

## 迭代

您可以使用 forEach()遍历地图。传递的回调接收三个参数:值、键和我们正在使用的映射。

```
const map = new Map([[5, 42], ["name", "Paul"], ["age", 45]])

map.forEach((value, key, thisMap) => {
    console.log(`${key} => ${value}`)
    console.log(thisMap === map)
})

//5 => 42
//true

//name => Paul
//true

//age => 45
//true 
```

Enter fullscreen mode Exit fullscreen mode

## 弱地图

弱映射遵循同样的弱集原理。在弱贴图中，每个**键**都必须是一个对象。弱映射用于存储弱对象引用。那是什么意思？

```
const map = new Map()
let obj1 = {}
map.set(obj1, 12)
//Map { {} => 12 }
obj1 = null // I remove the obj1 reference
// Map { {} => 12 } // But the reference still exists in the map anyway 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们对象的引用仍然存在于地图中。移除其他位置的参考不会将其从地图中移除。它不会被垃圾收集来释放内存。在某些情况下，您可能希望优化内存使用并避免内存泄漏。这就是 WeakMap 为你做的。如果一个对象的引用在你程序的其他地方消失了，它也将从 WeakSet 中被删除。

```
const map = new WeakMap()

let obj = {} // creates a reference to obj
map.set(obj, 12) // stores the reference inside the WeakMap as a key
map.has(obj) // true
map.get(obj) // 12

obj = null /* removes the reference. Will also remove it from the WeakMap because there are no other references to this object */

map.has(obj) // false
map.get(obj) // undefined
console.log(map) // WeakMap {}

// obj is gone from the WeakMap 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:这只在对象被存储为**键**而不是值时有效。如果一个对象存储为一个值，并且所有其他引用都消失了，那么它不会从 WeakMap 中消失。弱映射键是弱引用，而不是弱映射值。

也可以用数组的数组初始化 WeakMap，就像地图一样。不同之处在于，因为每个键必须是一个对象，所以每个数组中的第一项必须是一个对象。如果试图将非对象键放入 WeakMap 中，将会引发错误。

**注意** : WeakMap 没有*大小*属性

## 弱地图用例

WeakMap 的一个可能的用例是跟踪 DOM 元素。通过使用 WeakMap，您可以将 DOM 元素存储为键。一旦元素被移除，对象将被垃圾回收以释放内存。

```
const map = new WeakMap()
const element = document.querySelector(".button")

map.set(element, "Buttons")

map.get(element) // "Buttons"

element.parentNode.removeChild(element) // remove the element
element = null // removes reference

// WeakMap now empty! 
```

Enter fullscreen mode Exit fullscreen mode

WeakMap 的另一个实际用途是存储私有对象数据。在 ES6 中，所有对象属性都是公共的。那么你会怎么做呢？在 ES5 中，你可以这样做:

```
 var Car = (function(){

    var privateCarsData = {}
    var privateId = 0

    function Car(name, color){
        Object.defineProperty(this, "_id", {value: privateId++})

        privateCarsData[this._id] = {
            name: name,
            color: color
        }
    }

    Car.prototype.getCarName = function(){
        return privateCarsData[this._id].name
    }

    Car.prototype.getCarColor = function(){
        return privateCarsData[this._id].color
    }

    return Car
}()) 
```

Enter fullscreen mode Exit fullscreen mode

这是你在 ES5 中最接近真正私有的数据了。这里，汽车定义被包装在一个直接调用的函数表达式(IIFE)中。我们有两个私有变量，privateCarsData 和 privateId。privateCarsData 存储每个汽车实例的私有信息，privateId 为每个实例生成一个惟一的 Id。

当我们调用`Car(name, color)`时，_id 属性被添加到 privateCarsData 中，它接收一个具有 name 和 color 属性的对象。 *getCarName* 和 *getCarColor* 用这个来检索数据。_id 作为密钥。

数据是安全的，因为私人汽车数据在生命之外是不可访问的。_id 是公开的。问题是没有办法知道汽车实例何时被销毁。因此，当一个实例消失时，我们不能适当地更新 privateCarsData，它将总是包含额外的数据。

```
const Car = (function(){

    const privateCarsData = new WeakMap()

    function Car(name, color){
        // this => Car instance
        privateCarsData.set(this, {name, color})
    }

    Car.prototype.getCarName = function(){
        return privateCarsData.get(this).name
    }

    Car.prototype.getCarColor = function(){
        return privateCarsData.get(this).color
    }

    return Car
}()) 
```

Enter fullscreen mode Exit fullscreen mode

这个版本对 privateCarsData 使用了 WeakMap，而不是对象。我们将使用 Car 实例作为键，所以我们不需要为每个实例生成一个惟一的 id。关键字是`this`，值是一个包含名称和颜色的对象。 *getCarName* 和 *getCarColor* 通过将`this`传递给 get 方法来检索值。现在，每当 Car 实例被销毁时，privateCarsData 中引用该实例的键将被垃圾收集以释放内存。

## 结论

任何时候你想只使用对象键，弱贴图将是你最好的选择。将优化内存并避免内存泄漏。然而，弱地图让你很难看到他们有什么。不能使用 *forEach()* ，没有*大小*属性，没有 *clear()* 方法。如果您需要检查内容，请使用常规地图。显然，如果你想使用非对象键，你也必须使用常规映射。