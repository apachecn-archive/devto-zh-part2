# 使用普通 Javascript 的组件事件委托

> 原文：<https://dev.to/hybrid_alex/component-event-delegation-with-vanilla-javascript-1aag>

上周我写了关于在执行 Javascript 之前检查页面上的元素以避免错误的文章。如果只有一个元素可以连接事件侦听器，那么这个例子就很好。如果你有多个组件和触发器，我们应该考虑使用事件委托来保持代码的性能。

因此，让我们重构上周的模态组件代码，以利用事件委托。

```
function modal() {
  var trigger = document.querySelector('[data-modal-trigger]');
  trigger.addEventListener('click', function() {
    // Modal functionality code
  }, false);
}
modal(); 
```

首先让我们将`event`传递给我们的模态函数。然后我们可以使用`closest()`方法来查看我们的模态触发器是否被点击了。

既然我们已经重构了模态函数。我们可以在文档中附加一个事件监听器。现在，每当文档中发生单击时，我们就运行我们的模态代码并传递 click 事件。

```
function modal(event) {
  if (!event.target.closest('[data-modal-trigger]') return;
  // Modal functionality code
}

document.addEventListener('click', function(event) {
  modal(event);
}, false); 
```

这允许我们向我们的站点添加多个组件，并且只有一个事件监听器，如下所示。

```
function modal(event) {
  if (!event.target.closest('[data-modal-trigger]') return;
  // Modal functionality code
}

function accordion(event) {
  if (!event.target.closest('[data-accordion-trigger]') return;
  // Accordion functionality code
}

document.addEventListener('click', function(event) {
  modal(event);
  accordion(event);
}, false); 
```

请注意，这假设您的组件都是由一个单击事件运行的，但情况可能并不总是如此。您可能还有需要观察`mouseover`或`mouseout`事件的组件，这需要单独的事件监听器。

检查 [CodePen](https://codepen.io/alexcarpenter/pen/pZKeKV) 以查看运行中的代码。