# ES2018。异步迭代的实际简单用法:用 20 行代码从 REST APIs 获取分页数据

> 原文：<https://dev.to/exacs/es2018-real-life-simple-usage-of-async-iteration-get-paginated-data-from-rest-apis-3i2e>

下一个 JavaScript 标准 ES2018 已经推出，它有一个新的重要特性:异步迭代。这是一个非常有用的功能，我想和你分享一个超级简单的例子，告诉你我们如何在现实生活中使用它。

> 在这篇文章中，我不打算解释什么是异步迭代器或迭代器。你可以在[这里](https://dev.to/nestedsoftware/the-iterators-are-coming-symboliterator-and-symbolasynciterator-in-javascript-hj)或者[这里](http://2ality.com/2016/10/asynchronous-iteration.html)得到那些解释

**问题**。我们希望从返回分页的 API 中获取数据，并对每个页面进行处理。例如，我们想获取 Github repo 的所有提交，并用这些数据做一些事情。

我们希望**将“获取提交”和“做事情”的逻辑**分开，所以我们将使用两个函数。在真实生活场景中，`fetchCommits`可能在不同的模块中，而“做事情”部分将以某种方式调用【T1:

```
// Imagine that this function is in a different module...
function fetchCommits(repo) {}

function doStuff() {
  const commits = fetchCommits('facebook/react')
  // do something with `commits`
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，Github API 将返回分页的提交(像大多数 REST APIs 一样)，所以我们将“成批”获取提交。我们希望在`fetchCommits`中以某种方式实现这种“分页”逻辑。

然而，我们不想在`fetchCommits`中一起返回所有的提交，我们想在它们到来时为每个页面做一些逻辑，并在“做东西”部分实现这样的逻辑。

## 无异步迭代的解决方案

为此，我们被迫使用回调:

```
// Here we "do stuff"
fetchCommits('facebook/react', commits => {
  // do something with `commits`
} 
```

Enter fullscreen mode Exit fullscreen mode

我们能用承诺吗？。嗯，不是以这种方式，因为我们将只得到一页或整个东西:

```
function doStuff() {
  fetchCommits('facebook/react').then(commits => {
    // do something
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们能使用同步 T2 发电机吗？良好的...我们可以在生成器中返回一个`Promise`,并在它之外解析这个承诺。

```
// fetchCommits is a generator
for (let commitsPromise of fetchCommits('facebook/react')) {
  const commits = await commitsPromise
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是一个干净的解决方案，但是`fetchCommits`生成器的实现如何呢？

```
function* fetchCommits(repo) {
  const lastPage = 30 // Must be a known value
  const url = `https://api.github.com/${repo}/commits?per_page=10`

  let currentPage = 1
  while (currentPage <= lastPage) {
    // `fetch` returns a Promise. The generator is just yielding that one.
    yield fetch(url + '&page=' + currentPage)
    currentPage++
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不错的解决方案，但是我们有一个大问题:必须预先知道`lastPage`值。这通常是不可能的，因为当我们发出第一个请求时，这个值会出现在头中。

如果我们仍然想使用生成器，那么我们可以使用一个异步函数来获取该值并返回一个同步生成器...

```
async function fetchCommits (repo) {
  const url = `https://api.github.com/${repo}/commits?per_page=10`
  const response = await fetch(url)

  // Here we are calculating the last page...
  const last = parseLinkHeader(response.headers.link).last.url
  const lastPage = parseInt(
    last.split('?')[1].split('&').filter(q => q.indexOf('page') === 0)[0].split('=')[1]
  )

  // And this is the actual generator
  return function* () {
    let currentPage = 1
    while (currentPage <= lastPage) {
      // And this looks non dangerous but we are hard coding URLs!!
      yield fetch(url + '&page=' + currentPage)
      currentPage++
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个好的解决方案，因为我们实际上是硬编码“下一个”URL。

此外，这种用法可能会有点混乱...

```
async function doStuff() {
  // Calling a function to get...
  const getIterator = await fetchCommits('facebook/react')

  // ... a function that returns an iterator???
  for (const commitsPromise of getIterator()) {
    const value = await commitsPromise
    // Do stuff...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最理想的情况是，我们希望在每个请求之后获得“下一个”URL，这涉及到在生成器中放置异步逻辑，但是在产生的值之外

 *## 异步发电机(`async function*`)和`for await`回路

现在，异步生成器和异步迭代允许我们遍历结构，在这些结构中，*产生的值*之外的所有逻辑也是异步计算的。这意味着，对于每个 API 调用，我们可以根据标题猜测“下一个 URL ”,并检查我们是否到达了结尾。

事实上，这可能是一个真正的实现:

*(本例在节点> = 10)*

```
const rp = require('request-promise')
const parseLinkHeader = require('parse-link-header')

async function* fetchCommits (repo) {
  let url = `https://api.github.com/${repo}/commits?per_page=10`

  while (url) {
    const response = await request(url, {
      headers: {'User-Agent': 'example.com'},
      json: true,
      resolveWithFullResponse: true
    })

    // We obtain the "next" url looking at the "link" header
    // And we need an async generator because the header is part of the response.
    const linkHeader = parseLinkHeader(response.headers.link)

    // if the "link header" is not present or doesn't have the "next" value,
    // "url" will be undefined and the loop will finish
    url = linkHeader && linkHeader.next && linkHeader.next.url
    yield response.body
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

调用者函数的逻辑也变得非常简单:

```
async function start () {
  let total = 0
  const iterator = fetchCommits('facebook/react')

  // Here is the "for-await-of"
  for await (const commits of iterator) {
    // Do stuff with "commits" like printing the "total"
    total += commits.length
    console.log(total)

    // Or maybe throwing errors
    if (total > 100) {
      throw new Error('Manual Stop!')
    }
  }
  console.log('End')
}
start() 
```

Enter fullscreen mode Exit fullscreen mode

关于如何使用**异步发电机**，你还有其他的例子吗？*