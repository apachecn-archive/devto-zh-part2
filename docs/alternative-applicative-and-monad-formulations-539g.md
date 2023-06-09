# 替代应用和单子公式

> 原文：<https://dev.to/chrismwendt/alternative-applicative-and-monad-formulations-539g>

有一个关于 Applicative 和 Monad 的鲜为人知的替代类定义，我以前没有在文章中看到过，所以我想我应该分享一下:

```
class Functor f => Applicative f where
  pure :: f ()
  (**) :: f a -> f b -> f (a, b)

-- Left identity : pure ** v     ≅ v
-- Right identity: v    ** pure  ≅ v
-- Associativity : u ** (v ** w) ≅ (u ** v) ** w
-- (≅ means isomorphism)

class Applicative m => Monad m where
  join :: m (m a) -> m a

-- join . fmap join = join . join
-- join . fmap pure = join . pure = id 
```

Enter fullscreen mode Exit fullscreen mode

参见:

*   [https://stack overflow . com/questions/45829110/monad-laws-expressed-in-term-of-join-inst-of-bind/45829556](https://stackoverflow.com/questions/45829110/monad-laws-expressed-in-terms-of-join-instead-of-bind/45829556)
*   [https://wiki . haskell . org/Typeclassopedia # Alternative _ formulation](https://wiki.haskell.org/Typeclassopedia#Alternative_formulation)
*   [https://www . Reddit . com/r/haskell/comments/2 lompe/where _ do _ the _ applicative _ laws _ come _ from/clxfblk/](https://www.reddit.com/r/haskell/comments/2lompe/where_do_the_applicative_laws_come_from/clxfblk/)