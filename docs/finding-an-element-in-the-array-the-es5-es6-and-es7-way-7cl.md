# 寻找数组中的元素(ES5、ES6 和 ES7 方式)

> 原文：<https://dev.to/wangonya/finding-an-element-in-the-array-the-es5-es6-and-es7-way-7cl>

这将是一个快速的。

假设你想检查一个数组中是否存在一个特定的元素。有几种方法可以做到这一点:

#### ES5

##### `indexOf()`

`indexOf`返回找到的第一个匹配项的索引，如果没有找到则返回`-1`。

```
// check if a Fortnite ninja exists in the array
const ninjas = ['Alchemist', 'Brawler', 'Skirmisher', 'Harvester']

console.log(ninjas.indexOf('Brawler')) // 1
console.log(ninjas.indexOf('Harvester')) // 3
console.log(ninjas.indexOf('Assassin')) // -1 (doesn't exist) 
```

Enter fullscreen mode Exit fullscreen mode

#### `lastIndexOf()`

`lastIndexOf()`返回找到的最后一个匹配项的索引，如果没有找到则返回`-1`。

```
// check if a Fortnite ninja exists in the array
// note that 'Brawler' exists twice
const ninjas = ['Alchemist', 'Brawler', 'Skirmisher', 'Harvester', 'Brawler', 'Stonefoot']

console.log(ninjas.lastIndexOf('Brawler')) // 4 (last one returned)
console.log(ninjas.lastIndexOf('Harvester')) // 3
console.log(ninjas.lastIndexOf('Assassin')) // -1 (doesn't exist) 
```

Enter fullscreen mode Exit fullscreen mode

#### ES6

##### `find()`

`find()`方法返回满足提供的测试函数的数组中第**个**元素的**值**。否则返回 undefined。

```
const ninjas = [
                {name: 'Alchemist'}, 
                {name: 'Brawler'}, 
                {name: 'Skirmisher'}, 
                {name: 'Harvester'}
               ]

console.log(ninjas.find(ninja => ninja.name === 'Harvester')); // {name: "Harvester"}
console.log(ninjas.find(ninja => ninja.name === 'Assassin')); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

##### `findIndex()`

返回满足所提供测试函数的数组中第一个元素**的**索引**。否则返回-1。** 

```
const ninjas = [
                {name: 'Alchemist'}, 
                {name: 'Brawler'}, 
                {name: 'Skirmisher'}, 
                {name: 'Harvester'}
               ]

console.log(ninjas.findIndex(ninja => ninja.name === 'Harvester')); // 3
console.log(ninjas.findIndex(ninja => ninja.name === 'Assassin')); // -1 
```

Enter fullscreen mode Exit fullscreen mode

#### ES7

##### `includes()`

`includes()`方法确定一个数组是否包含某个元素，根据情况返回 true 或 false。例如，如果`a`包含`value`
，则`a.includes(value)`返回`true`

```
const ninjas = ['Alchemist', 'Brawler', 'Skirmisher', 'Harvester']

console.log(ninjas.includes('Brawler')); // true
console.log(ninjas.includes('Assassin')); // false 
```

Enter fullscreen mode Exit fullscreen mode

如果`a`在`i`
位置后(或位置处)包含`value` **，则`a.includes(value, i)`返回 true**

```
const ninjas = ['Alchemist', 'Brawler', 'Skirmisher', 'Harvester']

console.log(ninjas.includes('Skirmisher', 1)); // true
console.log(ninjas.includes('Skirmisher', 2)); // true
console.log(ninjas.includes('Skirmisher', 3)); // false 
```

Enter fullscreen mode Exit fullscreen mode

让我知道我可能错过的任何其他有趣的方式:)