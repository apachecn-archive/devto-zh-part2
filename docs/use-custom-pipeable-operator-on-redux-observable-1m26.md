# 在 redux-observable 上使用自定义管道运算符

> 原文：<https://dev.to/terrierscript/use-custom-pipeable-operator-on-redux-observable-1m26>

我们可以在 rxjs 上轻松创建自定义操作符

[https://github . com/react vex/rxjs/blob/master/doc/pipe able-operators . MD # build-your-own-operators-easily](https://github.com/ReactiveX/rxjs/blob/master/doc/pipeable-operators.md#build-your-own-operators-easily)

## 与 redux-observable 一起使用

比如我们有时候会用`tap`和`ignoreElements`进行调试。

```
export const pingEpic = (action$) =>
  action$.pipe(
    ofType("PING"),
    tap(console.log),
    ignoreElements()
  ) 
```

这个转换成这个。

```
const debug = () => <T>(source: Observable<T>) =>
  source.pipe(
    tap(console.log),
    ignoreElements()
  )

export const pingEpic = (action$) =>
  action$.pipe(
    ofType("PING"),
    debug()
  ) 
```

如果需要自定义抽头函数，可以使用参数

```
const debug = (tapFn = console.log) => <T>(source: Observable<T>) =>
  source.pipe(
    tap(tapFn),
    ignoreElements()
  )

export const pingEpic = (action$) =>
  action$.pipe(
    ofType("PING"),
    debug(item => console.log("This is DEBUG => ", item))
  ) 
```