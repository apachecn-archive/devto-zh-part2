# JavaScript 中的 uuid。

> 原文：<https://dev.to/pranay_rauthu/uuid-in-javascript-obc>

在 react js 应用程序中使用组件数组时。react 抱怨我们为列表项提供一个唯一的值键。我们通过添加数组索引作为键来避免这个警告。但是最近我知道，当你更新数组时，这实际上会导致错误(在我的一个项目中亲身经历)。所以我们需要设置一个唯一的键而不是数组索引。

在 googling 了生成惟一 id 之后，我知道了一些生成 uid 的解决方案和技巧。我想到了下面的解决方案。我相信这将有很少的机会产生两个独特的价值。

我决定根据以下参数生成一个 id。

*   时间
*   随机性
*   计数器

**时间**:让我们假设我们在
女士那里得到一个当前时间的唯一值

```
new Date().getMilliseconds(); 
```

Enter fullscreen mode Exit fullscreen mode

为了更好的独特性。我们也可以使用`performance.now`功能。更多关于`performance.now`的信息可以在[这里](https://developers.google.com/web/updates/2012/08/When-milliseconds-are-not-enough-performance-now?hl=en)找到。

**随机性**:随机数可以通过调用`Math.random()`生成

计数器:万一我们连续得到两个唯一的数字。为了安全起见，我们需要一个计数器，它在一定限度后会复位。一个循环链表将能够完成这个任务。

所以把这些放在一起。

```
 function uniqueId() {
        const firstItem = {
            value: "0"
        };
        /*length can be increased for lists with more items.*/
        let counter = "123456789".split('')
            .reduce((acc, curValue, curIndex, arr) => {
                const curObj = {};
                curObj.value = curValue;
                curObj.prev = acc;

                return curObj;
            }, firstItem);
        firstItem.prev = counter;

        return function () {
            let now = Date.now();
            if (typeof performance === "object" && typeof performance.now === "function") {
                now = performance.now().toString().replace('.', '');
            }
            counter = counter.prev;
            return `${now}${Math.random().toString(16).substr(2)}${counter.value}`;
        }
    }

    const randomIdGenerator = uniqueId();

    randomIdGenerator(); // 3519936476425aaa4f2a048 
    randomIdGenerator(); // 3519936472474f1e8119427
    randomIdGenerator(); // 35199364800000017a40a2d1eaa226 
```

Enter fullscreen mode Exit fullscreen mode

这个 [stackoverflow](https://stackoverflow.com/questions/105034/create-guid-uuid-in-javascript) 线程有更多的解决方案。

如果你知道任何其他生成 uuid 的技术，请在下面评论。