# 构建基于堆栈的虚拟机，第 4 部分-代码

> 原文：<https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-4---code-3lmi>

欢迎回来。

现在我们有了我们的[指令](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-3---instructions-4b3a)和我们的[堆栈](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-2---the-stack-d07)，我们需要一个我们可以运行的程序。我把这个叫做`Code`。

那么什么是代码呢？

让我们看一个非常简单的汇编程序来获得一些想法:

```
@0 = 123
@1 = 456

.main:
  push @0
  push @1
  add 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个真正的汇编程序，而是我用为`stack-vm`调试输出想出的一个简化版本[，它大致基于](https://gitlab.com/huia-lang/stack-vm/blob/master/src/code/debug.rs) [LLVM IR 文本格式](https://llvm.org/docs/LangRef.html)。它向我们展示了一个程序的一些重要的东西。一个计划需要:

*   *数据* -这里常量`123`和`456`被赋予名称`@0`和`@1`。
*   *标签*——在这种情况下，标签`main`指向第一条指令(指令指针= 0)。为了拥有功能或流程控制，我们需要能够标记代码部分，以便我们可以轻松地跳转到它们。我们不会直接跳转到指令指针，因为我们可能会犯错误(记得[跳转到](http://www.u.arizona.edu/~rubinson/copyright_violations/Go_To_Considered_Harmful.html)？).
*   *说明*——我们节目的荤素；这里我们使用指令`push`两次，每次一个参数，然后我们调用`add`指令。

那么这归结为什么呢？我们的`Code`结构需要包含:

*   一个“数据段”——或者一列常量操作数。
*   一个“程序段”，或者一个指令操作码和数据段索引的列表。
*   标签到指令指针(或程序段索引)的映射。
*   为便于调试，操作码存储的指令名(符号)列表。

记住所有这些，下面是我们的`Code`类型:

```
pub struct Code<T> {
    pub symbols: Vec<(usize, String)>,
    pub code:    Vec<usize>,
    pub data:    Vec<T>,
    pub labels:  Vec<(usize, String)>
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过直接将值插入这些向量来编写软件，但是手工编写汇编代码并不常见。作为编译高级语言的产物，以编程方式生成代码要常见得多。为了做到这一点，我们将创建自己的构建器(有时称为“代码生成器”或“汇编器”)。

那么我们的构建者需要知道什么才能发挥作用呢？

*   第三部分中概述的说明表。这样我们就可以访问标签、操作码和指令。
*   *数据*、*标签*和*指令*很像我们的代码类型。

> 如果`Code`和`Builder`几乎一样，为什么要把它们分开呢？很高兴你问了。
> 我们*可以*将两个用例合并在一起，但它们分开的主要原因是因为构建器需要访问指令表，以便在插入指令时进行验证，但代码假设这个过程已经完成，可以用于序列化和反序列化到磁盘或执行。

这是我们的`Builder` :

```
pub struct Builder<'a, T: 'a> {
    pub instruction_table: &'a InstructionTable<T>,
    pub instructions:      Vec<usize>,
    pub labels:            WriteOnceTable<usize>,
    pub data:              Vec<T>,
}

impl<'a, T> Builder<'a, T> {
    pub fn new(instruction_table: &'a InstructionTable<T>) -> Builder<T> {
        let mut labels = WriteOnceTable::new();
        labels.insert("main", 0);
        Builder {
            instruction_table: &instruction_table,
            instructions:      vec![],
            labels:            labels,
            data:              vec![],
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 不要担心`WriteOnceTable`——它只是一个包装`HashMap`的包装器，只允许你设置一次值。

那么，我们的建设者需要什么样的行为呢？

*   将指令及其参数添加到程序中。
*   插入标签。

所以让我们先讨论添加指令，因为这是代码中最复杂的部分。

```
impl<'a, T> Builder<'a, T> {

    pub fn push(&mut self, name: &str, args: Vec<T>) {
        let instr = self
            .instruction_table
            .by_name(name)
            .expect(&format!("Unable to find instruction with name {:?}", name));

        if args.len() != instr.arity {
            panic!("Instruction {} has arity of {}, but you provided {} arguments.", instr.name, instr.arity, args.len())
        }

        self.instructions.push(instr.op_code);
        self.instructions.push(instr.arity);
        for arg in args {
            let pos = self.push_data(arg);
            self.instructions.push(pos);
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

因此，给定指令的名称和操作数的集合，我们采取以下步骤:

*   在指令表中查找指令。
*   验证给我们的参数数量与指令 arity 匹配。
*   将指令的操作码推入程序。
*   将指令的 arity 推入程序。这可能看起来没有必要，但是对于独立于`InstructionTable`的`Code`类型来说是必要的。
*   将每个参数放入数据段，它就是程序的索引。

现在来看标签:

```
impl<'a, T> Builder<'a, T> {

    pub fn label(&mut self, name: &str) {
        let idx = self.instructions.len();
        self.labels.insert(name, idx);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这是非常直接的；我们只需查找程序中当前指令的数量，并存储指向它的名称。

这项工作的最后一点是能够将一个`Builder`转换成一个`Code`。这是一项相对简单的任务:

*   从指令表中获取所有的指令名称(符号)。
*   抓住节目部分。
*   抓住数据部分。
*   抓住标签，按 IP 分类。
*   将它们全部填充到代码对象中。

```
impl<'a, T> From<Builder<'a, T>> for Code<T> {
    fn from(builder: Builder<T>) -> Code<T> {
        let symbols = builder.instruction_table.symbols();
        let code    = builder.instructions;
        let data    = builder.data;
        let mut labels = vec![];
        for key in builder.labels.keys() {
            let idx = builder.labels.get(key).unwrap();
            labels.push((*idx, key.clone()));
        }
        labels.sort_by(|lhs, rhs| {
            lhs.0.cmp(&rhs.0)
        });

        Code {
            symbols: symbols,
            code:    code,
            data:    data,
            labels:  labels
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

唷！对你来说那是一个相当密集的插曲。我没有包括测试，但是相信我[它们都在那里](https://gitlab.com/huia-lang/stack-vm/blob/master/src/code/mod.rs#L187)。在[下一集](https://dev.to/jimsy/building-a-stack-based-virtual-machine-part-5---the-machine-3jif)中，我们将把所有东西连接到一台机器上，实际运行一个程序！