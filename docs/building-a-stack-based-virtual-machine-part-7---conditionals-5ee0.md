# 构建基于堆栈的虚拟机，第 7 部分-条件

> 原文：<https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-7---conditionals-5ee0>

在最后一集中，我们学习了如何让堆栈机器处理函数调用。一个非常方便的语言特性，如果我这么说的话。今天我们将学习如何模拟条件句。如果你一直在家跟着做，那么你可能已经自己弄明白了，但是如果你没有，让我们来看看它是怎么做的。

注意，我们已经有了让条件在我们的程序中工作的所有基础设施，这一切都归结到可以选择性地执行跳转的新指令。

和前面的例子一样，我们需要一个可以包含整数或字符串的操作数类型，这样我们就可以找到合适的标签。

```
enum Operand {
    I(i64),
    S(String),
}

impl Operand {
    fn to_i(&self) -> Option<i64> {
        match self {
            &Operand::I(i) => Some(i),
            _ => None,
        }
    }

    fn to_s(&self) -> Option<&str> {
        match self {
            &Operand::S(ref s) => Some(s),
            _ => None,
        }
    }
}

impl From<i64> for Operand {
    fn from(i: i64) -> Self {
        Operand::I(i)
    }
}

impl<'a> From<&'a str> for Operand {
    fn from(s: &'a str) -> Self {
        Operand::S(s.to_string())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们将需要我们的标准*推送*指令:

```
fn push(machine: &mut Machine<Operand>, args: &[usize]) {
    let arg = machine.get_data(args[0]).clone();
    machine.operand_push(arg);
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来是无条件跳转。该指令使用机器的`jump`方法将 IP 直接移动到标签标识的位置。很明显，你会想做比我在这里做的更多的检查(标签存在吗？如果自变量是一个整数会怎样)，但这是一个博客，所以 YOLO。

您也可以将它改为从堆栈中读取跳转标签或位置。那也很棒。

```
fn jump(machine: &mut Machine<Operand>, args: &[usize]) {
    let label = machine.get_data(args[0]).clone();
    machine.jump(label.to_s().unwrap());
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，也是最神奇的部分是我们新的`jump_if`指令。

此指令从堆栈中弹出顶部操作数，并假设它是一个整数。如果整数不为零，那么它执行一个跳转到指定的标签，否则它是一个空操作，机器只是继续下一条指令。

```
fn jump_if(machine: &mut Machine<Operand>, args: &[usize]) {
    let condition = machine.operand_pop().to_i().unwrap();
    if condition != 0 {
        let label = machine.get_data(args[0]).clone();
        machine.jump(label.to_s().unwrap());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们组装我们的指令表:

```
fn instruction_table() -> InstructionTable<Operand> {
    let mut it = InstructionTable::new();
    it.insert(Instruction::new(0, "push", 1, push));
    it.insert(Instruction::new(1, "jump_if", 1, jump_if));
    it.insert(Instruction::new(2, "jump", 1, jump));
    it
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以构建一个非常简单的程序，将一个操作数推送到堆栈上，如果它为“true ”,则跳转到“if_true”标签，否则跳转到“end”标签。

```
fn conditional_program(condition: Operand) -> Operand {
    let it = instruction_table();
    let mut builder: Builder<Operand> = Builder::new(&it);

    // Push our condition operand into the stack
    builder.push("push", vec![condition]);

    // Add our `jump_if` instruction and tell it to jump to the `if_true` label.
    builder.push("jump_if", vec![Operand::from("if_true")]);

    // These instructions will be skipped if the operand is true:
    // * Push "it was false" onto the stack.
    // * Unconditionally jump to the end of the program.
    builder.push("push", vec![Operand::from("it was false")]);
    builder.push("jump", vec![Operand::from("end")]);

    // This instruction will be skipped of the operand is false:
    // * Push "it was true" onto the stack.
    builder.label("if_true");
    builder.push("push", vec![Operand::from("it was true")]);

    // Points at the end of the program, which will cause the machine to halt.
    builder.label("end");

    // Run the program:
    let code = Code::from(builder);
    let constants: WriteManyTable<Operand> = WriteManyTable::new();
    let mut machine: Machine<Operand> = Machine::new(code, &constants, &it);
    machine.run();

    // Return the operand at the top of the stack to the caller:
    machine.operand_pop()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以编写测试来证明它是有效的:

```
#[test]
fn condition_true() {
    let result = conditional_program(Operand::from(1));
    assert_eq!(result.to_s().unwrap(), "it was true")
}

#[test]
fn condition_false() {
    let result = conditional_program(Operand::from(0));
    assert_eq!(result.to_s().unwrap(), "it was false")
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，对条件进行建模是非常简单的，尽管你可能需要跟踪一堆标签。您可能还注意到了如何构建一个 switch 语句模型，其中条件子句可以进入下一个子句。

这就完成了我们对基于堆栈的虚拟机如何工作的旋风之旅。我真希望你已经学到了一些东西。我鼓励你[阅读源代码](https://gitlab.com/huia-lang/stack-vm)，并随时提出你的任何问题。如果我不知道答案，我当然想找出答案。