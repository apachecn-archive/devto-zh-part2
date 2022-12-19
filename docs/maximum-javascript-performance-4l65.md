# 最高的 JavaScript 性能

> 原文：<https://dev.to/kayis/maximum-javascript-performance-4l65>

*[由公共图书馆专业人员 NRW 在 Flickr 上复盖图像。](https://www.flickr.com/photos/fst_oeb_nrw/)*

如果你有点像我，你可能会想很多如何让你的软件更好。一个主要原因是，将性能**提高到最大值**。今天我将告诉你高级 JS 开发人员对你隐藏的保守的性能秘密。

## 1。仅全局变量

阴影会使你的软件金属模型变得杂乱，而且你经常需要访问你在开始你的项目时没有预料到的东西。如果您总是重用您的全局名称空间，您可以安全地避免混乱您的内存。

```
// instead of 
const a = "Hello";
const b = "World";

// write
a = "Hello";
b = "World"; 
```

Enter fullscreen mode Exit fullscreen mode

## 2。短变量名

不要过分强调变量的长度，解析时间是现代 JavaScript 中的一个大问题，所以当你的全局名称空间最终填满时，只使用一个以上的字母作为变量名。

```
// instead of 
loginTimeout = 2300;

// write
a = 2300; 
```

Enter fullscreen mode Exit fullscreen mode

## 3。不要使用函数

函数是万恶之源！它们不仅通过间接方式使代码变得混乱，增加了代码的精神负担，还降低了代码的速度。所以当你有疑问的时候，一定要内嵌你的代码。

```
// instead of
const substract = (x, y) => x - y;
a = substract(2000, substract(500, 50));

// write
a = 2000 - (500 - 50); 
```

Enter fullscreen mode Exit fullscreen mode

## 4。`setTimeout`一路

通常你需要快速完成工作，而同步的方式并不能满足你的需求。最好的办法是用`setTimeout`调用替换所有的循环，这样事件循环会处理好一切。

```
// instead of
let x = 1; 
while (x < 1000) {
  console.log(x);
  x++;
}

// write
x = 1;
const fastLoop = () => {
  console.log(x);
  x++;
  if (x < 1000) setTimeout(fastLoop, 1);
}
fastLoop(); 
```

Enter fullscreen mode Exit fullscreen mode

## 5。通过`with`进行对象访问

如果您在生活中编写过大量有意义的 JavaScript 代码，那么您可能最终会得到大量需要深入研究的对象。当你编码时，去掉所有这些减慢你速度的点操作符不是很好吗？嗯，接线员会帮你搞定的。要解析的代码越少，意味着代码越快。

```
// instead of 
console.log(u.profile.name);
console.log(u.profile.birthday);
console.log(u.profile.town);

// write
with(u.profile) {
  console.log(name);
  console.log(birthday);
  console.log(town);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。缓存的随机性

去下一家桌面商店买一袋骰子。它们将帮助您获得随机值，您可以将这些值缓存在常量中以备后用。计算随机值相当慢，应该静态完成。

```
// instead of 
myRandomNumber = Math.random();

// write
r = 17; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

有许多简单的方法可以加速你的代码，甚至初学者也可以遵循。已经是 2018 年 4 月的*1 号了，不明白为什么要和慢软件和解。*

*愚人节！*