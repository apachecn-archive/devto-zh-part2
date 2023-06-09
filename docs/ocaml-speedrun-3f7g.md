# OCaml Speedrun！🐫🐪

> 原文：<https://dev.to/swyx/ocaml-speedrun-3f7g>

OCaml 是 [ReasonML](https://reasonml.github.io/) 的基础，作为一种领先的编译到 JS 语言，它受到了很多关注。今天 Jane Street 在纽约提供了一个免费的 OCaml 研讨会，所以我决定参加并在这里分享我的笔记。Jane Street 多年来已经转移了数十亿美元来运行 OCaml 代码库，因此它们是一个极其可靠的专业知识来源。

公平的警告:这篇文章不像一个普通的开发。来说明这是一次研讨会的引导式演练——你应该跟着做代码，否则这将对你毫无用处。但是通过这 24 个例子(**花了大约 2-3 个小时**)，您应该对 OCaml 中的关键语言特性有了一个基本的了解！

声明:我以前有过使用 Haskell 的经验，所以我可能对静态类型/函数语言有一些无意识的了解。

* * *

*   [在系统上安装 OCaml](#installing-ocaml-on-your-system)
*   [基础知识](#basic-knowledge)
*   [参观车间](#going-through-the-workshop)
*   [你好世界:`/01-introduction`](#hello-world----01-introduction-)
*   [基本数据类型:`/02-basic_types`](#basic-data-types----02-basic-types-)
*   [定义功能:`/03-define_functions`](#defining-functions----03-define-functions-)
*   [调用功能:`/04-call_functions`](#calling-functions----04-call-functions-)
*   [充当论元:`/05-twice`](#functions-as-arguments----05-twice-)
*   [模式匹配:`/06-pattern-matching`](#pattern-matching----06-pattern-matching-)
*   [递归:`/07-simple_recursion`](#recursion----07-simple-recursion-)
*   [数据类型:链表:`/08-list_intro`](#data-type--linked-lists----08-list-intro-)
*   [建筑列表:`/09-list_range`](#building-lists----09-list-range-)
*   [递归遍历列表:`/10-list_product`](#recursing-through-a-list----10-list-product-)
*   [抽象功能:`/11-sum_product`](#abstracting-functions----11-sum-product-)
*   [浮动功能:`/12-list_min`](#float-functions----12-list-min-)
*   [抽象和浮点函数:`/13-largest_smallest`](#abstractions-and-float-functions----13-largest-smallest-)
*   [数据类型:变体类型又名枚举`/14-variants`](#data-type--variant-types-aka-enums---14-variants-)
*   [数据类型:元组和参数化类型`/15-tuples`](#data-type--tuples-and-parameterized-types---15-tuples-)
*   [贴上标签的论点`/16-labelled_arguments`](#labelled-arguments---16-labelled-arguments-)
*   [数据类型:选项`/17-options`](#data-type--options---17-options-)
*   [匿名函数`/18-anonymous_functions`](#anonymous-functions---18-anonymous-functions-)
*   [列表操作`/19-list_operations`](#list-operations---19-list-operations-)
*   [类型定义！`/20-reading_sigs`](#type-definitions----20-reading-sigs-)
*   [折心`/21-writing_list_operations`](#folding-cardio---21-writing-list-operations-)
*   [数据类型:不可变记录`/22-records`](#data-type--immutable-records---22-records-)
*   [数据类型:可变记录`/23-mutable_records`](#data-type--mutable-records---23-mutable-records-)
*   [数据类型:参考文献`/24-refs`](#data-type--refs---24-refs-)
*   [都是乡亲们！](#that-s-all-folks-)
*   [额外知识](#extra-knowledge)

* * *

# 在系统上安装 OCaml

跟着这个:[https://github.com/janestreet/install-ocaml](https://github.com/janestreet/install-ocaml)。按照这些说明，我们没有任何问题。对于我的开发环境，我选择了 VS 代码中的“reason IDE”扩展，但是 vim/emacs 似乎也得到了很好的支持。不支持 Sublime。

# 基础知识

[opam](https://opam.ocaml.org/) 是 OCaml 的包管理器。如果你按照上面的说明，你已经使用它。

作为上述过程的一部分，你还需要安装 [utop](https://github.com/diml/utop) ，Jane Street 推荐这是一个比默认设置更好的“顶层”。一个“顶级”也被称为 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 。

merlin 是用于编译/语法突出显示/代码完成的工具。

我们没有使用“原始的 OCaml”——我们使用的是 Jane Street 的 [Base flavor](https://opensource.janestreet.com/) ，它用一些他们的观点覆盖了 OCaml 的 stdlib。这是你会在所有习题集的第一行看到的:

```
open! Base 
```

Enter fullscreen mode Exit fullscreen mode

模块导入都是这样做的。稍后我们会看到更多。

# 穿过车间

`git clone https://github.com/janestreet/learn-ocaml-workshop`

并开启`/02-exercises`。我们要把这些都过一遍！

# 你好世界:`/01-introduction`

正如在`problem.ml`中所说，只需运行`jbuilder runtest`就可以看到错误:

```
 ✝  learn-ocaml-workshop/02-exercises/01-introduction> jbuilder runtest
Entering directory '/Users/swyx/ocaml/learn-ocaml-workshop'
         ppx 02-exercises/01-introduction/problem.pp.ml (exit 1)
(cd _build/default && ./.ppx/ppx_jane/ppx.exe --dump-ast --cookie 'library-name="problem_1"' -o 02-exercises/01-introduction/problem.pp.ml --impl 02-exercises/01-introduction/problem.ml)
File "02-exercises/01-introduction/problem.ml", line 25, characters 22-23:
Error: String literal not terminated 
```

Enter fullscreen mode Exit fullscreen mode

所以，如果你修改第 25 行:`let () = Stdio.printf "Hello, World!"`加上最后一个引号，你会得到

```
 ✝  learn-ocaml-workshop/02-exercises/01-introduction> jbuilder runtest
Entering directory '/Users/swyx/ocaml/learn-ocaml-workshop'
         run alias 02-exercises/01-introduction/runtest
Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

世界快乐！请注意，当您运行`jbuilder`时，一个新的`.merlin`文件是如何添加的——这是编译器在工作。

# 基本数据类型:`/02-basic_types`

再次转到`problem.ml`并阅读。您的任务是实现第 65 行和第 68 行的两个函数:

```
let int_average x y = failwith "For you to implement"
(* val float_average : float -> float -> float *)
let float_average x y = failwith "For you to implement" 
```

Enter fullscreen mode Exit fullscreen mode

如果您再次运行`jbuilder`，您将会看到错误，因为这些函数目前是用“failwith”实现的。是时候开始实施了！

请注意，类型签名被注释掉了。这个文件夹也有一个`problem.mli`文件。这个文件声明了相关文件的接口，并且碰巧有您需要的签名，所以我们不需要担心它。

### 解

`int_average`可以用:`let int_average x y = (x + y) / 2`来解决，哪个有道理。但是`float_average`需要特定于浮点的操作符(这不同于 Haskell ),否则你会得到这个错误:

```
File "02-exercises/02-basic_types/problem.ml", line 163, characters 27-29:
Error: This expression has type float but an expression was expected of type Base__Int.t = int 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您实际转到第 163 行，您可以看到生成该错误的测试。你可以用浮点操作符来解决这个问题(在第 13-15 行中提到):

`let float_average x y = (x +. y) /. 2.`

# 定义功能:`/03-define_functions`

这个很简单。我确实喜欢这样的事实

> 在 OCaml 中，在字符串之外，空白和换行符是一样的。

### 解

```
let plus x y   = x + y

let times x y  = x * y

let minus x y  = x - y

let divide x y = x / y 
```

Enter fullscreen mode Exit fullscreen mode

# 调用函数:`/04-call_functions`

两个数的平均值相加然后减半。

### 解

```
let average x y = half (add x y) 
```

Enter fullscreen mode Exit fullscreen mode

玩弄多行语法和隐式返回，这也是可行的:

```
let average x y =
  let res = add x y in
  half res 
```

Enter fullscreen mode Exit fullscreen mode

还有这个也是:

```
let average x y =
  let res = add
    x
    y in
  half
    res 
```

Enter fullscreen mode Exit fullscreen mode

# 充当论元:`/05-twice`

我觉得，现在作为一等公民的职能在任何地方都是一个被广泛接受的概念。

### 解

```
let add1 x = x + 1
let square x = x * x
let twice f x = f (f x)
let add2 = twice add1
let raise_to_the_fourth = twice square 
```

Enter fullscreen mode Exit fullscreen mode

# 模式匹配:`/06-pattern-matching`

另一个熟悉的模式来自 [Haskell](https://www.haskell.org/tutorial/patterns.html) ，并在 [Javascript](https://github.com/tc39/proposal-pattern-matching) 中被提出。但是需要一个特殊的关键字`match _ with`

### 解

```
let non_zero x =
  match x with
  | 0 -> false
  | _ -> true 
```

Enter fullscreen mode Exit fullscreen mode

# 递归:`/07-simple_recursion`

请参阅:递归。递归函数需要用`let rec`声明

### 解

```
let rec add_every_number_up_to x =
  (* make sure we don't call this on negative numbers! *)
  assert (x >= 0);
  match x with
  | 0 -> 0
  | _ -> x + (add_every_number_up_to (x-1))

(* Let's write a function to multiply every number up to x. Remember: [factorial 0] is 1 *)
let rec factorial x =
  assert (x >= 0);
  match x with
  | 0 -> 1
  | 1 -> 1
  | _ -> x * (factorial (x-1)) 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:链表:`/08-list_intro`

本练习将数组与模式匹配和递归配对。这里棘手的一点是立即破坏你正在匹配的列表，这让我有点困惑。但是如果你仔细观察，提供的例子是有启发性的。

### 解

```
let rec sum lst =
  match lst with
  | [] -> 0
  | hd :: tl -> hd + (sum(tl)) 
```

Enter fullscreen mode Exit fullscreen mode

# 建筑列表:`/09-list_range`

这又是一个递归的回答。你想让`range`函数递归，然后一直调用它自己，直到`from`等于`to_`。我最初是这样尝试的:

```
let rec range from to_ =
  match from with
  | to_ -> []
  | _ -> (from :: (range (from + 1) to_)) 
```

Enter fullscreen mode Exit fullscreen mode

这不起作用，因为匹配的*将*分配给`to_`而不是与之比较，这很烦人。

### 解

```
let rec range from to_ =
  match from = to_ with
  | true -> []
  | false -> (from :: (range (from + 1) to_)) 
```

Enter fullscreen mode Exit fullscreen mode

这里的单个`=`是一个“中缀等式”操作符，它对整数很有效，这就是我们在这里使用它的原因。但是请注意，他们不得不使用“PPX 重写器”(参见“额外知识”部分)来实现同一个问题集中的列表比较`[%compare.equal: int list]`。

# 递归遍历列表:`/10-list_product`

这个和你做列表求和的练习 8 差不多。

### 解

```
let rec product xs =
  match xs with
  | [] -> 1
  | a :: b -> a * product(b) 
```

Enter fullscreen mode Exit fullscreen mode

# 抽象化功能:`/11-sum_product`

这是一个相当棘手的问题。我们正在创造一个创造一个功能的功能，抽象出重复的行为。从第 5-36 行开始，他们会带你看一个如何做的例子，然后从第 47 行开始，你会看到一个相似但不同的行为模式。祝你好运，并注意正在使用的模式！

### 解

```
let rec every answer combine xs =
  match xs with
  | [] -> answer
  | x :: ys -> combine x (every answer combine ys)

(* Now let's rewrite sum and product in just one line each using every *)
let simpler_sum xs     = every 0 plus xs
let simpler_product xs = every 1 times xs 
```

Enter fullscreen mode Exit fullscreen mode

相当整洁！您也可以将中缀操作符作为函数(`let simpler_sum xs = every 0 (+) xs`)传递，但是您不能对`*`操作符这样做，因为`(*)`与 OCaml 中的注释相冲突。

# 浮动功能:`/12-list_min`

我们第一次遇到`Float.max`、`Float.min`、`Float.infinity`和`Float.neg_infinity`。相当直接。

### 解

```
let rec smallest xs =
  match xs with
  | [] -> Float.infinity
  | x :: ys -> Float.min x (smallest ys) 
```

Enter fullscreen mode Exit fullscreen mode

# 抽象和浮点函数:`/13-largest_smallest`

结合最后两个练习——再次抽象函数并使用浮动函数。

### 解

```
let simpler_largest  xs = every Float.neg_infinity Float.max xs
let simpler_smallest xs = every Float.infinity Float.min xs 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:变体类型又名枚举`/14-variants`

变体的工作方式类似枚举，只是它们实际上可以携带数据:

```
type card_value =
  | Ace
  | King
  | Queen
  | Jack
  | Number of int

let one_card_value : card_value = Queen
let another_card_value : card_value = Number 8 
```

Enter fullscreen mode Exit fullscreen mode

而且这个很好听:)

### 解

```
let card_value_to_score card_value =
  match card_value with
  | Ace      -> 11
  | King     -> 10
  | Queen    -> 10
  | Jack     -> 10
  | Number i -> i 
```

Enter fullscreen mode Exit fullscreen mode

这也适用于“或”匹配

```
let card_value_to_score card_value =
  match card_value with
  | Ace      -> 11
  | King
  | Queen
  | Jack     -> 10
  | Number i -> i 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:元组和参数化类型`/15-tuples`

元组在其他语言中就是这样，但是它们的类型定义看起来有点奇怪

```
type int_and_string_and_char = int * string * char
let example : int_and_string_and_char = (5, "hello", 'A') 
```

Enter fullscreen mode Exit fullscreen mode

函数不必事先定义它们的类型。它们可以返回传递给它们的相同类型的东西:

```
type 'a pair = 'a * 'a` 
```

Enter fullscreen mode Exit fullscreen mode

### 解

您可以在函数定义中进行析构:

```
(* this works *)
(* let add coord1 coord2 =
  let (a, b) = coord1 in
  let (c, d) = coord2 in
  (a+c, b+d) *)

(* this also works *)
let add (a, b) (c, d) = (a+c, b+d) 
```

Enter fullscreen mode Exit fullscreen mode

并且再次

```
(* this works *)
(* let first pair =
  let (a, _) = pair in
  a *)
(* this too *)
let first (a, _) = a
(* this *)
let second (_,b) = b 
```

Enter fullscreen mode Exit fullscreen mode

内置函数`fst`和`snd`也做同样的事情。

# 贴上标签的论点`/16-labelled_arguments`

标记论点...

```
val divide : dividend:int -> divisor:int -> int
let divide ~dividend ~divisor = dividend / divisor 
```

Enter fullscreen mode Exit fullscreen mode

带标签的参数可以以任何顺序传入。)

### 解

```
let modulo ~dividend ~divisor = dividend - (divisor * divide ~dividend ~divisor) 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:选项`/17-options`

['a option]要么是[None]，意思是缺少数据，要么是[Some x]，意思是
数据存在，具体来说就是[x]。

### 解

```
let safe_divide ~dividend ~divisor =
  match divisor = 0 with
  | true -> None
  | false -> Some (dividend / divisor) 
```

Enter fullscreen mode Exit fullscreen mode

# 匿名函数`/18-anonymous_functions`

lambda 函数！用`fun`关键字定义。例如双重功能:

```
(fun i -> 2 * i) 
```

Enter fullscreen mode Exit fullscreen mode

讽刺的是，这个问题根本没有测试这方面的知识。

### 解

```
let apply_if_nonzero f i =
  match i = 0 with
  | true -> 0
  | false -> f i 
```

Enter fullscreen mode Exit fullscreen mode

# 列表操作`/19-list_operations`

现在正在介绍`List`的操作:`List.map`、`List.iter`、`List.fold_left`、`List.find`、`List.find_exn`。

### 解

这是我第一次尖锐的回答

```
let divide dividend divisor  = dividend / divisor
let modulo ~dividend ~divisor = dividend - (divisor * divide dividend divisor)
let mod2 x = modulo x 2
let ifEven x =
  match mod2 x with
  | 0 -> 1
  | _ -> 0
let num_even_ints ints =
  let first = List.map
    ~f:(fun x -> ifEven x)
    ints in
  sum_of_my_ints first 
```

Enter fullscreen mode Exit fullscreen mode

但是简街的核心显然有过滤和计数功能:

```
Core.List.count ~f:(fun x -> x mod 2 = 0) 
```

Enter fullscreen mode Exit fullscreen mode

# 类型定义！`/20-reading_sigs`

到目前为止，我们还没有任何编写自己的类型定义的实践，所以这将是棘手的。我们必须在大约 80 行中编写自己的 typedefs。这里有两件事需要注意:

1.  我们必须返回抽象类型`t`，而不是硬编码到`int`，即使测试是`int`
2.  带标签的参数也可以放入 typedef！

在这里，您还可以看到模块导入语法。我们通过在开头添加`open! Prelude`(注意大写首字母)来导入`prelude.ml`。

我们在这里也开始用关键字`module`定义作用域模块，用类型的`sig/end`对，然后用代码的`struct/end`对:

```
module Example : sig
  (*  type defs *)
end = struct
  (*  code *)
end 
```

Enter fullscreen mode Exit fullscreen mode

### 方案

```
 val create: numerator:int -> denominator:int -> t
  val value: t -> float 
```

Enter fullscreen mode Exit fullscreen mode

# 折叠有氧`/21-writing_list_operations`

这里有一堆练习。我第一次失败是因为直接追加方法`a :: [b]`以错误的顺序追加内容，所以我需要使用`List.append`来切换顺序，因为`[b] :: a`不是有效的语法。(也可以用`List.fold_right`

### 方案

```
 let map f lst = List.fold_left
    lst
    ~init:[]
    ~f:(fun acc x ->  List.append acc [f(x)])

  let iter f lst = List.fold_left
    lst
    ~init:()
    ~f:(fun acc x -> f(x))

  let filter f lst = List.fold_left
    lst
    ~init:[]
    ~f:(fun acc x ->
      match f(x) with
      | true -> List.append acc [x]
      | _ -> acc
    ) 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:不可变记录`/22-records`

新数据类型！并创建一个返回记录的函数。

### 方案

```
let modify_person (person : person) =
  match person.first_name with
  | "Jan" -> {person with age = 30}
  | _ -> {person with number_of_cars = person.number_of_cars + 6} 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:可变记录`/23-mutable_records`

可变记录用
声明

```
type color =
  | Red
  | Yellow
  | Green [@@deriving compare]

type stoplight =
  { location      : string (* stoplights don't usually move *)
  ; mutable color : color  (* but they often change color *)
  } [@@deriving compare] 
```

Enter fullscreen mode Exit fullscreen mode

并修改为:

```
let set_color stoplight color =
  stoplight.color <- color 
```

Enter fullscreen mode Exit fullscreen mode

### 方案

```
let advance_color stoplight =
  match stoplight.color with
  | Red -> set_color stoplight Green
  | Yellow -> set_color stoplight Red
  | Green -> set_color stoplight Yellow 
```

Enter fullscreen mode Exit fullscreen mode

# 数据类型:引用`/24-refs`

它们被声明为:

```
let x = ref 0 
```

Enter fullscreen mode Exit fullscreen mode

并修改为:

```
let () =
  x := !x + 1 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案在没有 ref:
的情况下也能工作

```
let take op a b =
  match op a b with
  | true -> a
  | false -> b

let min_and_max lst =
  List.fold_left
    lst
    ~init:(Int.max_value, Int.min_value)
    ~f:(fun (curmin, curmax) x ->
      (take (<) curmin x, take (>) curmax x)
    ) 
```

Enter fullscreen mode Exit fullscreen mode

### 方案

使用`ref`的一些注意事项:

*   您应该在函数中限定它的范围，否则函数之间会有一个持久状态
*   `List.iter`是`List.map`，没有返回值，会有一个警告。

```
let take op a b =
  match op a b with
  | true -> a
  | false -> b

let min_and_max lst =
  let min = ref Int.max_value in
  let max = ref Int.min_value in
  List.iter
    ~f:(fun x ->
      min := take (<) !min x;
      max := take (>) !max x;
      )
    lst;
  (!min, !max) 
```

Enter fullscreen mode Exit fullscreen mode

* *注意:也可以在评论中看到 Christophe 的解决方案**

# 那都是乡亲们！

还不算太糟，是吗？接下来，您可以尝试处理他们的“froggy”示例，但是使用`Js_of_ocaml`库有很多实现特定的东西。

# 额外知识

PPX 重写器用新的语法扩展了基本的 ocaml 语言，它将编译成原始的 OCaml。他们是 OCaml 的“巴别塔”。例如

```
assert([%compare.equal: int list]
        (5 : :[1;8;4])
        [5;1;8;4]) 
```

Enter fullscreen mode Exit fullscreen mode

### `let`与`in`并驾齐驱，与`;;`绝招

没有与`in`成对出现的`let`可以跳到下一行可能不相关的代码。您可以通过添加双分号来捕获错误，以便编译器知道您已经完成了顶级定义。

```
let min_and_max lst =
  let min = ref Int.max_value in
  let max = ref Int.min_value in
  List.iter
    ~f:(fun x ->
      min := take (<) !min x;
      max := take (>) !max x;
      )
    lst;
  (!min, !max)
;; 
```

Enter fullscreen mode Exit fullscreen mode

### 比较事物的四种方式

这些基本上都是在我们车间试运行中被打破的东西；您应该不会遇到这些，但是这些是调用 OCaml 语法的有用参考(不仅仅是为了比较)

```
 assert ([%compare.equal: string] s "hello");
  assert (String.equal s "hello");
  assert (String.(=) s "hello");
  assert String.(s = "hello"); 
```

Enter fullscreen mode Exit fullscreen mode