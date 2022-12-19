# 一种类型安全的方法来减少 TypeScript 中的存储

> 原文：<https://dev.to/resir014/a-type-safe-approach-to-redux-stores-in-typescript--5ajm>

# 一次更新(2018-07-06)

该指南现已过时。我终于花时间根据我收到的反馈更新了这个指南，用 React、Redux 和 TypeScript 的最新版本更新了所有内容，并引入了一些新的技巧。

[点击这里](https://dev.to/resir014/redux-4--typescript-29-a-type-safe-approach-2lf4)阅读。

* * *

最近用 TypeScript 写了很多代码。除此之外，我还写了很多 React 代码和 Redux。这个轻量级的状态管理库为许多 React 开发人员节省了时间。它的类型脚本支持也很出色，有一个积极维护的[类型声明文件](https://github.com/reactjs/redux/blob/master/index.d.ts)。

互联网上有很多关于构建 Redux 商店代码库的指南。我混合搭配了很多这样的指南，想出了一个易于打字的结构，并且完美地符合我的个人工作流程。

在我决定使用这个方法之前，我已经做了很多实验，并且不可否认这仍然是一个正在进行的实验，所以我愿意接受建议。我决定写这篇文章，部分是作为个人指南，所以这里提到的大部分东西都是基于个人偏好，但我也希望其他阅读这篇文章的人能从中有所收获。

*注:本文有效期为`redux@^3.7.2`。当它发布时，我会考虑更新它来支持`redux@^4.0.0`！*

## 目录结构

我跟你说实话，对我来说，开始使用 React + Redux 最困难的一步是弄清楚如何构建你的项目。实际上没有什么方法可以做到这一点，但正确处理这件事仍然很重要，这样就不会在以后的道路上造成更多的干扰。我通常是这样做的。

### 使用专用的`store/`目录

许多指南/项目在根目录`actions`和`reducers`下分别构建它们的存储，例如

```
.
|-- actions
|   |-- chat.ts
|   |-- index.ts
|   `-- layout.ts
|-- components
|   |-- Footer.tsx
|   `-- Header.tsx
|-- containers
|   `-- ChatWindow.tsx
|-- reducers
|   |-- chat.ts
|   |-- index.ts
|   `-- layout.ts
|-- ...
|-- index.tsx
`-- types.d.ts 
```

Enter fullscreen mode Exit fullscreen mode

但是，我个人觉得这很分散注意力。您最终会将共享相同功能的代码分散到整个项目中。我自然希望所有处理 Redux 存储的代码都在同一个地方。

所以我决定为我所有的 Redux 动作/reducer 专用一个`store/`目录。这个方法大部分是借鉴了 Wix 的 Tal Kol 做的[这个指南](https://hackernoon.com/redux-step-by-step-a-simple-and-robust-workflow-for-real-life-apps-1fdf7df46092)，明显是做了一些调整。

```
.
|-- components
|   |-- Footer.tsx
|   `-- Header.tsx
|-- containers
|   `-- ChatWindow.tsx
|-- store
|   |-- chat
|   |   |-- actions.ts
|   |   |-- reducer.ts
|   |   `-- types.ts
|   ├── layout
|   |   |-- actions.ts
|   |   |-- reducer.ts
|   |   `-- types.ts
|   `-- index.ts
|-- ...
|-- index.tsx
`-- types.d.ts 
```

Enter fullscreen mode Exit fullscreen mode

### 按上下文对门店进行分组

作为上述指南的扩展，状态树应该由上下文构成**。** 

```
.
`- store
    |-- chat // Handles chat functionalities, e.g. fetching messages
    |   |-- actions.ts
    |   |-- reducer.ts
    |   `-- types.ts
    ├── layout // Handles layout settings, e.g. theme, small/large text, etc.
    |   |-- actions.ts
    |   |-- reducer.ts
    |   `-- types.ts
    `-- index.ts 
```

Enter fullscreen mode Exit fullscreen mode

### 结合减速器内部`store/index.ts`

在`store/`目录的根目录下包含一个`index.ts`文件。我们将使用它来声明顶级应用程序状态对象类型，并导出我们组合的 reducers。

```
// ./src/store/index.ts

import { combineReducers, Dispatch, Reducer } from 'redux';
import { routerReducer } from 'react-router-redux';

// Import your state types and reducers here.
import { ChatState } from 'store/chat/types';
import { LayoutState } from 'store/layout/types';
import chatReducer from 'store/chat/reducer';
import layoutReducer from 'store/layout/reducer';

// The top-level state object
export interface ApplicationState {
  chat: ChatState;
  layout: LayoutState
}

// Whenever an action is dispatched, Redux will update each top-level application state property
// using the reducer with the matching name. It's important that the names match exactly, and that
// the reducer acts on the corresponding ApplicationState property type.
export const reducers: Reducer<ApplicationState> = combineReducers<ApplicationState>({
  router: routerReducer,
  chat: chatReducer,
  layout: layoutReducer,
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 分离表示层和容器组件

这与其说是 Redux 的事情，不如说是 React 的事情，但是不管怎样，让我们来看一下。

Dan Abramov 最初创造了“表示”和“容器”组件的术语。我使用这个组件结构的方式大致相同。我使用容器组件连接到我的 Redux store，表示性组件处理大部分样式工作。

```
.
├── components
|   |-- Footer.tsx
|   `-- Header.tsx
├── containers
|   |-- AddMessage.tsx
|   `-- ChatWindow.tsx
├── ...
`-- index.tsx 
```

Enter fullscreen mode Exit fullscreen mode

## 打字动作

现在我们已经搭建好了所有的东西，是时候以最安全的方式建立我们的商店了！

### 宣告各减速器的状态

首先要做的是键入每个减速器的状态。打开`chat`商店的`types.ts`文件，添加我们的状态对象。

```
// ./src/store/chat/types.ts

// Our chat-level state object
export interface ChatState {
  username: string;
  connectedUsers: UserInfo[];
  messages: MessagePayload[];
}

// Feel free to include more types for good measure.

export interface UserInfo {
  name: string;
  id: number;
}

export interface TemplateItem {
  item: string;
  text: string;
}

export interface MessagePayload {
  timestamp: Date;
  user: string;
  message: {
    type: 'text' | 'template';
    content?: string;
    items?: TemplateItem[];
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将动作类型声明为接口

为了正确地对我们的动作创建者进行分类，将它们声明为`interface` s。我们还将为它们中的每一个从基本`Action`接口进行扩展。

```
// ./src/store/chat/types.ts

import { Action } from 'redux';

// Declare our action types using our interface. For a better debugging experience,
// I use the `@@context/ACTION_TYPE` convention for naming action types.

export interface UsersListUpdatedAction extends Action {
  type: '@@chat/USERS_LIST_UPDATED';
  payload: {
    users: UserInfo[];
  };
}

export interface MessageReceivedAction extends Action {
  type: '@@chat/MESSAGE_RECEIVED';
  payload: {
    timestamp: Date;
    user: string;
    message: MessagePayload;
  };
}

// Down here, we'll create a discriminated union type of all actions which will be used for our reducer.
export type ChatActions = UsersListUpdatedAction | MessageReceivedAction; 
```

Enter fullscreen mode Exit fullscreen mode

### `ActionCreator`是你的朋友

是时候写我们的动作创作者了！首先我们将从 Redux 导入`ActionCreator`。我们将把它和我们之前创建的动作类型一起使用，作为一个通用的。

```
// ./src/store/chat/actions.ts

import { ActionCreator } from 'redux';
import {
  UsersListUpdatedAction,
  UserInfo,
  MessageReceivedAction,
  MessagePayload,
} from './types';

// Type these action creators with `: ActionCreator<ActionTypeYouWantToPass>`.
// Remember, you can also pass parameters into an action creator. Make sure to
// type them properly.

export const updateUsersList: ActionCreator<UsersListUpdatedAction> = (users: UserInfo[]) => ({
  type: '@@chat/USERS_LIST_UPDATED',
  payload: {
    users,
  },
});

export const messageReceived: ActionCreator<MessageReceivedAction> = (
  user: string,
  message: MessagePayload,
) => ({
  type: '@@chat/MESSAGE_RECEIVED',
  payload: {
    timestamp: new Date(),
    user,
    message,
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 打字减速器

```
// ./src/store/chat/reducer.ts

import { Reducer } from 'redux';
import { ChatState, ChatActions } from './types';

// Type-safe initialState!
export const initialState: ChatState = {
  username: '',
  connectedUsers: [],
  messages: [],
};

// Unfortunately, typing of the `action` parameter seems to be broken at the moment.
// This should be fixed in Redux 4.x, but for now, just augment your types.

const reducer: Reducer<ChatState> = (state: ChatState = initialState, action) => {
  // We'll augment the action type on the switch case to make sure we have
  // all the cases handled.
  switch ((action as ChatActions).type) {
    case '@@chat/SET_USERNAME':
      return { ...state, username: action.payload.username };
    case '@@chat/USERS_LIST_UPDATED':
      return { ...state, connectedUsers: action.payload.users };
    case '@@chat/MESSAGE_RECEIVED':
      return { ...state, messages: [...state.messages, action.payload] };
    default:
      return state;
  }
};

export default reducer; 
```

Enter fullscreen mode Exit fullscreen mode

## 店铺配置

初始化 Redux 存储应该在一个`configureStore()`函数中完成。在这个函数中，我们引导所需的中间件，并将它们与我们的 reducers 结合起来。

```
// ./stc/configureStore.ts

import { createStore, applyMiddleware, Store } from 'redux';

// react-router has its own Redux middleware, so we'll use this
import { routerMiddleware } from 'react-router-redux';
// We'll be using Redux Devtools. We can use the `composeWithDevTools()`
// directive so we can pass our middleware along with it
import { composeWithDevTools } from 'redux-devtools-extension';
// If you use react-router, don't forget to pass in your history type.
import { History } from 'history';

// Import the state interface and our combined reducers.
import { ApplicationState, reducers } from './store';

export default function configureStore(
  history: History,
  initialState: ApplicationState,
): Store<ApplicationState> {
  // create the composing function for our middlewares
  const composeEnhancers = composeWithDevTools({});

  // We'll create our store with the combined reducers and the initial Redux state that
  // we'll be passing from our entry point.
  return createStore<ApplicationState>(
    reducers,
    initialState,
    composeEnhancers(applyMiddleware(
      routerMiddleware(history),
    )),
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 猎艳有什么反应

现在让我们看看这整个结构是如何反应的。

### 将 React 组件连接到 Redux

我们现在将 React 组件连接到 Redux。因为我们正在映射我们的状态，所以我们也需要将我们映射的商店的状态对象组合到我们的组件道具中。

```
// ./src/containers/ChatWindow.tsx

import * as React from 'react';
import { connect, Dispatch } from 'react-redux';
import { ChatState } from 'store/chat/types';

// Standard component props
interface ChatWindowProps {
  // write your props here
}

// Create an intersection type of the component props and our state.
type AllProps = ChatWindowProps & ChatState;

// You can now safely use the mapped state as our component props!
const ChatWindow: React.SFC<AllProps> = ({ username, messages }) => (
  <Container>
    <div className={styles.root}>
      <ChatHeader username={username} />
      <ChatMessages>
        {messages && messages.map(message => (
          <ChatMessageItem
            key={`[${message.timestamp.toISOString()}]${message.user}`}
            payload={message}
            isCurrentUser={username === message.user}
          />
        ))}
      </ChatMessages>
      <div className={styles.chatNewMessage}><AddMessage /></div>
    </div>
  </Container>
); 
```

Enter fullscreen mode Exit fullscreen mode

`react-redux` `connect()`函数将我们的 React 组件连接到 redux 存储。请注意，在这种情况下，我们只有**的**将使用`mapStateToProps()`呼叫。

```
// It's usually good practice to only include one context at a time in a connected component.
// Although if necessary, you can always include multiple contexts. Just make sure to
// separate them from each other to prevent prop conflicts.
const mapStateToProps = (state: ApplicationState) => state.chat;

// Now let's connect our component!
export default connect(mapStateToProps)(ChatWindow); 
```

Enter fullscreen mode Exit fullscreen mode

### 调度动作

我知道你可能在想什么。*你没叫`mapDispatchToProps()`？你到底是怎么调度你的行动的？*

很简单，当我们在一个组件上调用`connect()`时，它也会传递`dispatch` prop，你可以用它来调用动作创建者！

我们可以为此创建一个基本接口。我一般把这个放在`./src/store/index.ts`里面。

```
// Additional props for connected React components. This prop is passed by default with `connect()`
export interface ConnectedReduxProps<S> {
  // Correct types for the `dispatch` prop passed by `react-redux`.
  // Additional type information is given through generics.
  dispatch: Dispatch<S>;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们回到之前做的`ChatWindowProps`接口，让它扩展我们刚刚做的接口:

```
import { connect, Dispatch } from 'react-redux';
import { ConnectedReduxProps } from 'store';
import { ChatState } from 'store/chat/types';

// Extend the interface.
interface ChatWindowProps extends ConnectedReduxProps<ChatState> {} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果你紧紧跟随这些指南，你应该有一个足够强大的打字 Redux 商店！当然，这只是众多方法中的一种，所以不要害怕用这些指南做进一步的实验。当然，这只是个人偏好，您的里程数可能会有所不同。