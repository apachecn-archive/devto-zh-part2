# 构建基于堆栈的虚拟机，第 2 部分——堆栈

> 原文：<https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-2---the-stack-d07>

你会注意到在第一部分中并没有真正的代码。很抱歉。让我们现在就解决这个问题。

如果你想跳到最后，完整的 stack-vm 源代码在 Gitlab 上的[。](https://gitlab.com/huia-lang/stack-vm)

我们需要构建堆栈机器的第一件事是堆栈:一种我们可以将操作数推入/取出的数据结构。因为我们使用 Rust，这意味着我们将使用一个[向量](https://doc.rust-lang.org/book/second-edition/ch08-01-vectors.html)，并且我们需要它是[通用的](https://doc.rust-lang.org/book/second-edition/ch10-01-syntax.html)。

```
pub struct Stack<T>(Vec<T>)

impl<T> Stack<T> {
  pub fn new() -> Stack<T> {
    Stack(vec![])
  }

  pub fn is_empty(&self) -> bool {
    self.0.is_empty()
  }

  pub fn push(&mut self, value: T) {
    self.0.push(value);
  }

  pub fn pop(&mut self) -> T {
    self.0.pop().expect("Unable to pop from empty stack")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一个只能推入和弹出的通用堆栈。我们还添加了`is_empty`来使测试变得简单一些。说到测试，下面是测试。请注意，在我们的测试中，我们将它作为一个`usize`堆栈，以使它更好、更容易使用。

```
#[cfg(test)]
mod test {
  use super::*;

    #[test]
    fn new() {
        let stack: Stack<usize> = Stack::new();
        assert!(stack.is_empty());
    }

    #[test]
    fn push() {
        let mut stack: Stack<usize> = Stack::new();
        stack.push(13);
        assert!(!stack.is_empty());
    }

    #[test]
    fn pop() {
        let mut stack: Stack<usize> = Stack::new();
        stack.push(13);
        let value = stack.pop();
        assert_eq!(value, 13);
    }

    #[test]
    #[should_panic(expected = "empty stack")]
    fn empty_pop() {
        let mut stack: Stack<usize> = Stack::new();
        stack.pop();
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在下一期中，我们将弄清楚什么是指令，如何构建一个指令，并制作一个漂亮的表来存储它们。