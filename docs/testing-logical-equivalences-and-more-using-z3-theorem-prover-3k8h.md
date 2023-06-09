# 使用 Z3 定理证明器测试逻辑等价(以及更多)

> 原文：<https://dev.to/donaldkellett/testing-logical-equivalences-and-more-using-z3-theorem-prover-3k8h>

[Z3 定理证明器](https://github.com/Z3Prover/z3)是一个 SMT 求解器，它可以找到从数论到命题和谓词逻辑的广泛问题的解决方案(如果存在),反过来也可以详尽地证明某个陈述/定理或一组陈述总是或从不为真。几个月前，我通过某些在线聊天发现了它，但在玩了大约一两个小时后，很快就放弃了它，发现它没有什么实际用途。然而，在最近参加了大学专业(计算机工程)要求的离散数学课程后，它被证明是一个有用的工具，可以帮助我理解一般的逻辑推理和演绎，以及验证某些命题和谓词逻辑家庭作业问题的解决方案。本文旨在介绍 Z3 定理证明器及其在命题逻辑和谓词逻辑中的应用。

首先，请访问 [rise4fun](https://rise4fun.com/z3/tutorial) ，在那里您可以在线试用 Z3 定理证明器。

现在让我们回顾一下什么是命题:

> 命题是一个要么为真要么为假的陈述，但不是两者都是。

所以下面是命题:

*   "我钱包里有 10 美元。"(要么我有[真]要么我没有[假])
*   "艾米可以用 Python 写一个 Hello World 程序."(与上面类似)
*   "道格喜欢比萨饼。"(与上面类似)

而下面的不是:

*   “喂，你好吗？”(一个无法确定真假的开放式问题)
*   "在九月底之前建立并运行我们公司的网站."(一个命令)
*   “这种说法是错误的。”(逻辑悖论不被认为是命题)

在 Z3 定理证明器中，命题被建模为布尔(如在任何编程语言中一样)，或者更确切地说，`Bool` s。因此，如果我们想说“ *p* 是一个(n)(任意)命题，而 *q* 是一个(n)(任意)命题”，那么我们将在 Z3 中编写以下内容(以`;`开头的行是 Z3 注释):

```
; p and q are arbitrary propositional constants
(declare-const p Bool)
(declare-const q Bool) 
```

您可能已经注意到，Z3 有一个类似 Lisp 的语法——所有东西都用括号括起来，每个函数/操作符(甚至是通常用中缀符号编写的操作符，如`+`或`-`)都用前缀符号编写。如果您来自类似 C 的背景(例如 C/C++/ObjC、C#/Java、JavaScript)或类似的背景，您可能会对这种符号感到陌生；然而，不要担心，因为你不需要知道任何 Lisp 来使用 Z3(我也不知道任何 Lisp ), Z3 非常简单和直观。也就是说，可以通过许多常见编程语言(例如 Python 或 C)中的 API 访问 Z3，这可以让您完全避免类似 Lisp 的语法，但我们不会在本文中讨论这一点。

目前，我们的 Z3“程序”没有做任何有用的事情——我们已经声明了两个任意的命题常数 *p* 和 *q* ，但是我们还没有声明我们想要用它做什么。所以让我们让 Z3 来确认一个任意命题 *p* 是否可能为真。这是通过使用断言来完成的:`(assert p)`。为了检查我们的所有断言是否都可以满足*(即至少存在一组我们定义的常量值，使得我们的所有断言都为真)，我们需要在我们的声明和断言下面添加一个`(check-sat)`:*

```
(declare-const p Bool)
(declare-const q Bool)
(assert p)
(check-sat) 
```

当您运行代码时，您应该看到显示的`sat`(“satisfactable”的缩写)。这意味着至少存在一组可能的值`(p, q)`，使得我们的断言`p`为真。那么什么时候我们的断言是正确的呢？我们可以在我们的`(check-sat)`下面添加一行`(get-model)`，显示*我们断言的一个可能的解决方案*:

```
(declare-const p Bool)
(declare-const q Bool)
(assert p)
(check-sat)
(get-model) 
```

您应该会看到类似这样的内容:

```
sat
(model 
  (define-fun p () Bool
    true)
) 
```

因为我们没有断言任何有意义的东西，Z3 只是陈述了显而易见的事实——当任意命题 *p* 被设置为真时，我们的断言`p`为真。但是等等——我们不是把 *p* 声明为常数了吗？为什么现在是函数(`define-fun`)了？事实上，Z3 中的任意常数被视为空元函数，即不接受参数并返回(常数)结果的函数。

现在让我们看一个不小的例子——给定两个任意命题 *p* 和 *q* ，什么时候 *p ∧ q* (读作“p 和 q”)为真？在 Z3 中，逻辑合取 *∧* 是接受两个`Bool`并产生一个`Bool`的函数/运算符`and`:

```
(declare-fun p () Bool) ; Recall that a Z3 constant is just a null-ary function returning a constant result
(declare-const q Bool)
(assert (and p q)) ; (and p q) invokes the `and` function/operator with the two arguments, p and q
(check-sat)
(get-model) 
```

不出所料，Z3 告诉我们当 *p* 和 *q* 都为真时 *p ∧ q* 为真。请注意，`(get-model)`仅仅显示了*对我们的断言的一个*可能的解决方案——我们不能从产生的输出中得出结论，只有当 *p* 和 *q* 同时为真时, *p ∧ q* 才成立:

```
(declare-fun p () Bool)
(declare-const q Bool)
(assert (or p q)) ; We know there are 3 cases where p ∨ q is true; how many will Z3 display?
(check-sat)
(get-model) 
```

上面代码示例的输出只显示了三种可能解决方案中的一种——当 *q* 为假而 *p* 为真时。可以通过`(next-sat)`命令在旧版本 Z3 中直接检索多个模型，也可以通过 API 访问 Z3 来间接检索所有可能的解决方案，但本文不讨论这个问题，因为一个例子/反例通常足以证明一个论点。

到目前为止，我们已经看到 Z3 在寻找解决方案中是有用的，但是我们如何使用它来证明或否定某个定理(或者在我们的例子中是命题/陈述)？这是通过断言我们想要证明的陈述的否定来实现的——如果它给出`unsat`(不可满足的缩写)，那么我们的原始陈述是不可证伪的，即它总是真的。考虑*p∨p*——我们可以很容易地看到它是一个重言式(一个命题，不管它的命题变量的真值如何，它总是为真),通过查看它的真值表得到不同的值 *p* ,所以我们在 Z3 中通过断言它的否定来显示它:

```
(declare-const p Bool)
(define-fun conjecture () Bool
  (or p (not p))
) ; Our conjecture - p ∨ ¬p is a tautology
(assert (not conjecture)) ; assert its negation
(check-sat) ; displays `unsat` as expected 
```

注意:如果一组断言给出了`unsat`，而你试图给出`(get-model)`，那么它会导致一个错误——你可以尝试；)

让我们看一个更复杂的例子。假设对于一些命题 *p* 、 *q* 和 *r* 、 *p* 隐含 *q* 和 *q* 隐含 *r* 。那么按常理来说， *p* 必然隐含着 *r* 。让我们尝试使用 Z3 定理证明器来证明它:

```
(declare-const p Bool)
(declare-const q Bool)
(declare-const r Bool)
(define-fun conjecture () Bool
  (=> (and (=> p q) (=> q r)) (=> p r)) ; (=> p q) means "p implies q"
)
(assert (not conjecture))
(check-sat) ; gives `unsat` as expected 
```

然而，当这第一次在我的脑海中出现时，我犯了一个逻辑谬误，认为说“ *p* 暗示 *q* 和 *q* 暗示 *r* ”在逻辑上等同于说“ *p* 暗示 *r* ”。让我们用 Z3 定理证明器来否定我的猜想:

```
(declare-const p Bool)
(declare-const q Bool)
(declare-const r Bool)
(define-fun conjecture () Bool
  ; logical equivalence/bi-implication is denoted by `=`, e.g. (= p q)
  (= (and (=> p q) (=> q r)) (=> p r))
)
(assert (not conjecture))
(check-sat) ; gives `sat`; my conjecture is falsifiable
(get-model) 
```

原来我们不能互换使用语句 *(p → q) ∧ (q → r)* 和 *p → r* 来表示任何 *p* 、 *q* 和*r*——具体来说，当 *p* 和 *r* 都为真而 *q* 为假时 左侧表达式给出*(p→q)∧(q→r)≡(T→F)∧(F→T)≡F∧T≡F*但右侧表达式给出 *p → r ≡ T → T ≡ T* 。

为了结束本文，让我们看一个 Z3 定理证明器中谓词逻辑的例子。

谓词逻辑中只有两个量词——全称量词 *∀* 和存在量词 *∃* 。那么，我们如何使用谓词逻辑来表达类似“这个班里正好有一个人拥有一台计算机”这样的语句呢？我的教授在讲座中提出了以下建议:

> ∃x(P(x)∧∀y((y≠x)→p(y)))【x 的定义域是班里所有人，p(x)是“x 拥有一台电脑”这句话】

讲座结束后我思考了一下，我突然意识到可能有一种更简洁的表达方式:

> (p(y)(y = x))

所以我开始用 Z3 定理证明器来证明它:

```
(declare-sort U) ; let U be an arbitrary domain
(declare-fun p (U) Bool) ; let p be an arbitrary predicate on members of U
(define-fun conjecture () Bool
  (=
    (exists ((x U)) (and (p x) (forall ((y U)) (=> (not (= y x)) (not (p y))))))
    (exists ((x U)) (forall ((y U)) (= (p y) (= y x))))
  )
)
(assert (not conjecture))
(check-sat) 
```

...果然不出我所料。

感谢阅读我的文章——我希望这是对定理证明及其潜在应用的一个很好的介绍。欢迎在评论中留下您的反馈:)*