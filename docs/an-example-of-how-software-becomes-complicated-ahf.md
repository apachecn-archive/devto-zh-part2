# 软件变得复杂的一个例子

> 原文：<https://dev.to/jorinvo/an-example-of-how-software-becomes-complicated-ahf>

***让我们用 JavaScript 写一个缓存，看看什么叫化繁为简。*T3】**

我们总是听到软件开发人员说类似于*我们应该保持事情简单*和*我们需要控制复杂性*这样的话。同时我们提倡*重用*和*共享*代码，并使*易于扩展*。

当编写软件时，很容易以代码[比复杂](https://www.youtube.com/watch?v=ubaX1Smg6pY)更复杂而告终，代码试图做太多事情，很难处理。

每个人都告诉你保持简单。基本上我们所有人都同意这听起来是一件合理的事情。如果我们都知道我们的目标，为什么随着时间的推移，项目会变得如此混乱，如此难以开展？

也许我们需要更多的例子来说明努力寻求简单的解决方案意味着什么。

* * *

让我们构建一个简单的缓存。

缓存应该允许我们设置键值对并一次性检索值。

一个简单的实现可能是这样的:

```
const cache = () => {
  const store = {}

  const set = (key, value) => {
    store[key] = value
  }

  const remove = key => {
    const value = store[key]
    delete store[key]
    return value
  }

  return { set, remove }
}

// Let's use the cache

const simpleCache = cache()

simpleCache.set('a', 1)
simpleCache.set('b', 2)
simpleCache.set('b', 3)

console.log(simpleCache.remove('a')) // 1
console.log(simpleCache.remove('b')) // 3
console.log(simpleCache.remove('b')) // undefined 
```

Enter fullscreen mode Exit fullscreen mode

现在，随着项目的发展，您会得到新的需求，缓存也需要终止存储在缓存中的项目。应该指定一个*生存时间* ( *TTL* )，并在每次缓存项目到期时执行一个回调函数。你相应地修改代码:

```
const cache = (ttl, expirationHandler) => {
  const store = {}

  const set = (key, value) => {
    // Clear existing timer
    const record = store[key]
    if (record) {
      clearTimeout(record.timer)
    }
    // Set expiration timer
    const timer = setTimeout(() => {
      expirationHandler(key, store[key].value)
      delete store[key]
    }, ttl)
    // Store timer and value
    store[key] = { timer, value }
  }

  const remove = key => {
    // Find record
    const record = store[key]
    if (!record) {
      return undefined
    }
    delete store[key]
    const { timer, value } = record
    // Clear timer and store
    clearTimeout(timer)
    return value
  }

  return { set, remove }
}

const expirationHandler = (key, value) => {
  console.log(`expired ${key}: ${value}`) // expired b: 2
}
const expiringCache = cache(1000, expirationHandler)

expiringCache.set('a', 1)
expiringCache.set('b', 2)

console.log(expiringCache.remove('a')) // 1
console.log(expiringCache.remove('a')) // undefined
setTimeout(() => {
  console.log(expiringCache.remove('b')) // undefined
}, 1100) 
```

Enter fullscreen mode Exit fullscreen mode

一切都很好，然后，在检查您的代码时，您的同事注意到在另一个严格要求缓存中的项目永不过期的情况下使用了同一个缓存。

现在，您可以简单地在您的代码库中保留旧的和新的缓存实现，但是您更喜欢保持事情[干燥](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。

因此，您需要调整新的缓存来支持这两种用例:

```
const cache = (ttl, expirationHandler) => {
  const store = {}

  const set = (key, value) => {
    // If no TTL is specified, behave as before and return early
    if (!ttl) {
      store[key] = value
      return
    }
    // Clear existing timer
    const record = store[key]
    if (record) {
      clearTimeout(record.timer)
    }
    // Set expiration timer
    const timer = setTimeout(() => {
      expirationHandler(key, store[key].value)
      delete store[key]
    }, ttl)
    // Store timer and value
    store[key] = { timer, value }
  }

  const remove = key => {
    // Find record
    const record = store[key]
    if (!record) {
      return undefined
    }
    delete store[key]
    // If no TTL is specified, behave as before and return early
    if (!ttl) {
      return record
    }
    const { timer, value } = record
    // Clear timer and store
    clearTimeout(timer)
    return value
  }

  return { set, remove }
}

// Let's use the simple cache

const simpleCache = cache()

simpleCache.set('a', 1)
simpleCache.set('b', 2)
simpleCache.set('b', 3)

console.log(simpleCache.remove('a')) // 1
console.log(simpleCache.remove('b')) // 3
console.log(simpleCache.remove('b')) // undefined

// Let's use the expiring cache

const expirationHandler = (key, value) => {
  console.log(`expired ${key}: ${value}`) // expired b: 2
}
const expiringCache = cache(1000, expirationHandler)

expiringCache.set('a', 1)
expiringCache.set('b', 2)

console.log(expiringCache.remove('a')) // 1
console.log(expiringCache.remove('a')) // undefined
setTimeout(() => {
  console.log(expiringCache.remove('b')) // undefined
}, 1100) 
```

Enter fullscreen mode Exit fullscreen mode

真快。你所要做的就是添加两个 IF 语句。

这就是事情变得复杂的原因:简单缓存不再简单，而是与过期缓存纠缠在一起。简单的场景变得更难理解，更慢，并且有更多的机会引入错误。

**每次你通过*简单地*增加一个 *IF* 语句来实现一个特性，你就帮助它进一步发展—[这个大泥球](http://www.joeyoder.com/PDFs/mud.pdf)。**

怎样才能保持原来的缓存简单？

*复制代码，而不是把简单的事情复杂化。*

当您复制代码时，可以更容易地看到哪些部分可以共享和重用。

构建专门的工具，每个工具做一件事。并组合这些工具来构建其他工具。

这个在之前[已经说过很多次了。](https://en.wikipedia.org/wiki/Unix_philosophy)

我们如何在不使简单缓存复杂化的情况下创建过期缓存？

在我们的例子中，过期行为可以很容易地建立在初始缓存实现之上:

```
const cache = () => {
  const store = {}

  const set = (key, value) => {
    store[key] = value
  }

  const remove = key => {
    const value = store[key]
    delete store[key]
    return value
  }

  return { set, remove }
}

const expire = (cache, ttl, expirationHandler) => {
  const timers = {}

  const set = (key, value) => {
    // Store value
    cache.set(key, value)
    // Clear existing timer
    clearTimeout(timers[key])
    // Set expiration timer
    timers[key] = setTimeout(() => {
      const value = cache.remove(key)
      delete timers[key]
      expirationHandler(key, value)
    }, ttl)
  }

  const remove = key => {
    clearTimeout(timers[key])
    delete timers[key]
    return cache.remove(key)
  }

  return { set, remove }
}

// Let's use the simple cache

const simpleCache = cache()

simpleCache.set('a', 1)
simpleCache.set('b', 2)
simpleCache.set('b', 3)

console.log(simpleCache.remove('a')) // 1
console.log(simpleCache.remove('b')) // 3
console.log(simpleCache.remove('b')) // undefined

// Let's use the expiring cache

const expirationHandler = (key, value) => {
  console.log(`expired ${key}: ${value}`)
}
const expiringCache = expire(cache(), 1000, expirationHandler)

expiringCache.set('a', 1)
expiringCache.set('b', 2)

console.log(expiringCache.remove('a')) // 1
console.log(expiringCache.remove('a')) // undefined
setTimeout(() => {
  console.log(expiringCache.remove('b')) // undefined
}, 1100) 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，比如这个例子，工具组合得很好。在其他情况下，只有零件可以重复使用。将部分逻辑转移到单独的功能中，可以让您共享它们，将它们作为独立的工具来使用。

记住，每当在现有程序中引入新的条件时，都要小心。考虑哪些部分可以是独立的、可重用的工具。不要害怕抄袭代码。