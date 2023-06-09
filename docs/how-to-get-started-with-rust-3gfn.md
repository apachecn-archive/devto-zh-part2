# 如何入门 Rust

> 原文：<https://dev.to/dannypsnl/how-to-get-started-with-rust-3gfn>

铁锈是一种奇怪的语言。

开始吧！

## 移动

第一点是移动语义。

```
fn main() {
    let s = "hello".to_string();
    let t = s;
    let u = s;
} 
```

Enter fullscreen mode Exit fullscreen mode

你还能指望什么`t` & `u`是`s`？不要！

`rustc`表示:

```
error[E0382]: use of moved value: `s`
 --> main.rs:4:9
  |
3 | let t = s;
  | - value moved here
4 | let u = s;
  | ^ value used here after move
  |
  = note: move occurs because `s` has type `std::string::String`, which does not implement the `Copy` trait 
```

Enter fullscreen mode Exit fullscreen mode

这些错误告诉您失败的原因。

在 Rust 中，你应该预料到赋值的默认行为是移动！

### 复制

你可以预料，如果有任何类型实现了`Copy`特征，它将被复制。

这就是为什么这些代码会工作:

```
let s = 1;
let t = s;
let u = s; 
```

Enter fullscreen mode Exit fullscreen mode

实现`Copy`特征可以在 Rust 中使用`derive`符号。

```
#[derive(Clone, Copy)]
struct Foo {
    number: i32
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`Clone`是必需的。

但是一个类型实现`Copy`不能有一个字段不实现`Copy`！！！

因此下面的代码将会失败。

```
#[derive(Clone, Copy)]
struct Foo {
    label: String
} 
```

Enter fullscreen mode Exit fullscreen mode

错误消息:

```
error[E0204]: the trait `Copy` may not be implemented for this type
 --> main.rs:1:17
  |
1 | #[derive(Clone, Copy)]
  | ^^^^
2 | struct Foo {
3 | number: String,
  | -------------- this field does not implement `Copy` 
```

Enter fullscreen mode Exit fullscreen mode

## 可变

在 Rust 中，可变和不可变是非常不同的。

```
let s = "one ".to_string();
s.push_str("two "); 
```

Enter fullscreen mode Exit fullscreen mode

错误报告:

```
error[E0596]: cannot borrow immutable local variable `s` as mutable
 --> main.rs:3:5
  |
2 | let s = "one ".to_string();
  | - consider changing this to `mut s`
3 | s.push_str("two ");
  | ^ cannot borrow mutably 
```

Enter fullscreen mode Exit fullscreen mode

因为`String::push_str`借用`&mut self`，所以不能被不可变的`String`使用。

## 引用

我们已经看到`String`，它叫**拥有指针**，其他还有`Vec`、`Box`等。

但是这些指针会移动数据。有时候，我们不需要移动数据，也不想复制数据。

参考表演时间到了！

Rust 的参考有几点。

1.  显式地使用它所指向的东西

```
let a = 1;
let r = &a;
assert!(*r == 1); 
```

Enter fullscreen mode Exit fullscreen mode

1.  对可变所有权的可变引用

```
let mut num = 15;
let r = &mut num;
*r += 10;
assert!(*r == 25); 
```

Enter fullscreen mode Exit fullscreen mode

1.  引用从不为空
2.  你不能借一个价值，当你还活着的时候，它会比你活得久

```
let r;
{
    let x = 1;
    r = &x;
} 
```

Enter fullscreen mode Exit fullscreen mode

错误消息:

```
error[E0597]: `x` does not live long enough
 --> main.rs:5:14
  |
5 | r = &x;
  | ^ borrowed value does not live long enough
6 | }
  | - `x` dropped here while still borrowed
7 | }
  | - borrowed value needs to live until here 
```

Enter fullscreen mode Exit fullscreen mode

问题是在`x`已经被删除后`r`还可以被访问！这意味着一个悬空指针。铁锈不允许。

## 结论

我认为当你是 Rust 的初学者时，这些是最难的部分。

因为 Rust 选择了一种新的方式来处理它的内存，移动默认，检查实时，通过引用共享数据。

了解这些东西对熟悉 Rust 最重要。

希望你喜欢它&能从中得到一些帮助。

### 参考文献:

#### [编程生锈](http://shop.oreilly.com/product/0636920040385.do)

*   作者:吉姆·布兰迪和杰森·奥伦多夫
*   国际标准书号:978-1-491-92728-1