# 打字稿中的单子

> 原文：<https://dev.to/e_ntyo/-monad-in-typescript-dmg>

## TL；博士；医生

*   平时写 TypeScript 的宅男读了很厉害的 h 书

    *   Haskell 有很多方便的功能和想法，我们发现其中的一部分也可以用 TypeScript 这样的编程语言来表现
    *   想用 TypeScript 做像 Haskell 那样的事情的话，所谓的蒙娜丽莎库很方便，其中[fp-ts](https://github.com/gcanti/fp-ts) 看起来很好

以下用代码写下关于 fp-ts 的具体解说等。

## 前言

在以前的工作中，在 TypeScript 的代码中写了`type Either<Left, Right> = ...`这样的 type alias 的工程师向我推荐了 Haskell，并试着读了[厉害的 h 本](https://www.amazon.co.jp/%E3%81%99%E3%81%94%E3%81%84Haskell%E3%81%9F%E3%81%AE%E3%81%97%E3%81%8F%E5%AD%A6%E3%81%BC%E3%81%86%EF%BC%81-%EF%BC%AD%EF%BD%89%EF%BD%92%EF%BD%81%EF%BD%8E%EF%BC%AC%EF%BD%89%EF%BD%90%EF%BD%8F%EF%BD%96%EF%BD%81%EF%BD%83%EF%BD%81-ebook/dp/B009RO80XY)。

Haskell 很厉害。 拥有两个非常强大的力量。 是生硬的静态定型和现代的函数型编程技术。 美观，[型安全](https://postd.cc/what-is-type-safety/)，能够写出没有浪费的处理。

## 静态型，什么都表示

刚才的`Either`也是类型类(与 Java 等一般类型不同，总的来说是接口之类的东西)之一，可以将失败类和成功类这两个语境定义为一个类型。 如果是 HTTP 请求的话，就可以将错误信息和响应分别表现为类型。

这样方便的类型类还有很多，似乎是除了 Haskell 之外，Scala 等也存在的概念。 那个名字叫莫纳德。 想详细了解以莫纳德为首的“表达语境的类型类别”及其处理方法的人可以通过[Monad](https://qiita.com/lagenorhynque/items/e68ada9ee2d63539eb45) 、[MTL style](https://qiita.com/ruicc/items/7512c990a1835bba444a) 等进行调查，但追求严密性的话会变成高级数学话题

> 图中的函子、应用、单子-[http://adit . io/posts/2013-04-17-函子、_ 应用、_ And _ Monads _ In _ Pictures . html](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)
> [![http://adit.io/imgs/functors/recap.png](../Images/7dab7de3b4e86de60dde3a6bc64825b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---QGXi-th--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://adit.io/imgs/functors/recap.png)

图像中的箱子表示上下文。 该图像表示在 Haskell 中按箱子的种类使用什么样的运算符(图像中的`<$>, <*>, >>=`)才能在不失去上下文( =箱子)的情况下加工数值。 因为在这篇文章中没有出现这些符号本身，所以希望你不要在意，只有以下几点希望你能记住在脑子的某个地方。

*   `Functor`、`Applicative`是箱子的种类，是`Monad`的亲戚。
*   箱子可以用模型来表现
*   由于装在箱子里的值不能直接传递给取无箱子值的函数，所以需要使用特殊的函数运算符

## 函数型编程

Haskell 是所谓的纯函数式编程语言，有副作用的处理与无副作用的处理完全分离。 在编写无副作用处理的地方，在将值加工成目标形式时，可以完全从模型的转换和代入操作中解放出来，可以集中在变换处理本身上。

## 用 TypeScript 做类似 Haskell，Scala 的事

虽然是众所周知的事实，但是用 JavaScript 也可以进行一定程度的函数型编程。 [奥莱理书也出版了。](https://www.amazon.co.jp/JavaScript%E3%81%A7%E5%AD%A6%E3%81%B6%E9%96%A2%E6%95%B0%E5%9E%8B%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0-Michael-Fogus/dp/4873116600) (内容已经过时)。 具体地说，可以使用高阶函数连接无副作用的处理，或者将函数表象化并传递给其他函数。

而且，如果使用 TypeScript 或 flow 之类的话，也可以使用静态型。 那就算是 JavaScript 也能像 Haskell，Scala 一样吗？ 到了这样的想法。 在那里方便的是莫纳多库。 莫那德库为我们提供了表达上述上下文的模那德和处理该模型的函数等。

那么，关于 TypeScript 中可以利用的莫那德库，[这边](https://blog.mmmcorp.co.jp/blog/2016/10/10/compare-maybe-monad/)的报道很详细。 但是，在这篇文章公开后，明星数、规模感都在 TypeScript 的莫那德库里出现了最大的[fp-ts](https://github.com/gcanti/fp-ts) 。 在这次的报道中，我想做这个的介绍。

## fp-ts

fp-ts 似乎是受现在话题的[PureScript](http://www.purescript.org/) ，还有[fantasy-land](https://github.com/fantasyland/fantasy-land) 和 Scala 影响的蒙娜丽莎库。 其他有名的莫那德库在 300 Star 左右，与此相对，似乎也有规模感，Star 数量是其两倍以上。

能做什么呢，总结在自述的[Examples](https://github.com/gcanti/fp-ts#examples) 中。 Free Monad、MTL Style 等 Haskell 是主要的(？ )从技巧上来看，`Option`、`Either`、`Reader`、`Writer`、`State`、`List`等固定模态，甚至在 Haskell 中经常被称为可以代替`Writer`，便于调试的`Trace` 甚至还有可以使用依存型安全地记述有限自动机的`IxIO`([好像是使用了 2016 年发表的论文的安装](https://www.idris-lang.org/drafts/sms.pdf)，但是不太清楚…)。

试着以莫纳德为中心，dig 一下 fp-ts 的几个功能。

### 最佳模式

option mond 用于表现可能没有值的上下文，例如在从列表中检索目标要素时等有用。 作为`type Option<A> = None | Some<A>`，对于某个类型`A`，返回用`Some`包装了`none`或`A`的结果。 使用`null`而不是`none`，使用`some`包装值而不是值本身，是为了不破坏`Option`的语境。

是从 Scala 带来的摩纳德，在 Haskell 中应该相当于 Maybe。 因为不知道 Scala，所以标量...

```
import { Option, some, none, option } from 'fp-ts/lib/Option'
import { array } from 'fp-ts/lib/Array'
import { sequence } from 'fp-ts/lib/Traversable'

export function getAllSomesOrNone<A>(xs: Array<Option<A>>): Option<Array<A>> {
  return sequence(option, array)(xs)
}

console.log(getAllSomesOrNone([some(1), some(2), some(3)])) // => some([1, 2, 3])
console.log(getAllSomesOrNone([some(1), none, some(3)])) // => none 
```

Enter fullscreen mode Exit fullscreen mode

此代码放在信息库的[exercise (以一问一答的形式学习 fp-ts )](https://github.com/gcanti/fp-ts/tree/master/exercises)中。 在此代码中，定义了接收`Array<Option<A>>`的数组，如果数组中不包含`none`，则用`some`包装各要素的数组，如果包含，则返回`none`的函数`getAllsomesOrNone`。

在`getAllSomesOrNone`中在意的是 fp-ts 的`sequence`的安装吧。

```
// Traversable.ts より一部を抜粋

// applicativeはモナドcにくるまった関数 a -> bを、c[a] -> c[b]な関数に変換する
export function traverse<F, T>(
  F: Applicative<F>,
  T: Traversable<T>
): <A, B>(ta: HKT<T, A>, f: (a: A) => HKT<F, B>) => HKT<F, HKT<T, B>> {
  return T.traverse(F)
}

export interface Traversable<T> extends Functor<T>, Foldable<T> {
  readonly traverse: <F>(F: Applicative<F>) => <A, B>(ta: HKT<T, A>, f: (a: A) => HKT<F, B>) => HKT<F, HKT<T, B>>
}

// HKT: Higher Kinded Types(高階型)
// "型の型"を定義する
export function sequence<F, T>(
  f: Applicative<F>,
  t: Traversable<T>
): <A>(tfa: HKT<T, HKT<F, A>>) => HKT<F, HKT<T, A>> {
  return tfa => t.traverse(f)(tfa, fa => fa)
}

// URIは、Monadの識別子で単なる文字列("Option", "List"など)
export function sequence<F extends URIS, T extends URIS>(
  f: Applicative1<F>,
  t: Traversable1<T>
): <A>(tfa: Type<T, Type<F, A>>) => Type<F, Type<T, A>> 
```

Enter fullscreen mode Exit fullscreen mode

`sequence`是具有上述上下文的类型——APP 截断(`option`)，和扫描后各要素可以使用同一函数的类型(虽然这种表达相当粗暴！ )以`traversable`(`array`)为自变量，返回函数。 该函数以成为双重嵌套结构的高阶型(`Option<A>[]`)为自变量，返回将输入顺序颠倒的高阶型(`Option<A[]>`)。 在本例中，由于针对`sequence`返回的函数传递了`Array<Opation<A>>`，最终从`getSomeOrNone`返回了`Option<Array<A>>`。 得到目标的模具。

那么，`sequence`是如何从`Applicative`和`Traversable`中得到`(tfa: HKT<T, HKT<F, A>>) => HKT<F, HKT<T, A>>`的呢？ 答案在`sequense`调用的`Traversable.traverse`函数中。 正如定义中所定义的那样，该函数的类型定义为:无论如何都请部分应用后使用。 实际上，这个函数被称为`t.traverse(f)(tfa, fa => fa)`。 在此，希望大家认为`f`是`Option`，`tfa`是`Array<Option<A>>`，`fa => fa`是`Option<A> => Option<A>`。

当然，这也可以适用于`Option`以外的 APP (`Either`等)和`Array`以外的`traversable`(`tuple`等)。 在模型水平上能做出如此高级的事情！

但是…很难！ 太难了！ 头痛！ ！ 但是习惯了的话就会觉得好像可以了。 (真的吗？ )最方便的事情是确实的，所以如果能使用的话会很高兴吧。 现在写的是 TypeScript。 这样就可以写服务器端的代码和前端的代码了。

### Either 莫纳德

Either 如上所述，是表现失败系和成功系的模型。

```
import { Either, left, right } from 'fp-ts/lib/Either'

export function elementAt<A>(xs: Array<A>, i: number): Either<string, A> {
  if (i < 0) {
    return left<string, A>('out of lower bound')
  }
  if (i >= xs.length) {
    return left<string, A>('out of upper bound')
  }
  return right<string, A>(xs[i])
}

console.log(elementAt([1, 2, 3], -1)) // => left('out of lower bound')
console.log(elementAt([1, 2, 3], 10)) // => left('out of upper bound')
console.log(elementAt([1, 2, 3], 1))  // => right(2) 
```

Enter fullscreen mode Exit fullscreen mode

这已经连解说的必要了吧。 方便…！

### State 莫纳德

最后想介绍的是 state monado。
用 Haskell 写的这样的代码是

```
import Control.Monad.State

main :: IO ()
main = runStateT code [1..] >> return ()
--
-- layer an infinite list of uniques over the IO monad
--

code :: StateT [Integer] IO ()
code = do
    x <- pop
    io $ print x
    y <- pop
    io $ print y
    z <- pop
    io $ print z
    return ()

--
-- pop the next unique off the stack
--
pop :: StateT [Integer] IO Integer
pop = do
    (x:xs) <- get
    put xs
    return x

io :: IO a -> StateT [Integer] IO a
io = liftIO 
```

Enter fullscreen mode Exit fullscreen mode

可以用 fp-ts 这样写的

```
import * as stateT from 'fp-ts/lib/StateT'
import { io, IO } from 'fp-ts/lib/IO'
import { Monad2 } from 'fp-ts/lib/Monad'
import { Endomorphism } from 'fp-ts/lib/function'
import * as array from 'fp-ts/lib/Array'
import { State } from 'fp-ts/lib/State'

declare module 'fp-ts/lib/HKT' {
  interface URI2HKT2<L, A> {
    StateIO: StateIO<L, A>
  }
}

const stateTIO = stateT.getStateT(io)

export const URI = 'StateIO'

export type URI = typeof URI

export class StateIO<S, A> {
  readonly _A!: A
  readonly _L!: S
  readonly _URI!: URI
  constructor(readonly value: (s: S) => IO<[A, S]>) {}
  run(s: S): [A, S] {
    return this.value(s).run()
  }
  eval(s: S): A {
    return this.run(s)[0]
  }
  exec(s: S): S {
    return this.run(s)[1]
  }
  map<B>(f: (a: A) => B): StateIO<S, B> {
    return new StateIO(stateTIO.map(f, this.value))
  }
  ap<B>(fab: StateIO<S, (a: A) => B>): StateIO<S, B> {
    return new StateIO(stateTIO.ap(fab.value, this.value))
  }
  ap_<B, C>(this: StateIO<S, (b: B) => C>, fb: StateIO<S, B>): StateIO<S, C> {
    return fb.ap(this)
  }
  chain<B>(f: (a: A) => StateIO<S, B>): StateIO<S, B> {
    return new StateIO(stateTIO.chain(a => f(a).value, this.value))
  }
}

const map = <S, A, B>(fa: StateIO<S, A>, f: (a: A) => B): StateIO<S, B> => {
  return fa.map(f)
}

const of = <S, A>(a: A): StateIO<S, A> => {
  return new StateIO(stateTIO.of(a))
}

const ap = <S, A, B>(fab: StateIO<S, (a: A) => B>, fa: StateIO<S, A>): StateIO<S, B> => {
  return fa.ap(fab)
}

const chain = <S, A, B>(fa: StateIO<S, A>, f: (a: A) => StateIO<S, B>): StateIO<S, B> => {
  return fa.chain(f)
}

const stateTget = stateT.get(io)
export const get = <S>(): StateIO<S, S> => {
  return new StateIO(stateTget())
}

const stateTput = stateT.put(io)
export const put = <S>(s: S): StateIO<S, void> => {
  return new StateIO(stateTput(s))
}

const stateTmodify = stateT.modify(io)
export const modify = <S>(f: Endomorphism<S>): StateIO<S, void> => {
  return new StateIO(stateTmodify(f))
}

const stateTgets = stateT.gets(io)
export const gets = <S, A>(f: (s: S) => A): StateIO<S, A> => {
  return new StateIO(stateTgets(f))
}

const stateTliftF = stateT.liftF(io)
export const fromIO = <S, A>(fa: IO<A>): StateIO<S, A> => {
  return new StateIO(stateTliftF(fa))
}

const stateTfromState = stateT.fromState(io)
export const fromState = <S, A>(fa: State<S, A>): StateIO<S, A> => {
  return new StateIO(stateTfromState(fa))
}

export const stateIO: Monad2<URI> = {
  URI,
  map,
  of,
  ap,
  chain
}

//
// Usage (adapted from https://wiki.haskell.org/Simple_StateT_use)
//

import { log } from 'fp-ts/lib/Console'

/** pop the next unique off the stack */
const pop: StateIO<Array<number>, number> = get<Array<number>>().chain(ns =>
  array.foldL(ns, () => of(0), (h, t) => put(t).chain(() => of(h)))
)

const program1: StateIO<Array<number>, void> = pop
  .chain(x => fromIO(log(x)))
  .chain(() => pop)
  .chain(y => fromIO(log(y)))
  .chain(() => of(undefined))

program1.run([1, 2, 3]) 
```

Enter fullscreen mode Exit fullscreen mode

因为代码量很大，所以对各安装的解说很少，但是在堵塞的地方利用`StateT`，将`State`和`IO`这两个莫纳德组合起来。 `of`、`ap`、`map`这样的地方(对于不熟悉 Haskell 和 Scala 的人来说很难理解，但是看了 Haskell 的代码就明白了，用那种编程语言没有必要自己安装)如果不自己定义的话

## 最后

非常难以理解&目标变成了不明确的说明……对于这里错了或者这样写比较好之类的反馈，请进行点赞。

也就是说在 fp-ts 以前 Haskell 不是很难吗…？ 请告诉我厉害 h 之后应该读的推荐教程/OSS 信息