# 构建基于堆栈的虚拟机，第 3 部分-说明

> 原文：<https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-3---instructions-4b3a>

在[第二部分](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-2---the-stack-d07)中，我们建立了一个可以用来存储操作数的堆栈。那很酷。我们的下一步是定义允许我们对堆栈中的操作数执行操作的指令。

如果您错过了第 1 部分中的定义，让我们回顾一下我们需要做什么说明。

*   一个名字 -从技术上来说这是可选的，但是如果我要使用一台必须记住我们指令操作码的机器，我会被诅咒的。
*   *操作码*——该指令的唯一编号。重要的是，这不能在 VM 版本之间改变，否则我们将不能运行旧代码。
*   *Arity*——这条指令接受的参数数量——不同于它从堆栈中弹出的操作数数量。想想指令`push 2`；`2`从何而来？它是作为参数提供的。大多数指令都有 0 个参数，但有时你需要给出一个指令，而不仅仅是堆栈的内容。
*   *行为* -我们的指令将执行的动作。在 CPU 中，这是使用晶体管实现的，但在我们的例子中，我们将使用 Rust 函数。

记住所有这些，让我们构建我们的指令类型。请记住，因为我们的操作数是通用的，我们需要我们的指令也是通用的。

```
pub struct Instruction<T> {
  pub op_code: usize,
  pub name: String,
  pub arity: usize,
  pub fun: InstructionFn<T>
}

pub type InstructionFn<T> = fn(machine: &mut Machine<T>, args: &[usize]);

impl<T> Instruction<T> {

    pub fn new(op_code: usize, name: &str, arity: usize, fun: InstructionFn<T>) -> Instruction<T> {
        Instruction {
            op_code: op_code,
            name: String::from(name),
            arity: arity,
            fun: fun
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 不要担心`Machine`型。我们将在下一集讨论这个问题。

这里需要注意的重要一点是，这里的指令参数不是类型`T`——它们是程序数据部分的索引。我们稍后也会谈到这一点。

因为`Instruction`类型只不过是一个方便的容器，所以测试相对简单:

```
#[cfg(test)]
mod test {
    use super::*;

    struct Operand(i64);

    fn noop(_machine: &mut Machine<Operand>, _args: &[usize]) {}

    #[test]
    fn new() {
        let operand = Instruction::new(13, "noop", 7, noop);
        assert_eq!(operand.op_code, 13);
        assert_eq!(operand.name, "noop".to_string());
        assert_eq!(operand.arity, 7);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要一个指令表——一个我们可以存储指令并通过操作码和名字查找指令的结构。我们将使用一个[散列表](https://doc.rust-lang.org/std/collections/struct.HashMap.html)。

```
pub struct InstructionTable<T>(HashMap<usize, Instruction<T>>);

impl<T> InstructionTable<T> {
    pub fn new() -> InstructionTable<T> {
        InstructionTable(HashMap::new())
    }

    pub fn by_op_code(&self, op_code: usize) -> Option<&Instruction<T>> {
        self.0.get(&op_code)
    }

    pub fn by_name(&self, name: &str) -> Option<&Instruction<T>> {
        self.0
            .values()
            .find(|ref instr| instr.name == name)
    }

    pub fn insert(&mut self, instr: Instruction<T>) {
        self.0.insert(instr.op_code, instr);
    }

    pub fn is_empty(&self) -> bool {
        self.0.is_empty()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这并不难。现在让我们写一些测试:

```
#[cfg(test)]
mod test {
    use super::*;
    use machine::Machine;

    fn noop(_machine: &mut Machine<usize>, _args: &[usize]) {}

    #[test]
    fn new() {
        let table: InstructionTable<usize> = InstructionTable::new();
        assert!(table.is_empty())
    }

    #[test]
    fn insert() {
        let mut table: InstructionTable<usize> = InstructionTable::new();
        assert!(table.is_empty());
        table.insert(Instruction::new(0, "NOOP", 0, noop));
        assert!(!table.is_empty());
    }

    #[test]
    fn by_op_code() {
        let mut table: InstructionTable<usize> = InstructionTable::new();
        table.insert(Instruction::new(0, "NOOP", 0, noop));
        let instr = table.by_op_code(0).unwrap();
        assert_eq!(instr.name, "NOOP");
    }

    #[test]
    fn by_name() {
        let mut table: InstructionTable<usize> = InstructionTable::new();
        table.insert(Instruction::new(0, "NOOP", 0, noop));
        let instr = table.by_name("NOOP").unwrap();
        assert_eq!(instr.op_code, 0);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们今天的旅程已经结束了。我们现在有了一个操作数堆栈，以及一种收集和使用指令的方法。在我们的[下一集](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-4---code-3lmi)中，我们将创建必要的结构来表示我们的机器可以执行的程序。