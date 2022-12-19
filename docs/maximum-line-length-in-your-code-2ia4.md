# 代码中的最大行长度

> 原文：<https://dev.to/buinauskas/maximum-line-length-in-your-code-2ia4>

到目前为止，我一直试图坚持每行 80 个字符，这在普通的 JavaScript 中工作得很好，但是在采用 TypeScript 后，感觉对所有这些类型声明来说还不够，有时代码变得更难阅读，例如:

```
export const setCommunity: ActionCreator<SetCommunityAction> = (
  id: string
) => ({
  type: '@@community/SET_COMMUNITY',
  payload: {
    id
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

而如果 limit 是 100，这将变得更具可读性:

```
export const setCommunity: ActionCreator<SetCommunityAction> = (id: string) => ({
  type: '@@community/SET_COMMUNITY',
  payload: {
    id
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，这只是一个让我沮丧的例子。

你用的最大线长是多少，背后的原因是什么？