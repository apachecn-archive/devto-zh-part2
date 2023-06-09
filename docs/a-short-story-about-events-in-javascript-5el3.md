# 一个关于 Javascript 事件的小故事

> 原文：<https://dev.to/migueloop/a-short-story-about-events-in-javascript-5el3>

在 JavaScript 中，事件流过程由三个概念完成:

*   事件捕获
*   事件目标
*   事件冒泡

> 事件是在你正在编程的系统中发生的动作或事件，系统会告诉你，这样你就可以根据需要以某种方式对它们做出响应。

## 事件冒泡

事件冒泡是指事件从最深的元素或目标元素开始到它的父元素，然后是它的所有祖先，从下到上。目前，所有现代浏览器都将事件冒泡作为事件流的默认方式。

### 举例

```
<div id="parent">
 <button id="child">Child</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
var parent = document.querySelector('#parent');
    parent.addEventListener('click', function(){
    console.log("Parent clicked");
});

var child = document.querySelector('#child');
    child.addEventListener('click', function(){
    console.log("Child clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

执行将于:
结束

`Child clicked
Parent clicked`

当您单击按钮时，事件从内部事件目标(id 为子级的按钮)传递到文档。按以下顺序单击事件传递:

[![bubbling](img/5d06b5fe434974e39033431636577ce6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nN-q40LK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0i1b7ra0xw3bjltzkyc.png)

如果要停止事件冒泡，可以使用 event.stopPropagation()方法。

## 事件捕捉

事件捕获是事件从顶层元素开始到目标元素。现代浏览器默认不支持事件捕获，但是我们可以通过 JavaScript 代码来实现。

```
<div id="parent">
 <button id="child">Child</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
var parent = document.querySelector('#parent');
var child = document.querySelector('#child');
parent.addEventListener('click', function(){
    console.log("Parent clicked");
},true);
child.addEventListener('click', function(){
    console.log("Child clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

执行将于:
结束

`Parent clicked
Child clicked`

[![capturing](img/094c59a186df9483fe46427135a57799.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_apBw3uN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/crhfkfu5168ah5g1y67m.png)

## 结论

在事件流中，事件目标有两个阶段，一个是事件捕获结束，一个是事件冒泡开始。

[![all](img/aa36a8cd44d68adaf969638e8aef92b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1j07Bq16--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mz1kgfil7cyog5esxbxi.png)