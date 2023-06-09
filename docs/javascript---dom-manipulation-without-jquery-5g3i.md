# 不使用 jQuery 的 Javascript - DOM 操作

> 原文：<https://dev.to/wiaio/javascript---dom-manipulation-without-jquery-5g3i>

[![alt text](img/a908fd92a49d415e92684baec14de6d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DRq6p1Cp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-29/1530273375-100728-blog-04.png)

通常，开发人员倾向于依赖像 jQuery 这样的库来进行基本的 DOM 操作，比如查询元素、添加/删除类、添加事件监听器等。许多资源都指出 jQuery 是解决这些问题的解决方案，但是实际上，仅仅使用它作为基础可能对您的网站来说是大材小用。普通的 Javascript DOM API 比基本的 DOM 操作更有能力，而放弃 jQuery 将意味着页面加载速度的降低！下面是普通 Javascript 的基本内容。

# 查询 DOM 元素

有几种方法可以用来在普通 Javascript 中查询 DOM 元素，最常用的是`document.querySelector()`。该方法将 CSS 选择器作为参数，并返回该选择器的第一个匹配项。下面是使用方法:

```
var mySelector = document.querySelector('.my-selector');
var paragraph = document.querySelector('p');
var hiddenInput = document.querySelector('input[type="hidden"]'); 
```

Enter fullscreen mode Exit fullscreen mode

如果您想要获得选择器的所有出现(例如，页面上的所有`<h1>`标签)，请使用`document.querySelectorAll()`。这个方法返回一个[节点列表](https://developer.mozilla.org/en-US/docs/Web/API/NodeList)。下面是它的使用方法:

```
var headings = document.querySelectorAll('h1');
var listLinks = document.querySelectorAll('li a');
var items = document.querySelectorAll('.my-selector > p strong'); 
```

Enter fullscreen mode Exit fullscreen mode

要进一步操作它们，您可以简单地对它们进行迭代。有几种方法可以做到这一点，最简单的是`for`循环或`for of`循环。

```
var headings = document.querySelectorAll('h1');

for (var i = 0; i < headings.length; i++) {
  var heading = headings[i];
  // do something with heading here
}

for (var heading of headings) {
  // do something with heading here
} 
```

Enter fullscreen mode Exit fullscreen mode

在最近的浏览器中，还可以在节点列表上使用`forEach()`方法:

```
var headings = document.querySelectorAll('h1');

headings.forEach(function(heading) {
  // do something with heading here
}); 
```

Enter fullscreen mode Exit fullscreen mode

查询 DOM 元素的其他方法有:`document.getElementById()`用于获取匹配一个 ID 的第一个元素，`document.getElementsByClassName()`用于获取匹配一个类的元素列表(返回一个 [HTMLCollection](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection) )，以及`document.getElementsByTagName()`用于获取匹配一个选择器的 *live* 元素列表(返回一个 [HTMLCollection](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection) )。

`getElementsByTagName()`和`querySelectorAll()`的区别在于`getElementsByTagName()`返回一个 *live* 列表，这意味着如果一个元素被动态添加，它将会更新。

# 添加/删除类别

一个真正常见的 DOM 操作是在元素中添加或删除一个类。幸运的是，这很容易用普通的 Javascript 实现。

```
var button = document.querySelector('button');

button.classList.add('small');
button.classList.remove('large');
button.classList.toggle('active'); // if element has class 'active' then remove it, otherwise add it 
```

Enter fullscreen mode Exit fullscreen mode

# 添加事件监听器

用 vanilla JS 添加一个事件监听器(比如‘click’，‘scroll’等)非常简单。只需在 DOM 元素上使用`addEventListener()`方法，传入一个事件类型和一个回调函数(类似于 jQuery 的 [`on`方法](http://api.jquery.com/on/))。下面是它的使用方法:

```
var button = document.getElementById('button');

button.addEventListener('click', function(event) {
  // do something here
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 把所有的东西放在一起

既然我们已经学习了 DOM 操作的基础，让我们把它们放在一起:

```
// Query all button elements
var buttons = document.querySelectorAll('button');

// Iterate through the buttons
for (var button of buttons) {
  // Add a 'click' event listener to each button
  button.addEventListener('click', function() {
    // Add a class to the clicked button
    this.classList.add('active');
  });
} 
```

Enter fullscreen mode Exit fullscreen mode