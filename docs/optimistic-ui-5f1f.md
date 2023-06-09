# 乐观 UI

> 原文：<https://dev.to/stereobooster/optimistic-ui-5f1f>

这是系列文章的第三篇(假设你已经阅读了之前的文章)。这个帖子的代码是[这里是](https://github.com/stereobooster/react-fsm-example/tree/post-3)

1.  [还原为有限状态机](https://dev.to/stereobooster/pragmatic-types-how-to-turn-redux-to-finite-state-machine-with-the-help-of-types-5f08)
2.  [Redux 中的副作用](https://dev.to/stereobooster/turn-redux-into-finite-state-machine-vol-2-side-effects-6j0)
3.  [乐观 UI](https://dev.to/stereobooster/optimistic-ui-5f1f)
4.  [我创造了一个怪物](https://dev.to/stereobooster/i-created-a-monster-237o)

## 新要求:多步表单

需求是变化的——它们总是变化的。现在我们需要实现多步表单，在第一页上，用户输入数据，在第二页上选择一个项目，在第三页上要求凭据或提供付款细节。

显示项目列表的组件是无状态的，所以将其移动到下一页是很简单的，还需要从 redux 读取状态并将其传递给项目列表组件，最后但同样重要的是，我们需要将用户导航到下一页——我们可以这样做的副作用是:

```
case "SUBMIT_FRUIT_OK":
  const navigateToTheNextPage = Cmd.run(path => history.push(path), {
    args: ["/step-2"]
  });
  return loop(
    { ...state: "fruit_ok", ... },
    navigateToTheNextPage
  ); 
```

这里的问题是，用户按下按钮后，应用程序在做下一件事之前等待响应。从用户的角度来看，这是因为按钮没有响应，应用程序很慢或被破坏(特别是如果响应时间超过 200 毫秒)。

## 乐观 UI

解决这个问题的一个方法是，不要等待，就像我们实际上有了响应一样继续进行，转换到下一页，开始绘制页面(标题，可能是面包屑等)，一旦我们到达实际内容，就绘制一个微调器*，如果请求是待定的，或者内容本身。

* -有 200 毫秒的延迟，在我们承认花费太长时间之前，我们会给请求更多的时间(对用户来说“不明显”)。不知道我在哪里得到最初的 200 毫秒，[另一种意见应该是 100 毫秒](https://www.nngroup.com/articles/response-times-3-important-limits/)。

我们可以这样做:

```
case "SUBMIT_FRUIT":
  const navigateToTheNextPage = Cmd.run(path => history.push(path), {
    args: ["/step-2"]
  });
  return loop(
    { ...state: "fruit_loading", ... },
    Cmd.list([submitForm, navigateToTheNextPage])
  ); 
```

此外，我们需要调整错误情况:

```
case "SUBMIT_FRUIT_ERROR":
  const navigateToPreviousPage = Cmd.run(
    (expectedPath, path) => {
      // check that user hasn't navigated away
      if (history.location.pathname === expectedPath)
        history.replace(path);
    },
    {
      args: ["/step-2", "/"]
    }
  );
  return loop(
    { ...state: "fruit_error", ... },
    navigateToPreviousPage
  ); 
```

并从“正常”情况中移除一个效果:

```
case "SUBMIT_FRUIT_OK":
  return loop(
    { ...state: "fruit_ok", ... },
    Cmd.none
  ); 
```

## 预取

好吧，这样更好，但我们不能就此打住。如果我们一有有效数据就启动请求，例如，不要等待用户真正按下按钮，而是在用户提供有效数据时就发送请求，我们可以赢得几毫秒的时间

为了模拟现实生活中的网络，我创建了`setupProxy.js`，它提供随机的慢速响应(至少 100 毫秒长)。

### 何时触发预取

问题是如何捕捉用户完成输入和提交表单之间的时间。

#### 当用户鼠标接近表单末尾时

当用户鼠标接近表单末尾时运行预取。不适用于移动设备。

```
<div
  onMouseEnter={() => {
    this.validateAndPrefetch(this.state.values);
  }}
>
  <button type="submit">Search</button> </div> 
```

#### 月变化

在每个输入改变时运行预取。这种方法对于文本字段(inputs 和 textarea)来说是有问题的，因为它会产生大量的请求，并且因为浏览器可以同时处理有限数量的请求，所以它会降低最终的性能。它可能适用于离散输入，比如选择/组合框、复选框、日历等等。

```
handleChange = (e: SyntheticEvent<HTMLInputElement>) => {
  const { name, value, type } = e.target;
  const values = {
    ...this.state.values,
    [name]: value
  };
  this.setState({ values });
  if (isDiscrete(type)) this.validateAndPrefetch(values);
}; 
```

#### 在模糊上

运行模糊预取(unfocus)，这可以工作，除非表单中的所有字段都需要，最有可能的是，在模糊和表单提交之间没有停顿。

```
handleBlur = (e: SyntheticEvent<HTMLInputElement>) => {
  const { name, type } = e.target;
  const [errors] = validate(this.state.values);
  this.setState({
    touched: {
      ...this.state.touched,
      [name]: true
    },
    errors
  });
  if (!isDiscrete(type)) this.validateAndPrefetch(values);
}; 
```

### 如何缓存结果

#### 浏览器缓存

最简单的选择是依赖浏览器缓存，例如启动请求并“将其结果传送到`dev/null`”。

**优点**:使用服务器指定的缓存时间。

**缺点**:如果请求很慢，可能会发生这样的情况，在用户提交之前没有足够的时间进行预取。

```
const baseFruitRequest = (form: FruitForm) => {
  //...
  const query = `name=${form.name}&start=${form.start.toISOString()}`;
  return request(`${endpoint}?${query}`);
};

export const prefetch = async (form: FruitForm): Promise<void> => {
  try {
    await baseFruitRequest(form);
  } catch (e) {}
}; 
```

### 《JS 之地》中的 LRU

使用基于 LRU 的小型缓存来缓存获取请求。

**缺点**这是额外的缓存，与服务器指令(缓存头)相比可能配置错误。

**优点**适用于慢速请求，用户后续提交将从缓存中提取

LRU 用什么？

*   我创建了一个具有极简功能的 lru_map 的[分支(用一个双向链表和 map 实现)](https://github.com/stereobooster/lru_map)
*   还有更小的实现- [tmp-cache](https://github.com/lukeed/tmp-cache) (用数组和映射实现)

```
const cache = new Cache<string, Promise<FruitResponse>>({
  max: 5,
  maxAge: 60000
});

export const fruitRequest = (form: FruitForm): Promise<FruitResponse> => {
  const query = queryToString(form);
  let result = cache.get(query);
  if (!result) {
    result = baseFruitRequest(form);
    cache.set(query, result);
  }
  return result;
};

export const prefetch = async (form: FruitForm): Promise<void> => {
  try {
    await fruitRequest(form);
  } catch (e) {}
}; 
```

### 关于预取的更多想法

从我个人的实验中，我发现预取可以在 300 毫秒到几秒的任何时间内取得成功。

但是胜利是有代价的——我们打破了封装。Connector-component 只负责分派动作，逻辑封装在 Redux 中，但是现在逻辑也向连接器公开了。

### CORS

我们试图通过预取“欺骗”来赢得毫秒，但同时如果请求正在进行 CORS 检查，它可能会给最终体验增加几秒钟的暂停(取决于网络)。在您开始使用预取解决方案之前，最好先对此进行优化。

如果某个端点是公共的，不需要身份验证，例如，任何人都可以读取它，并且它不是特定于用户的，则删除 CORS。在这种情况下，CORS 什么都得不到。如果使用`fetch`删除所有自定义标题并使用经典的 GET/POST，否则浏览器将触发 CORS 预检检查。

如果你使用 CORS，确保它可以被浏览器缓存( [Access-Control-Max-Age](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Max-Age) )。

## 何时使用

> 权力越大，责任越大

|  | 乐观 UI | 预取 |
| --- | --- | --- |
| 有点激动 | 是 | 是 |
| 波斯特吉斯 | 可能 | 不 |

### 得意忘形

GET-ish -对服务器没有副作用的请求，只读取数据。通常这是 GET 请求(但不限于此)。

**乐观**如果我们确保降低客户端的错误率，例如，在发送用户输入之前，我们在客户端验证用户输入，那么服务器的错误响应率将会下降。

### 后置式

POST-ish -对服务器有副作用的请求，比如写入数据库或类似的。通常这是一个 POST、PUT 或 DELETE。

**乐观**如果我们确保降低客户端的错误率，例如，在发送用户输入之前，我们在客户端验证用户输入，那么服务器的错误响应率将会下降。

可能会有特定的情况阻止使用像 Braintree 托管字段这样的乐观用户界面，这是我们无法控制的，当我们再次导航并显示表单时，如果出现错误，也无法重新填充。

**预取**未经用户明确同意，我们不能向服务器发送“写入”请求。

例如，我们不能在用户点击“注册”之前提交注册表单，但同时我们可以提前验证所有字段(包括电子邮件、电话、密码)并使用乐观的 UI

> 照片由 Jonas Verstuyft 在 Unsplash 上拍摄

* * *

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。