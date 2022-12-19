# 构建基于堆栈的虚拟机，第 6 部分——函数调用

> 原文：<https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-6---function-calls-2md5>

在最后一集中，我们终于运行了一个程序。这太令人兴奋了。这一集我们将添加函数调用。

到目前为止，每当我们使用术语“堆栈”时，我们都指操作数堆栈，但通常当程序员谈论堆栈时，他们谈论的是[调用堆栈](https://en.wikipedia.org/wiki/Call_stack)。

那么什么是调用栈，它有什么用途呢？很高兴你问了。

当你调用一个函数时，你是在执行从程序中的一个地方(指令指针)到程序中的另一个地方(希望有名字)的跳转。函数调用和跳转的区别在于，你必须存储你从跳转到*的位置，这样当你完成时就可以返回。*

在执行调用之前，只需将参数推送到堆栈上，就可以传递参数。被调用方应该从堆栈中弹出适当数量的参数，并将适当数量的返回值推回堆栈。

> 是的，这意味着函数可以有多个返回值。机器如何获取参数和返回值被称为它的[调用约定](https://en.wikipedia.org/wiki/Calling_convention)。这里我们选择了最简单的约定，让你，编译器的作者，自己决定函数调用的行为。

那么什么是调用栈呢？我们通常称它们为堆栈*帧*，所以这就是我们将要构建的。在这个阶段，我们的帧包含一个值——我们的返回地址。

> 帧通常会包含其他环境信息，例如局部变量或任何其他应该在此范围内的信息。

```
pub struct Frame {
    pub return_address: usize
}

impl Frame {
  pub fn new(return_address: usize) -> Frame {
    Frame { return_address: return_address }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么我们如何把它们堆成一堆呢？原来我们已经有了一个通用的`Stack<T>`可以用于这项工作。让我们把它加到我们的机器上:

```
pub struct Machine<'a, T> {
  code: Code<T>,
  instruction_table: &'a InstructionTable<T>,
  ip: usize,
  operand_stack: Stack<T>,
  call_stack: Stack<Frame>,
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要更新我们的`new`函数来初始化调用栈。我们创建一个指向程序中最后一条指令的框架，这样如果最后一个框架弹出，IP 就指向程序之外，机器就会停止。

我们还需要将以下方法添加到我们的机器中:

*   `jump` -将机器的指令指针更新为指定标签的指针。
*   `call` -创建一个新的堆栈帧，将当前 IP 作为其返回地址。
*   `ret` -从堆栈中弹出顶部框架，并将机器的 IP 设置回原始位置。

```
impl<'a, T> Machine<'a, T> {
    pub fn new(code: Code<T>, instruction_table: &'a InstructionTable<T>) -> Machine<'a, T> {
        let frame = Frame::new(code.code.len());
        let mut call_stack = Stack::new();
        call_stack.push(frame);

        Machine {
            code: code,
            instruction_table: instruction_table,
            ip: 0,
            call_stack: call_stack,
            operand_stack: Stack::new(),
        }
    }

    pub fn jump(&mut self, label: &str) {
        self.ip = self
            .code
            .get_label_ip(label)
            .expect(&format!("Attempted to jump to unknown label {}", label));
    }

    pub fn call(&mut self, label: &str) {
        self.call_stack.push(Frame::new(self.ip));
        self.jump(label);
    }

    pub fn ret(&mut self) {
        let frame = self.call_stack.pop();
        self.ip = frame.return_address;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了自己的方法，我们可以将之前的算术程序转换成一个有`add`函数的程序。这里我们使用一个`enum`作为操作数类型，因为现在我们需要存储标签(字符串)和整数。

在我们编写代码之前，这里是我们的汇编程序，这样更容易理解:

```
@0 = I(3)
@1 = I(4)
@2 = S("add_fun")

.main:
    push @0
    push @1
    call @2
    ret

.add_fun:
    add
    ret 
```

Enter fullscreen mode Exit fullscreen mode

为了理解它在做什么，让我们一步一步来看:

1.  *将整数`3`推送到堆栈上*。
2.  *将整数`4`推送到堆栈上*。
3.  *调用*的`"add_fun"`函数，该函数推送一个新的堆栈帧并跳转到`add_fun`标签。
4.  *Add* 将两个操作数弹出堆栈，并将它们相加的结果推回。
5.  *Ret* 从调用堆栈中弹出帧，并通过将 IP 设置回 3 来返回。
6.  从调用堆栈中弹出最后一帧并结束程序。

```
#[derive(Clone, Debug, PartialEq)]
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

fn push(machine: &mut Machine<Operand>, args: &[usize]) {
    let arg = machine.get_data(args[0]).clone();
    machine.operand_push(arg)
}

fn add(machine: &mut Machine<Operand>, _args: &[usize]) {
    let rhs = machine.operand_pop().to_i().unwrap();
    let lhs = machine.operand_pop().to_i().unwrap();
    machine.operand_push(Operand::I(lhs + rhs));
}

fn call(machine: &mut Machine<Operand>, args: &[usize]) {
    let label = machine.get_data(args[0]).clone();
    machine.call(label.to_s().unwrap());
}

fn ret(machine: &mut Machine<Operand>, _args: &[usize]) {
    machine.ret();
}

fn instruction_table() -> InstructionTable<Operand> {
    let mut it = InstructionTable::new();
    it.insert(Instruction::new(0, "push", 1, push));
    it.insert(Instruction::new(1, "add", 0, add));
    it.insert(Instruction::new(2, "call", 1, call));
    it.insert(Instruction::new(3, "ret", 0, ret));
    it
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

#[test]
fn example() {
    let it = instruction_table();
    let mut builder: Builder<Operand> = Builder::new(&it);
    builder.push("push", vec![Operand::from(3)]);
    builder.push("push", vec![Operand::from(4)]);
    builder.push("call", vec![Operand::from("add_fun")]);
    builder.push("ret", vec![]);
    builder.label("add_fun");
    builder.push("add", vec![]);
    builder.push("ret", vec![]);
    let mut machine: Machine<Operand> = Machine::new(Code::from(builder), &it);
    machine.run();
    let result = machine.operand_pop().to_i().unwrap();
    assert_eq!(result, 7);
} 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。现在我们有了函数调用。在下一集中，我将向你展示如何实现条件指令。