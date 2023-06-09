# Redux +原子设计中如何将组件视为类型安全

> 原文：<https://dev.to/konojunya/how-to-treat-component-as-typesafe-in-redux--atomic-design-1o4h>

> 它把我在日本 Qiita 技术分享服务上的一篇文章改写成了英文。
> 日本的文章如下。
> 
> [用我想到的 Redux + Atomic Design 在 TypeSafe 中处理组件的方法](https://qiita.com/konojunya/items/5042da2257bb1a85fcac)

这是首诗。让我们注意浏览:)

首先道歉，因为你可能认为你不理解原子设计摆在首位。
只是作为一个常见的识别，我也写在文档里，所以我会用这个方法来操作。

# 目录结构

```
- src/
    - actions/
        - hoge.ts
    - api/
        - client.ts
    - components/
    - model/
        - type.ts
    - store/
        - index.ts
        - reducer-type.ts
        - sagas/
        - reducers/ 
```

`ActionCreator`在`actions`中有描述。
API 环境(使用 axios 的通信)在`client.ts`中总结。

下属是模仿原子设计的。

`model/type.ts`通过用名称空间`Payload` `Response` `Domain`将它们分开来管理。
例如，如果你使用 API 用邮箱和密码登录`Payload.Signin`

喜欢

```
interface Signin {
  email: string;
  password: string;
} 
```

这是稍后用作`Action`的有效载荷的类型。

在`Response.Signin`中总结了该 API 的响应。

示例

```
interface Signin {
  name: string;
  icon: string;
  token: string;
} 
```

`store`除了`index.ts`做`combineReducer`等，还有`reducers`定义减速器，`sagas`写传奇，等等。

这是`reducer-type.ts`帮助我在这个项目中创建的形状。

# 什么是减速器-类型. ts 的内容

`reducer-type.ts`有以下内容。

```
export interface ReduxAPIError {
  statusCode: string;
  message?: string;
}

interface ReduxAPIStruct<T> {
  isLoading: boolean;
  status: "success" | "failure";
  data: T;
  error: ReduxAPIError;
}

export const defaultSet = <T>(defaultValue?): ReduxAPIStruct<T> => ({
  isLoading: false,
  status: null,
  data: defaultValue || null,
  error: errorDefault()
});

export const errorDefault = (): ReduxAPIError => ({
  statusCode: null,
  message: ""
});

export default ReduxAPIStruct; 
```

在应用程序内部，我将这个有用的工具称为`ReduxAPIStruct`。

重要的是下面的界面。

```
interface ReduxAPIStruct<T> {
  isLoading: boolean;
  status: "success" | "failure";
  data: T;
  error: ReduxAPIError;
} 
```

每个都描述了与应用程序通信时的运动。

`isLoading`由 boolean 管理，处理是否加载。创建
`status`是为了便于以后回退到错误组件。
`data`是实际从 API 中获取时的数据体。这里用`T`定义为通用。以后使用这种型号时，直接将`model/type.ts`的`Response`型号通过即可。
`error`有一种类型叫`ReduxAPIError`。

```
export interface ReduxAPIError {
  statusCode: string;
  message?: string;
} 
```

`ReduxAPIError`有`statusCode`和`message`。
`statusCode`设置访问该 API 时响应的状态码。
消息有一种类型，可将其插入服务器的响应正文中，然后插入 saga 中。

# 一个具有 ReduxAPIStruct 的世界

首先作为什么模仿原子设计的重要一点，我们把`pages`和`template`的职责划分的很清楚。
例如，只将 redux 连接到`paegs`类的组件。

我们将`pages`定义为**，一个包含数据并链接到 URL** 的页面。
另一方面，`template`被创建为**一个基于数据**的 UI。

通过分离这个明确的义务，`pages`组件看起来像这样。

例如，我想创建一个接收以下数据的文章列表页面。

```
{  "items":  [  {  "title":  "タイトル1",  "body":  "本文 1"  },  {  "title":  "タイトル2",  "body":  "本文 2"  },  {  "title":  "タイトル3",  "body":  "本文 3"  }  ]  } 
```

假设这个 API 的端点是`[GET] /items`，axios API 客户端是如下实现。

```
import axios from "axios";

class Client {
  public getItems() {
    return axios.get("/items");
  }
} 
```

客户端实现它来返回`Promise`。

传奇代码会结束，但假设结果在`article reducer`的键`items`里。

```
import * as React from "react";
import { connect } from "react-redux";
import { withRouter } from "react-router";
import { requestGetItems } from "~/actions/article";
import ErrorHandler from "~/ErrorHandler";

// templates
import ItemsPageTemplate from "~/components/templates/ItemsPage";
import LoadingTemplate from "~/components/templates/Loading";

// type
import { Response } from "~/model/type";
import ReduxAPIStruct from "~/store/reducer-type";

interface Props {
  items: ReduxAPIStruct<Response.Article.Items>;
}

class ItemPage extends React.Component {
  public render() {
    const { items } = this.props;
    if(items.status === "failure") {
      return <ErrorHandler errorStatus={items.error.statusCode}/>;
    }

    return items.isLoading ? <LoadingTemplate /> : <ItemsPageTemplate items={items.data} />;
  }
}

export default wituRouter(connect(
  state => ({ items: state.articleReducer.items })
)(ItemPage) as any); 
```

如果使用此功能采集数据，`LoadingTemplate`将会失败，数据提取将会失败。
如果未能`ErrorHandler`成功，将挂载`ItemsPage`。

不管你写哪一页或者什么都不想，你都可以管理这三种状态。

由于 ReduxAPIStruct，您可以很容易地了解数据是否在获取中，并且通过查看`status`也可以了解获取失败。

当通过将这种状态管理切割到另一种状态来编写 saga 时，可以管理状态而不考虑组件。

# 后记

我用谷歌翻译来翻译我的英语。如有错误为英文描述，请评论指出:(

Twitter:[@ konojunya](https://twitter.com/konojunya)
GitHub:[konojunya](https://github.com/konojunya)