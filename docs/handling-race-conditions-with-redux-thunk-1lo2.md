# 用 redux-thunk 处理竞争条件

> 原文：<https://dev.to/remejuan/handling-race-conditions-with-redux-thunk-1lo2>

[![](img/764f384cea265cddd1984f4e2084d51e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vsnKbiof--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AvuRVtzCDBnbF_YINN6uKRg.png)

许多现代网络应用程序中包含的一个非常重要的功能是搜索，我们的用户总是需要能够找到一些东西，最近我不得不连接一个由 [*弹性搜索*](https://www.elastic.co) *，*驱动的“实时”搜索，当用户键入时，我们开始返回一个与您键入的内容潜在最相关的简短列表。

为了使结果更加相关和有用，我们自然地将初始 API 调用限制在输入第三个字符之后，并且[对异步调度](https://github.com/RemeJuan/debounce-redux-dispatch)去抖，所以所有这些工作都很顺利，并且很容易设置。

这就是潜在问题开始出现的地方，即使有去抖，我们仍然有多个调用可能会被触发，并且没有办法控制它们返回的顺序，以确保我们为用户提供正确的结果，我们真正关心的是我们进行的最后一个调用。

在花了一些时间筛选 googles 的结果，查看用户提出的各种其他想法后，出现了使用时间戳来过滤掉除最后一次呼叫之外的所有呼叫，并进而用期望的结果集更新状态的想法。

这个解决方案被证明工作得很好，并且相对容易设置，只需要对你已经构建的动作/思维和 reducer 做一些小的改变。

**动作**

您需要设置一个包含数据负载的开始操作，除了成功和错误之外，这将在 thunk 中用于设置 reducer 中搜索查询开始的时间。

```
export const SEARCH_POST_START = 'SEARCH_POST_START';

export const searchPostNoResults = data => ({
 type: SEARCH_POST_NO_RESULTS, data,
}); 
```

**Thunk**

至于 thunk，您需要做的就是在 API 调用开始之前更新它以包含这个新动作。

```
export function search(searchQuery) {
 return async (dispatch, getState) => {
   const timestamp = Date.now();
   const startData = { searchText: searchQuery, timestamp };
   dispatch(searchPostStart(startData));
   try {
     const url = `${URL}?searchQuery=${searchQuery}`;
     const options = {};
     const response = await fetch(url, options);
     const json = await response.json();
     const success = { searchResults: json, timestamp };
     return dispatch(searchPostSuccess(success));
   } catch (error) {
     const err = { error: true, errorMessage: error };
     return dispatch(searchPostError(err));
   }
 };
} 
```

在上面的代码片段中，您将看到在 thunk 的开始，我们分派了 start 操作，其中包括搜索查询和当前时间戳。

同样的逻辑也适用于成功和错误，它们都从这个调用实例中获得这个时间戳，以便以后在 reducer 中进行比较。

**减速器**

最后一部分发生在这里，首先是一个小助手进来，它对传入的时间戳进行简单的检查。

```
function actionIsValid(state, data) {
 return data.timestamp >= state.timestamp;
} 
```

如您所见，它所做的只是确保传入的时间戳不会比当前状态中存储的时间戳旧。

```
export const initialState = {
  error: false,
  errorMessage: undefined,
  isSearching: false,
  searchResults: [],
  searchText: '',
  timestamp: undefined,
};

function actionIsValid(state, data) {
  return data.timestamp >= state.timestamp;
}

export default function searchReducer(state = initialState, action){
 const { type, data } = action;

 switch (type) {
   case SEARCH_POST_SUCCESS: {
     if (actionIsValid(state, data)) {
       return {
         ...state,
         ...data,
         isSearching: false,
       };
     }
     return state;
   }

  case SEARCH_POST_START:
    return {
      ...state,
      ...data,
      isSearching: true,
    };

  default:
    return state;
  }
} 
```

为了简洁起见，我只包括了开始和成功，您将看到我只是从 thunk 发送的操作中传播数据对象，它是用与 reducer 中的错误、搜索结果、时间戳相匹配的键设置的，并且基于 is ationIsValid 帮助器，您可以确定状态是否需要更新。

对于一个完整的、更突出的例子，这里有一个完整的[要点](https://gist.github.com/RemeJuan/540800f3d600e95c18f6dad183f4382e)。

感谢阅读，我希望你觉得这是有用的。