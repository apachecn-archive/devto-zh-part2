# 自制的可观测量。第二部分:作文

> 原文：<https://dev.to/vonheikemen/homemade-observables-part-2-composition-576f>

在上一篇文章中，我们了解了可观测量，它们是什么，以及我们如何从头开始建造一个可观测量。现在我们将学习如何操纵现有的可观测量来扩展它们的行为。

这一次我们将创建一些实用函数，并稍微调整一下我们当前的可观察实现，以便用它们创建更灵活的特性。

## 一切始于运营商

运算符是允许我们用一系列函数来扩展可观察对象的行为的函数。每个函数都可以将一个可观察值作为数据源，并返回一个新的可观察值。

让我们保留数组主题并创建一个 **map** 操作符，该操作符模拟数组原型的本地 map 函数，但用于观察。我们的操作符会这样做:取一个值，应用一个函数来执行一些转换并返回一个新值。

让我们试一试:

第一步，获取转换函数和数据源，然后返回一个我们可以使用的新的可观察值。

```
function map(transformFn, source$) {
  return Observable(function(observer) {
    // to be continued...
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

最酷的部分来了，我们得到的源是一个可观测的，这意味着我们可以订阅它来得到一些值。

```
function map(transformFn, source$) {
  return Observable(function(observer) {
    // remember to keep returning values from your functions.
    // This will return the unsubcribe function
    return source$.subscribe(function(value) {
      // to be continued...
    });
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要将转换的结果传递给观察者，这样我们就可以在订阅这个新的可观察对象时“看到”它。

```
function map(transformFn, source$) {
  return Observable(function(observer) {
    return source$.subscribe(function(value) {
      // ****** WE ARE HERE ******
      var newValue = transformFn(value);
      observer.next(newValue);
      // *************************
    });
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有很多缩进和返回。如果我们一直使用箭头函数，我们可以“解决”这个问题。

```
function map(transformFn, source$) {
  return Observable(observer => 
    source$.subscribe(value => observer.next(
      transformFn(value)
    ))
  );
}

// that didn't do much for the indentation. 
// Well, you can't win them all. 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要使用运营商，现在这将是它。

```
function fromArray(arr) {
  return Observable(function(observer) {
    arr.forEach(value => observer.next(value));
    observer.complete();
  });
}

var thisArray = [1, 2, 3, 4];
var plusOne   = num => num + 1;
var array$    = map(plusOne, fromArray(thisArray));

array$.subscribe(value => console.log(value)); 
```

Enter fullscreen mode Exit fullscreen mode

这感觉不是很链。为了使用更多的地图功能，我们必须嵌套它们，这是不对的。别担心，我们一会儿就会谈到那个。

## 管万物

我们将创建一个助手函数，允许我们使用一个或多个操作符来修改一个可观测的源。

这个函数将接受一个函数集合，集合中的每个函数将使用前一个函数的返回值作为输入。

首先，我将展示这是如何作为一个独立的助手函数来完成的。

```
function pipe(aFunctionArray, initialSource) {
  var reducerFn = function(source, fn) {
    var result = fn(source);
    return result;
  };

  var finalResult = aFunctionArray.reduce(reducerFn, initialSource);

  return finalResult;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里， **reduce** 函数在数组中循环，对数组中的每个元素执行 **reducerFn** 。在第一个循环的 reducerFn 中， **source** 将是 **initialSource** ，在其余的循环中 **source** 将是从 reducerFn 返回的任何内容。 **finalResult** 只是 reducerFn 返回的最后一个结果。

通过一些修改(包括 ES6+ goodness ),我们可以在我们的可观察工厂中使用这个帮助器函数，使它更加灵活。我们的新工厂现在看起来会是这样的:

```
function Observable (subscriber) {
  var observable = {
    subscribe: observer => subscriber(SafeObserver(observer)),
    pipe: function (...fns) {
      return fns.reduce((source, fn) => fn(source), observable);
    }
  }

  return observable; 
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要做一件事来确保我们的操作符与这个新的管道功能兼容。例如，我们当前的**映射**操作符同时期望**转换 Fn** 和**源**。这不会发生在管道内部。将不得不把它分成两个函数，一个需要初始的必要参数来使它工作，另一个需要源的可观测值。

有几种方法可以做到这一点。

```
// Option 1
function map(transformFn) {
  // Instead of returning an observable 
  // we return a function that expects a source
  return source$ => Observable(observer => 
    source$.subscribe(value => observer.next(
      transformFn(value)
    ))
  );
}

// Option 2
function map(transformFn, source$) {
  if(source$ === undefined) {
    // we'll return a function 
    // that will "remember" the transform function
    // and expect the source and put in its place.

    return placeholder => map(transformFn, placeholder);
  }

  return Observable(observer => 
    source$.subscribe(value => observer.next(
      transformFn(value)
    ))
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以这样扩展我们的可观测性:

```
var thisArray = [1, 2, 3, 4];
var plusOne   = num => num + 1;
var timesTwo  = num => num * 2;

var array$ = fromArray(thisArray).pipe(
  map(plusOne),
  map(timesTwo),
  map(num => `number: ${num}`),
  // ... many more operators
);

array$.subscribe(value => console.log(value)); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备创建更多的操作符。

## 锻炼时间

假设我们有一段代码，它每秒向控制台打印一个“时间字符串”，并在五秒钟后停止(因为为什么不这样做)。这个家伙就在这里:

```
function startTimer() {
  var time = 0;
  var interval = setInterval(function() {
    time = time + 1;

    var minutes = Math.floor((time / 60) % 60).toString().padStart(2, '0');
    var seconds = Math.floor(time % 60).toString().padStart(2, '0');
    var timeString = minutes + ':' + seconds;

    console.log(timeString);

    if(timeString === '00:05') {
      clearInterval(interval);
    }
  }, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码没有任何问题。我的意思是，它起作用了，它是可预测的，你需要知道的一切都显而易见。但是你知道，我们正处于重构的情绪中，我们刚刚学到了一些新的东西。我们会把它变成可观察的东西。

首先，让我们创建几个帮助函数来处理格式化和时间计算。

```
function paddedNumber(num) {
  return num.toString().padStart(2, '0');
}

function readableTime(time) {
  var minutes = Math.floor((time / 60) % 60);
  var seconds = Math.floor(time % 60);

  return paddedNumber(minutes) + ':' + paddedNumber(seconds);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们处理时间。setInterval 是一个很好的数据源候选，它需要一个回调函数来产生值，它还有一个“清理”机制。它创造了完美的可观察性。

```
function interval(delay) {
  return Observable(function(observer) {
    var counter   = 0;
    var callback  = () => observer.next(counter++);
    var _interval = setInterval(callback, delay);

    observer.setUnsubscribe(() => clearInterval(_interval));

    return observer.unsubscribe;
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这是惊人的，我们现在有真正可重复使用的方法来设置和破坏间隔。

你可能已经注意到我们正在传递一个数字给观察者，我们没有称它为*秒*，因为**延迟**可以是任意的数字。在这里，我们没有跟踪时间，我们只是计算回调已经执行了多少次。为什么？因为我们想让每个可观察的工厂尽可能通用。我们总是可以通过使用操作符来修改它发出的值。

这就是我们如何使用新的区间函数。

```
// pretend we have our helper functions in scope.

var time$ = interval(1000).pipe(
  map(plusOne),
  map(readableTime)
);

var unsubscribe = time$.subscribe(function(timeString) {
  console.log(timeString);

  if(timeString === '00:05') {
    unsubscribe();
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

那更好。但是如果 T1 打扰了我。我觉得那种行为不属于那里。你猜怎么着我将创建一个操作符，它可以在发出五个值后取消订阅间隔。

```
// I'll named "take" because naming is hard.
// Also, that is how is called in other libraries.

function take(total) {
  return source$ => Observable(function(observer) {
    // we'll have our own counter because I don't trust in the values
    // that other observables emits
    var count = 0;
    var unsubscribeSource = source$.subscribe(function(value) {
      count++;
      // we pass every single value to the observer.
      // the subscribe function will still get every value in the stream 
      observer.next(value);

      if (count === total) {
        // we signal the completion of the stream and "destroy" the thing
        observer.complete();
        unsubscribeSource();
      }
    });
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以有一个自毁定时器。最后。

```
// pretend we have our helper functions in scope.

var time$ = interval(1000).pipe(
  map(plusOne),
  map(readableTime),
  take(5)
);

time$.subscribe({
  next: timeString => console.log(timeString),
  complete: () => console.info("Time's up")
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 游乐场

我做了几支笔，这样你就可以摆弄这些东西了。这支笔包含了我为这篇文章写的所有可观察到的相关代码。

这是练习用的笔。

[https://codepen.io/VonHeikemen/embed/VGZXZa?height=600&default-tab=result&embed-version=2](https://codepen.io/VonHeikemen/embed/VGZXZa?height=600&default-tab=result&embed-version=2)

## 结论

我再说一遍，可观测量是一个强大的抽象概念。它们可以让你一次处理一大块数据流。不仅如此，还可以让您拼凑解决方案，这些解决方案可以由通用函数和特定于当前问题定制函数组成。

不过，公平的警告。它们不是所有问题的最终解决方案。你必须决定这种复杂性是否值得。就像在练习中一样，为了获得一些灵活性(我们可以通过其他方式实现)，我们失去了简单的 startTimer。

# 其他来源

谁害怕看得见的东西？
[了解 RxJS 中的 mergeMap 和 switch map](https://netbasal.com/understanding-mergemap-and-switchmap-in-rxjs-13cf9c57c885)
[JavaScript—引擎盖下的可观测量](https://netbasal.com/javascript-observables-under-the-hood-2423f760584)
[Github repository—Zen—observable](https://github.com/zenparsing/zen-observable)
[了解可观测量](https://dev.to/supermanitu/understanding-observables)

* * *

感谢您的阅读。如果你觉得这篇文章有用，并想支持我的努力，请给我买一杯☕咖啡。

[![buy me a coffee](../Images/9e6f0f1293ebfd51e2e5cab78e6e6a08.png)T2】](https://www.buymeacoffee.com/vonheikemen)