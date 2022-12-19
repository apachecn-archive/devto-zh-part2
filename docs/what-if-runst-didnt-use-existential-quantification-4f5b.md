# 如果 runST 不用存在量化呢？

> 原文：<https://dev.to/chrismwendt/what-if-runst-didnt-use-existential-quantification-4f5b>

下面是 [`ST`](https://hackage.haskell.org/package/base-4.14.0.0/docs/Control-Monad-ST.html#t:ST) API:

```
data ST s a

runST :: (forall s. ST s a) -> a
newSTRef :: a -> ST s (STRef s a)
readSTRef :: STRef s a -> ST s a
writeSTRef :: STRef s a -> a -> ST s () 
```

Enter fullscreen mode Exit fullscreen mode

让我们放下`forall s`，看看会发生什么:

```
data ST s a

runST :: ST s a -> a
newSTRef :: a -> ST s (STRef s a)
readSTRef :: STRef s a -> ST s a
writeSTRef :: STRef s a -> a -> ST s () 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在`ST`可以通过在任何其他操作(例如`runST . newSTRef` ):
后调用`runST`来消除

```
newSTRef :: a -> STRef s a
readSTRef :: STRef s a -> a
writeSTRef :: STRef s a -> a -> () 
```

Enter fullscreen mode Exit fullscreen mode

在类型级别，`writeSTRef`看起来没有用，但是它仍然有向`STRef`写入值的副作用。

现在，评估的顺序很重要，而纯粹性已经过时了:

```
let ref = newSTRef "1"
    _ = writeSTRef ref "2"
in readSTRef ref -- Does this evaluate to "1" or "2"? 
```

Enter fullscreen mode Exit fullscreen mode

`forall s`是在`ST`单子中执行顺序读/写的关键。