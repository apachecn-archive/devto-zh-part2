# 构建基于堆栈的虚拟机，第 5 部分——机器

> 原文：<https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-5---the-machine-3jif>

在最后一集中，我们构建了一个代码生成器和一个程序表示，所以我们现在除了运行我们的虚拟机所需的最后一点之外，其他的都有了。

## 介绍机器

到目前为止，我们已经构建了组装程序所需的所有主要结构，我们有[代码](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-4---code-3lmi)、[指令](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-3---instructions-4b3a)和[堆栈](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-2---the-stack-d07)。在运行程序之前，我们需要做的最后一件重要的事情就是机器本身。就像行星船长一样，我们将把我们的组成部分组装成一个更大的整体。

这是我们的机器:

```
pub struct Machine<'a, T> {
  code: Code<T>,
  instruction_table: &'a InstructionTable<T>,
  ip: usize,
  operand_stack: Stack<T>
}

impl<'a, T> Machine<'a, T> {
  pub fn new(code: Code<T>, instruction_table: &'a InstructionTable<T>) -> Machine<'a, T> {
    Machine {
      code: code,
      instruction_table: instruction_table,
      ip: 0,
      operand_stack: Stack::new()
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以当我们创建机器时，我们传递代码和指令表。它将指令指针设置为零(即代码中的第一条指令)，创建一个空堆栈，并返回我们看中的新机器。

在运行任何代码之前，我们需要给它一些方便的助手方法，用于操作操作数堆栈和从数据段中检索数据。我们的指令使用这些参数，因为如果你记得的话，它们会接收一个对机器的可变引用作为它们的第一个参数。

```
impl<'a, T> Machine<'a, T> {

    pub fn operand_push(&mut self, value: T) {
        self.operand_stack.push(value);
    }

    pub fn operand_pop(&mut self) -> T {
        self.operand_stack.pop()
    }

    pub fn get_data(&self, idx: usize) -> &T {
        self.code
            .data
            .get(idx)
            .expect(&format!("Constant data is not present at index {}.", idx))
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

不，我们终于可以将我们的`run`方法添加到机器中了。这是一段合理的代码，所以让我们先把它分解成一些基本概念:

*   保持循环，直到指令指针(IP)与程序长度相同(即我们用完了程序)。
*   从我们的 IP 程序中读取操作码。
*   从我们的 IP + 1 程序中读取 arity。
*   从 0 到 arity 从程序中读取参数索引，并将它们推入参数向量。
*   调用带有参数的指令。

```
impl<'a, T> Machine<'a, T> {

    pub fn run(&mut self) {
        loop {
            if self.ip == self.code.code.len() {
                break;
            }

            let op_code = self.next_code();
            let arity = self.next_code();

            let instr = self.instruction_table.by_op_code(op_code).expect(&format!(
                "Unable to find instruction with op code {}",
                op_code
            ));

            let mut args: Vec<usize> = vec![];

            for _i in 0..arity {
                args.push(self.next_code());
            }

            let fun = instr.fun;
            fun(self, args.as_slice());
        }
    }

    fn next_code(&mut self) -> usize {
        let code = self.code.code[self.ip];
        self.ip = self.ip + 1;
        code
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有足够的基础设施来运行我们的第一个程序了！

我们要做一个非常简单的算术机器，因为我不用写太长的博文就能做到。我们需要两条指令: *Push* 获取数据并将其推入堆栈， *Add* 从堆栈中弹出两个操作数，将它们相加并将结果推回堆栈:

```
fn push(machine: &mut Machine<isize>, args: &[usize]) {
    let arg = *machine.get_data(args[0]);
    machine.operand_push(arg);
}

fn add(machine: &mut Machine<isize>, _args: &[usize]) {
    let rhs = machine.operand_pop();
    let lhs = machine.operand_pop();
    machine.operand_push(lhs + rhs);
}

fn instruction_table() -> InstructionTable<isize> {
    let mut it = InstructionTable::new();
    it.insert(Instruction::new(0, "push", 1, push));
    it.insert(Instruction::new(1, "add", 0, add));
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要我们的程序，它将把`2`和`3`加在一起:

```
fn build_program(it: &InstructionTable<isize>) -> Code<isize> {
    let mut builder = Builder::new(&it);
    builder.push("push", vec![2]);
    builder.push("push", vec![3]);
    builder.push("add", vec![]);
    Code::from(builder)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们运行它:

```
#[test]
fn addition_example() {
    let it = instruction_table();
    let code = build_program(&it);
    let mut machine = Machine::new(code, &it);
    machine.run();
    let result = machine.operand_pop();
    assert_eq!(result, 5);
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。我们已经在虚拟机上运行了第一个程序。当然没有函数调用用处不大，所以我们会在[下集](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-6---function-calls-2md5)添加。