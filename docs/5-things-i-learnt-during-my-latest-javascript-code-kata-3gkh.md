# 我在最新的 Javascript 代码 Kata 中学到的 5 件事

> 原文：<https://dev.to/erikpischel/5-things-i-learnt-during-my-latest-javascript-code-kata-3gkh>

有时我在 codewars.com 做代码形。这是一种解决计算机科学相关问题的有趣方式，在解决问题的过程中学习，尤其是从其他人的解决方案中学习。

今天我用 Javascript 完成了“生成树”这个动作。我偶尔会用 Javascript 写一个事件处理程序，但是我没有太多“现代”Javascript 的经验。以下是我从观察其他人的解决方案中学到的。

# 解构

我从我的 Scala 类和 Clojure 中了解到这一点。

可以给变量分配数组元素:

```
var a, b, rest; 
[a, b] = [10, 20];
console.log(a); // expected output: 10 
console.log(b); // expected output: 20 
[a, b, ...rest] = [10, 20, 30, 40, 50]; 
console.log(rest); 
// expected output: [30,40,50] 
```

Enter fullscreen mode Exit fullscreen mode

所以“…rest”是分配数组的其余部分。

在处理嵌套数组时，这也是很好的语法糖。例如，当“边”是一对数组时:

```
// sort edges by weight
edges.sort(([edge_a, a], [edge_b, b]) => a - b); 
```

Enter fullscreen mode Exit fullscreen mode

有对象正在被析构:

```
var o = {p: 42, q: true};
var {p, q} = o;
console.log(p); // 42
console.log(q); // true 
```

Enter fullscreen mode Exit fullscreen mode

甚至给新变量分配属性

```
var o = {p: 42, q: true};
var {p: foo, q: bar} = o; 
console.log(foo); // 42 
console.log(bar); // true 
```

Enter fullscreen mode Exit fullscreen mode

请参见 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)了解更多信息。

# Spread 运算符使用数组文字创建一个数组

使用数组文字从另外两个数组创建一个数组:

```
const sets = {}; 
//... 
// new array with sets[a] elements and sets[b] elements 
const set = [...sets[a], ...sets[b]]; 
```

Enter fullscreen mode Exit fullscreen mode

# 对象是关联数组(又名地图)

虽然我已经知道这一点，这刷新了我的 JS 知识。

首先，你可以在没有声明的情况下给对象添加属性:

```
let obj = {}; // anonymous object 
obj.height=2; // create new property "height" and assign value 
console.log(obj.height); // 2 
```

Enter fullscreen mode Exit fullscreen mode

第二，你可以使用数组索引符号代替点符号，使用属性名作为索引:

```
let obj = {}; 
obj['height'] = 2; 
console.log(obj['height']); // 2 
```

Enter fullscreen mode Exit fullscreen mode

一个解决方案使用这个来保存物体的加权边，就像我在正确的地图物体中做的一样:

```
let set = {}; 
edges.filter(e => e[0][1] !== e[0][0]).forEach(e => { if (!set[e[0]] || minOrMaxFunc(set[e[0]], e[1])>00) { set[e[0]] = e[1]; } }); 
```

Enter fullscreen mode Exit fullscreen mode

第三，方法也是一种属性。在同一个解决方案中，巧妙地选择了“minor max func”(“minor max”参数为“min”或“max”):

```
function makeSpanningTree(edges, minOrMax) { 
  let minOrMaxFunc = { min: (a, b) => a - b, max: (a, b) => b - a }[minOrMax]; 
  // ... 
} 
```

Enter fullscreen mode Exit fullscreen mode

它用两个方法创建一个对象:“min”和“max”，然后引用参数中给定的一个。如果“minOrMax=min”，则返回对“min”方法的引用。

# 字符串是数组

用字符串解构作品:

```
let [a,b] = 'ABC'; 
console.log(a); // "A" 
console.log(b); // "B" 
```

Enter fullscreen mode Exit fullscreen mode

并且可以索引字符串:

```
const s = "ABC"; 
s[1]; // "B" 
```

Enter fullscreen mode Exit fullscreen mode

# “var”vs .“let”

当然，用“现代”JS 写的解到处都是“let”和“const”。我只是让自己相信 let 和 var 之间的区别:

首先，在一个使用“var”的块中声明的变量在该块之外是可见的，并且在声明之前是“已知的”:

```
function f() { 
  console.log(v); // undefined 
  { var v = 3; } 
  console.log(v); // 3 
} 
```

Enter fullscreen mode Exit fullscreen mode

一个块可能是一个 for 循环。

使用 let 声明的变量在块外是不可见的，并且在声明之前是“未知的”:

```
function f() { 
  console.log(v); // Reference error 
  { let v = 3; } 
  console.log(v); // Reference error } 
```

Enter fullscreen mode Exit fullscreen mode

第三，你不能使用 let:
来重新声明一个变量

```
var a = 0; 
var a = 1; // OK 
let b = 0; 
let b = 1; // not OK 
```

Enter fullscreen mode Exit fullscreen mode

所以基本上，“let”是声明变量的一种合理方式。

[![](img/78e9fc2ec1675257a15042a228ab0a1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q4E8LaAX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.epischel.de/analytics/piwik.php%3Fidsite%3D3%26rec%3D1%26url%3Dhttps%253A%252F%252Fon-sw-integration.epischel.de%252F2018%252F08%252F30%252Fthings-i-learnt-during-my-latest-javascript-code-kata%252F%253Fpk_campaign%253Dfeed%2526pk_kwd%253Dthings-i-learnt-during-my-latest-javascript-code-kata%26action_name%3DThings%2BI%2Blearnt%2Bduring%2Bmy%2Blatest%2BJavascript%2BCode%2BKata%26urlref%3Dhttps%253A%252F%252Fon-sw-integration.epischel.de%252Ffeed%252F)