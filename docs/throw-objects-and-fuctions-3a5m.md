# “抛出”对象和函数

> 原文：<https://dev.to/aravindballa/throw-objects-and-fuctions-3a5m>

你知道你可以在 Javascript 中抛出对象/函数吗？对象意味着，对程序运行有用的对象，而不仅仅是对调试有用。这里有一个很短的给你！

让我们来看看演示一个毫无用处的缓存的简单代码。

```
 const cache = new Map();

    const read = key => cache.get(key);

    const store = (key, value) => {
      cache.set(key, value);
    }

    const fakeExternalRes = key => Math.random(0, 888);

    const readResource = async key => {
      let value;

      // try reading from cache
      // if not found, fetch it from external resource
      try {
        value = read(key);
        if(!value) throw key;
      } catch (key) {
        value = fakeExternalRes(key);
        store(key, value);
      }

      return value;
    }

    store('asdf', 'world');
    console.log(readResource('hello'));
    console.log(readResource('hello'));

/*
Output:
0.4123278447585672
0.4123278447585672
*/ 
```

在这里，我们抛出`key`并捕获它，实际上是从外部资源获取它并存储在缓存中。有点酷的方式。

这里有一个问题，我们通常在异常情况下使用`throw`,而不是在正常情况下。所以我们必须确保我们不会扰乱团队中其他成员的心态。

这是脸书团队在`react-cache`包中使用的。我刚刚用这个演示了一个非常简单的东西。但是你可以通过下面的链接了解他们是如何实现的。

[https://github . com/Facebook/react/blob/master/packages/react-cache/src/react cache . js # L281-L305](https://github.com/facebook/react/blob/master/packages/react-cache/src/ReactCache.js#L281-L305)

保重。