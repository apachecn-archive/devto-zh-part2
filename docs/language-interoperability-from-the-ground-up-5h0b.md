# 从头开始的语言互操作性

> 原文：<https://dev.to/samwho/language-interoperability-from-the-ground-up-5h0b>

Ruby 中的函数如何调用 C 中的函数？Kotlin 中的函数如何调用 Java 中的函数？

众所周知，您可以从一种编程语言调用另一种编程语言的函数。这不仅是可能的，而且可以通过多种方式实现。但是它实际上是如何工作的呢？

这篇文章将自下而上，深入探讨互操作性。每一节都将建立在前一节的基础上，直到我们理解了使语言互操作性成为可能的机制。

# 谁应该看这篇帖子？

如果你从事编程已经有一段时间了，并且听到人们谈论一个库是有“Java 绑定”还是“Ruby 绑定”

如果你已经使用像 Kotlin 这样的语言有一段时间了，并且从 Kotlin 调用 Java 函数，并且想知道它到底是如何工作的。

如果你对复杂系统的血淋淋的细节有浓厚的兴趣，但是想要一个不做太多假设的解释，并且让例子尽可能简单和实用。

此外，如果您希望按照示例进行操作，您需要在您的计算机上安装以下程序，并且可以从命令行访问这些程序:`nm`、`gcc`、`nasm`、`gdb`、`javac`、`javap`、`kotlinc`、`kotlin`、`ruby`。本文中的所有示例都是在 Arch Linux 系统上编写和运行的，在 Mac 或 Windows 机器上运行时，它们不太可能像这里显示的那样工作。

# 为什么语言互通很重要？

大多数语言互操作性都涉及调用用低级语言编写的库的高级语言。例如，一个 Java 库，它提供了调用本地 OpenSSL 库的能力。或者是一个 Rust 库，它为用 c 编写的 cURL 库提供了一个更加惯用的 Rust API。

重复是不好的。当你用一种语言编写了一个复杂的库，用另一种语言重新实现它只是又一件需要忘记维护的事情。

此外，对于年轻的语言来说，能够充分利用现有的成果是一个好主意。如果你创造了一种新的语言，并包含了一种使用现有的本地库的机制，你就可以立即利用几十年的辛勤工作。

## 纯洁的好处

有时你可能会看到一个库被宣传为“纯 Ruby”实现，或者其他库的“纯 Java”实现。这些库将是其目标技术的完全重新实现。

例如，[这个](https://github.com/dain/leveldb)是 [LevelDB](https://github.com/google/leveldb) 的纯 Java 实现。这对于作者来说是一项繁重的工作，但好处是人们将能够从 Java 中使用 LevelDB，而不必在他们的系统上安装本机 LevelDB 库，并将其与他们的 Java 代码打包在一起进行部署。

虽然纯粹的重新实现可能需要大量的前期工作和维护，但它们可能更易于使用。

# 为什么要从底层做起，往上干？

互操作性的关键是找到共同点。对于计算机来说，共同点是在程序如何工作的较低层次上有一套标准和约定，允许语言之间相互交流。理解这些约定是理解语言何时可以互操作，何时不可以互操作的关键。

# 底层:汇编语言

```
mov eax, 10 
```

有一行“汇编代码”它是一条“指令”，由一个“助记符”、“T0”和两个“操作数”、“T1”和“T2”组成。

这是“move”指令，它指示我们的 CPU 将值`10`移入寄存器`eax`。它是名为“x86”的指令集的一部分，用于 32 位英特尔和 AMD CPU<sup id="fnref1">T31</sup>。

## 什么是寄存器？

寄存器是直接连接到 CPU 的小而快的存储位。如果你想对一些数据做一些事情，无论是加法、减法还是其他操作，数据都需要首先被加载到寄存器中。

如果您不习惯在这个级别编写代码，那么寄存器的概念可能看起来很傻。为什么我们不能在内存中存储 10，然后在那里对它进行操作呢？

因为这在物理上是不可能的。CPU 不直接连接到内存。它通过一系列缓存连接，所有内存访问都必须通过内存管理单元(MMU)。MMU 不能处理任何运算，这发生在算术逻辑单元，或者 ALU <sup id="fnref2">[2](#fn2)</sup> ，ALU 也是直接连接到 CPU 的。它只能获取寄存器中的数据。

[![The MMU and friends](img/37f43fafd7716307b5a98a79867a634f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hX3VIo_r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://samwho.co.img/mmu-and-friends.svg)

这不是按比例的。实际上，大约有一千字节的寄存器，几百千字节的 L1 高速缓存，一两兆字节的 L2 高速缓存，几十兆字节的三级高速缓存，然后内存通常是几十千兆字节。

## 程序流程

做数学运算是很棒的，但是对于我们来说，要编写做任何事情的代码，我们需要能够比较事物并根据结果做出决定。

```
mov     eax, 10
sub     eax, 5
mov     ebx, 5
test    eax, ebx
je equal

notequal:
jmp     exit

equal:
; do something here

exit:
; end of program 
```

我们以一些移动和一个减法(`sub`)开始这个片段。我们引入一个新的寄存器`ebx`。然后我们做一个`test`。`test`指令比较两个值，它们可以都是寄存器，或者其中一个可以是“立即”值，例如 10。`test`的结果存储在一个特殊的“标志”寄存器中，我们不直接接触它。相反，我们使用一系列“跳转”指令来读取标志寄存器并决定下一步运行哪条指令。

如果测试结果是两个值相等，`je`指令将跳转到我们代码中的一个点，用“标签”`equal`表示。否则，代码会进入下一条汇编指令，也就是我们决定放在`notequal`标签下面的内容。现在，我们只做一个`jmp`，这是一个无条件的跳转，到我们程序的结尾`exit`。

“标签”是这个片段中的一个新概念。它们不是指令，CPU 不会试图运行它们。相反，它们用来标记代码中可以跳转到的点，这就是我们在这里使用它们的目的。您可以通过查找尾随冒号来识别标签。

## 访问内存

到目前为止还不错，但我们目前只涉及到寄存器。最终我们将耗尽寄存器中的空间，不得不使用内存。这在组装中是如何工作的？

```
mov         eax, 0x7f00000
mov dword [eax], 123 
```

第一位应该是熟悉的，我们将十六进制值`0x7f00000`存储到`eax`中，但是我们对下一条指令做了一些奇怪的事情。

`[eax]`周围的方括号表示我们想要在`eax`内部的内存地址中存储一个值。`dword`关键字表示我们想要存储一个 4 字节的值(“双字”)。我们需要关键字`dword`,因为没有它就无法推断出`123`在内存中应该有多大。

如果你熟悉 C 语言中的指针，这基本上就是指针的工作方式。你把你想要指向的内存地址存储在一个寄存器中，然后通过用方括号把寄存器名括起来来访问那个内存。

我们可以更进一步:

```
mov             eax, 0x7f00000
mov dword [eax + 1], 123 
```

这将在地址`0x7f00001`存储值`123`，比之前高一个字节。这样做不会修改`eax`的值，它只是让我们访问寄存器中的值加上一个偏移量。这在真实的汇编代码中很常见，稍后我们会看到原因。

## 堆栈

这是我们从高级语言中第一个可识别的概念。栈是语言通常存储短期变量的地方，但是它是如何工作的呢？

当你的程序启动时，一个叫做`esp`的特殊寄存器的值被设置到一个代表你被允许访问的堆栈空间顶部的内存位置，它本身就在你的程序的“地址空间”的顶部附近

您可以通过编写一个简单的 C 程序并在调试器中运行来检查这种现象:

```
int main() {
    return 42;
} 
```

用`gcc`编译，用`gdb`“GNU 调试器”:
运行

```
$ gcc main.c -o main -m32
$ gdb main
gdb> start
gdb> info register esp
esp 0xffffd468  0xffffd468 
```

当我们在 GDB 提示符下说`start`时，我们要求它将我们的程序加载到内存中，开始运行它，但是一旦它开始就暂停它。然后我们用`info register esp`检查`esp`的内容。

### 短暂的迂回进入记忆组织

为什么堆栈在地址空间的顶部附近？什么是地址空间？

当您的程序被加载到 32 位系统的内存中时，它会被分配一个 4GB 的地址空间:0x00000000 到 0xffffffff。无论您的机器安装了多少物理内存，都会发生这种情况。映射这个“虚拟”地址空间是 MMU 执行的另一项工作，它的细节超出了本文的范围。

[![Memory layout visualised](img/7aa5ac7af692fbce3d38a27df387e138.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rHc5z7Gd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://samwho.co.img/memory-layout.svg)

这个简化的内存视图显示，我们的程序被加载到地址空间中相当低的位置，我们的堆栈在顶部附近，然后向下增长，然后我们有一个神秘的地方，称为“堆”，就在我们程序的上面。这对于语言互操作性并不重要，但是堆存储的是长期数据，例如在 C 中使用`malloc()`或在 C++中使用`new`分配的数据。

这允许相当有效地利用可用空间。实际上，栈的大小被限制在几兆字节，超过这个数会导致你的程序崩溃。堆可以一直增长到堆栈的底部，但不会更远。如果它试图与堆栈重叠，您的`malloc()`调用将开始失败，这将导致许多程序崩溃。

### 回栈

x86 指令集为我们提供了一些在堆栈中添加和删除值的指令。

```
push    1
push    2
push    3
pop     eax
pop     ebx
pop     ecx 
```

在这个指令序列的末尾，`eax`的值将是 3，`ebx`的值将是 2，`ecx`的值将是 1。不信任我？我们可以通过一些小的修改来验证这一点。

```
global main
main:
        push    1
        push    2
        push    3
        pop     eax
        pop     ebx
        pop     ecx
        ret 
```

将其保存到名为`stack.s`的文件中，并运行以下命令:

```
$ nasm -f elf stack.s
$ gcc -m32 stack.o -o stack 
```

如果你没有`nasm`你需要安装它。这是一种叫做“汇编程序”的程序，它可以接受汇编指令，并把它们编译成机器代码。

现在，我们的工作目录中有一个名为“stack”的可执行文件。这是一个真正的程序，你可以像其他程序一样运行。我们必须对它做的唯一修改是给它一个`main`标签，并确保它用`ret`指令正确地将控制权返回给操作系统。稍后我们将更详细地探索`ret`。

运行这个程序实际上没有任何作用。它会运行，但会无声无息地退出 <sup id="fnref3">[3](#fn3)</sup> 。为了查看它内部发生了什么，我们将再次需要一个调试器。

```
$ gdb stack
gdb> break *&main+9
gdb> run
gdb> info registers eax ebx ecx 
```

这个命令序列的输出应该验证我前面所说的那些寄存器的内容。`gdb`允许我们加载一个程序，运行它直到某个点(`break *&main+9`是我们告诉`gdb`在`ret`指令之前停止)，然后检查程序状态。

## 那么`push`和`pop`到底在做什么呢？

`push`和`pop`指令是简写的，可以扩展为如下:

```
; push
sub     esp, 4
mov     [esp], operand

; pop
mov     operand, [esp]
add     esp, 4 
```

所有这些您在前面的章节中应该已经很熟悉了，并且清楚地展示了栈是如何随着内容的添加和删除而向下增长和向上收缩的。

## 组装中的功能

语言互操作性，从最根本上来说，是从不同的语言调用用一种语言编写的函数的能力。那么我们如何在汇编中定义和调用函数呢？

根据我们目前掌握的知识，您可能会认为函数调用是这样的:

```
main:
        jmp myfunction

myfunction:
        ; do things here 
```

一个标签表示你的函数在内存中的位置，一个跳转指令来调用它。这种方法有两个关键问题:它不处理向函数传递参数或从函数返回值，也不处理在函数结束时将控制权返回给调用者。

我们可以通过将参数和返回值放入堆栈来解决第一个问题:

```
main:
        push    1
        push    2
        jmp     add
        pop     eax

add:
        pop     eax
        pop     ebx
        add     eax, ebx
        push    eax 
```

如果我们的`add`函数能够在完成时跳回到调用者，这将会非常好。此刻，当`add`结束时，程序结束。在理想的情况下，它会在`main`中的`jmp`之后返回。

如果我们保存调用函数时的位置，并在函数完成时跳回到那个位置，会怎么样？

`eip`寄存器保存当前执行指令的位置。利用这些知识，我们可以这样做:

```
main:
        push    1
        push    2
        push    eip
        jmp     add
        pop     eax

add:
        pop     edx ; store the return address for later
        pop     eax ; 2
        pop     ebx ; 1
        add     eax, ebx
        push    eax
        mov     eip, edx 
```

我们快到了。不过，这种方法有几个问题:我们对`esp`的修改比我们实际需要的多得多，而且 x86 不允许你将`mov`的东西放入`eip`。

下面是一个编译器会为我们上面的例子*实际生成的内容:* 

```
main:
        push    ebp
        mov     ebp, esp
        push    2
        push    1
        call    add
        add     esp, 8
        pop     ebp
        ret

add:
        push    ebp
        mov     ebp, esp
        mov     edx, dword [ebp + 8]
        mov     eax, dword [ebp + 12]
        add     eax, edx
        pop     ebp
        ret 
```

这需要理解很多东西，所以让我们一行一行地过一遍。

我们引入了一个新的特殊寄存器:`ebp`。这是一个“基址指针”寄存器，它的作用是在函数被调用时作为一个指向栈顶的指针。每一个函数都是从在堆栈上保存旧的基址指针值开始，然后把新的栈顶移入`ebp`。

接下来，我们将熟悉的参数推送到堆栈上。至少我们做对了。然后我们用一个以前没见过的指令叫做`call`。`call`可以扩展为以下:

```
; call
push    eip + 2
jmp     operand 
```

`eip + 2`表示指令*在它下面的`jmp`*之后。这个值是什么并不重要，只要把它想成是把在`call`指令之后的指令的地址推到堆栈上，这样我们以后可以引用它。

然后控制被传递给`add`，其遵循类似的模式。基址指针被保存，堆栈指针成为新的基址指针，然后我们可以看到基址指针的运行。

```
mov     edx, dword [ebp + 8]
mov     eax, dword [ebp + 12] 
```

这段代码将`add`的两个参数从堆栈中取出，放入寄存器中，这样我们就可以对它们进行操作。但是为什么是 8 个字节和 12 个字节呢？

还记得我们将参数推送到堆栈上，然后`call`将地址推回。这意味着堆栈的前 4 个字节是一个返回地址，之后的 8 个字节是我们的参数。为了得到第一个参数，您需要从堆栈指针上移 8 个字节(因为它是向下增长的)，为了得到第二个参数，您需要上移 12 个字节。

[![Stack frame](img/06eaa374730728894e2cbfd9820add9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WXGzda1k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://samwho.co.img/stack-frame.svg)

这有一个额外的好处，即不需要我们用每一条`push`和`pop`指令来修改`esp`。这是一个很小的节省，但是当你考虑到对每个函数调用的每个参数都必须这样做时，这就增加了。

当我们在`add`函数中完成了我们需要做的事情后，我们执行开始时的步骤，但顺序相反。

```
pop     ebp
ret 
```

`ret`指令很特殊，因为它允许我们设置`eip`的值。它弹出`call`为我们推送的返回值并跳转到它，将程序的控制权交还给调用函数。

同样的事情发生在`main`中，除了有一个微妙的区别。`add
esp, 8`是将我们推入堆栈的参数“释放”给`add`所必需的。如果我们不这样做，`pop ebp`将不会正确地恢复基指针，我们可能会试图引用我们从未打算引用的内存，从而使我们的程序崩溃。

最后，您会注意到`add`在完成后并没有将其结果推回堆栈。它将结果留在`eax`中。这是因为函数的返回值通常存储在`eax`中。

# 约定俗成

我们刚刚尽可能深入地研究了函数调用在 x86 中是如何工作的，现在让我们为我们所学的每一个东西命名。

在调用函数之前保存基指针和移动堆栈指针被称为**函数序言**。

在一个函数调用后恢复堆栈指针和基指针被称为**函数尾声**。

这两个概念，以及在`eax`中返回值和在堆栈中存储你的函数参数，组成了所谓的**调用约定**，而调用约定是一个更大概念的一部分，这个概念被称为**应用二进制接口**，或 **ABI** 。具体来说，到目前为止我所描述的都是 ABI 系统的一部分，几乎所有的 Unix 和类 Unix 操作系统都使用这个系统。

在我们开始从用一种语言编写的函数调用用另一种语言编写的函数之前，我们需要知道最后一件事。

# 对象文件

当你编译一个 C 程序时，很多事情都是在幕后发生的。对于语言互操作性，需要理解的最重要的概念是“目标文件”

如果你的程序由两个`.c`文件组成，编译器做的第一件事就是把每个`.c`文件编译成一个`.o`文件。在`.c`文件和`.o`文件之间通常存在一对一的映射。汇编，或者`.s`文件也是如此。

Linux 和许多其他类似 Unix 的操作系统上的目标文件是一种二进制格式，称为“可执行和可链接格式”，简称 ELF。如果您对 ELF 文件中的全部数据感兴趣，您可以使用`readelf -a <elffile>`命令找到答案。不过，输出可能会非常令人眼花缭乱，我们在这里只对它的一个特性感兴趣。

## 符号表

为了解释符号表，让我们将之前的汇编分成两个`.s`文件:

`add.s` :

```
add:
        push    ebp
        mov     ebp, esp
        mov     edx, dword [ebp + 8]
        mov     eax, dword [ebp + 12]
        add     eax, edx
        pop     ebp
        ret 
```

`main.s` :

```
main:
    push    ebp
    mov     ebp, esp
    push    2
    push    1
    call    add
    add     esp, 8
    pop     ebp
    ret 
```

组装两个文件:

```
$ nasm -f elf add.s
$ nasm -f elf main.s
main.s:6: error symbol `add' undefined 
```

哎呦。我们的汇编程序不喜欢我们调用一个未定义的函数。这很棘手，因为我们想在别处定义该函数，并在`main.s`中调用它，但这里似乎汇编程序不允许这样做。

问题是这个文件中没有定义`add`符号。如果我们想告诉汇编程序我们打算在另一个文件中找到这个符号，我们需要这样说。将此行添加到`main.s` :
的顶部

```
extern add 
```

现在它应该毫无怨言地组装起来。在我们继续之前，请查看一下您的工作目录。你应该有两个`.o`文件:`main.o`和`add.o`。我们可以用一个叫做`nm` :
的工具来查看它们符号表的内容

```
$ nm add.o
00000000 t add

$ nm main.o
         U add
00000000 t main 
```

第一列是符号的地址，第二列是符号的类型，第三列是符号的名称。请注意，符号名称与我们的标签名称相匹配。还要注意`main.o`有一个`U`类型的`add`符号。`U`表示“未定义”，这意味着当我们稍后将这些目标文件链接在一起时，需要为它找到一个定义。

我们定义的两个函数都有符号类型`t`。这意味着符号指向一些代码。

为了从这些目标文件中创建一个可执行文件，我们运行:

```
$ gcc -m32 main.o add.o -o main 
```

它会对你大喊大叫，声称找不到`main`或`add`。怎么回事？

除非我们明确说明，否则当编译器将一个目标文件中的符号链接在一起时，它们不能被其他目标文件使用。为了解决这个问题，我们需要添加:

```
global add 
```

到顶部`add.s`和:

```
global main 
```

到`main.s`的顶端。这允许符号被链接，结果是编译器现在接受我们的目标文件并创建一个可执行文件。

```
$ nm main 
```

这会产生很多输出，因为编译器必须链接很多管理性的东西，比如 libc 构造函数和析构函数处理程序，分别是`__libc_csu_init`和`__ libc_csu_fini`。不要担心它们，重要的是`main`和`add`都被定义了，程序运行时没有抱怨。

[![Assemble and link diagram](img/4051735d3291d9ebcb0b29bef0686fa4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yU07TZH0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://samwho.co.img/assemble-link.svg)

# 从 C++调用 C 函数

我们上一个层次，看一些 C 和 C++。

`main.cpp` :

```
extern int inc(int i);

int main() {
    return inc(2);
} 
```

这里的第一行是我们告诉编译器期望在另一个文件中找到一个名为`inc`的函数。

`inc.c` :

```
int inc(int i) {
    return i + 1;
} 
```

下面是我们需要分别编译它们，然后将它们连接在一起的一系列步骤:

```
$ gcc -c main.cpp -o main.o
$ gcc -c inc.c -o inc.o
$ gcc inc.o main.o 
```

不幸的是，由于一个看似深不可测的错误，这个失败了:

```
main.o: In function `main'
main.cpp:(.text+0xa): undefined reference to `inc(int)'
collect2: error: ld return 1 exit status 
```

但是我们提供了一个目标文件，其中有一个`inc(int)`的定义，我们确保告诉编译器期望找到一个名为`inc(int)`的函数，为什么它找不到呢？

## 名邙岭

遗憾的是，C++不能提供它想要的所有特性，而不偏离 ABI 系统一点点。当你用 C 语言编译一个函数时，这个函数会被赋予一个符号，这个符号就是你给它起的名字，这样其他人就可以用这个名字来调用它。

默认情况下，C++不会这样做。除了你给它起的名字，C++还附加了关于函数的返回类型和参数类型的信息。如果函数在一个类中，则还包括关于该类的信息。这样做是为了允许你重载一个函数的名字，这样你就可以定义一个函数的多个变量，这些变量采用不同的参数。这就叫**名莽**。

结果，当我们编译我们的`main.cpp`文件时，它被告知寻找一个名为`_Z3inci`的函数，而不是普通的旧的`inc`。我们的`inc.c`文件提供了一个名为`inc`的函数，因此，如果没有一点帮助，这两种语言就无法互操作。

幸运的是，通过在我们的`main.cpp` :
中添加 4 个字符，问题很容易解决

```
extern "C" int inc(int i); 
```

添加的这个`"C"`告诉 C++编译这个文件，以寻找一个具有普通老式 C 风格调用约定的函数，这包括使用普通老式 C 名`inc`。尝试编译这段代码现在应该可以正常工作了。

## 从 C 中调用 C++函数

这种关系在另一个方向上也类似。如果我们想用 C++写一个函数，但是以一种 C 程序可以调用它的方式公开它，我们需要在那个函数上使用`extern "C"`:

```
extern "C" int inc(int i) {
    return i + 1;
} 
```

# Java 呢？

到目前为止，我们讨论的是语言互操作性如何在本地语言之间工作的基本基础，但是运行在虚拟机上的语言，比如 Java 呢？

这个过程有点复杂，但是可行。出现这个问题是因为 Java 运行在一个叫做 Java 虚拟机(JVM)的东西上，JVM 充当 Java 代码和运行它的机器之间的一个间接层。因此，Java 不能像 C 和 C++那样直接链接到本地库。我们必须引入一个在本地世界和 JVM 世界之间进行转换的层。

幸运的是，Java 背后的人对此想了很多，他们提出了“Java 本地接口”，或 JNI。这是让 Java 代码与本地代码对话的公认方法，下面是它的工作方式:

```
public class Interop {
  static {
    System.loadLibrary("inc");
  }

  public static native int inc(int i);

  public static void main(String... args) {
    System.out.println(inc(2));
  }
} 
```

注意关键字`native`的使用。这告诉 Java，这个函数的实现是在某处的某个本机代码中定义的。`System.loadLibrary("inc")`行将在 Java 的库路径中搜索一个名为`libinc.so`的库，当它找到时，我们将能够使用 Java 函数`inc`来调用我们的本地代码！

但是我们怎么做呢？

步骤 1:从我们的代码生成 JNI 头文件。

```
$ javac -h . Interop.java 
```

`-h .`告诉`javac`在当前目录下生成一个名为`Interop.h`的文件。这将定义我们必须实现的功能。结果文件如下:

```
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h> /* Header for class Interop */

#ifndef _Included_Interop
#define _Included_Interop
#ifdef __cplusplus extern "C" {
#endif /*
 * Class: Interop
 * Method: inc
 * Signature: (I)I
 */
JNIEXPORT jint JNICALL Java_Interop_inc
  (JNIEnv *, jclass, jint);

#ifdef __cplusplus }
#endif
#endif 
```

这看起来比实际可怕多了。包含`_Included_Interop`的行只是“标题保护”,确保我们不会意外地将这个文件包含两次，并且`__cplusplus`位检查我们是否正在编译为 C++代码，如果是的话，将所有内容包装在一个`extern "C"`块中，这一点你在本文前面会记得。

剩下的就是我们要实现的 JNI 函数的定义:

```
JNIEXPORT jint JNICALL Java_Interop_inc
  (JNIEnv *, jclass, jint); 
```

它可能看起来不像，但这确实是一个函数声明。我们这样实现它:

`inc-jni.c` :

```
#include <jni.h>
#include "Interop.h" 
JNIEXPORT jint JNICALL Java_Interop_inc
  (JNIEnv* env, jclass class, jint i) {
    return (jint)(i + 1);
} 
```

强制转换是为了确保我们的整数是 Java 期望的大小。我们看到的所有 Java 特有的东西都需要`jni.h` include，比如`jint`和`jclass`。

为了编译它，我们需要执行一个非常复杂的`gcc`调用:

```
$ gcc -I"$JAVA_HOME\include" -I"$JAVA_HOME\include\linux" -fPIC -shared -o libinc.so inc-jni.c 
```

`-I`标志告诉`gcc`在哪里可以找到头文件，我们需要它来让`#include <jni.h>`行工作。`-fPIC -shared`标志创建一种特殊类型的对象文件，称为“共享对象”这种类型的目标文件的特别之处在于，它可以在运行时加载到您的进程中，而不是直接针对它进行编译。按照惯例，共享对象文件被命名为`lib<something>.so`。

现在我们可以像这样运行 Java 代码:

```
$ java -Djava.library.path=. Interop
3 
```

瞧啊。我们成功地从 Java 中调用了我们的本机`inc`实现！多酷啊。

# Ruby 呢？

Ruby 对 Java 有类似的方法。它公开了一个叫做“Ruby 本地扩展”的 API，你可以用它来调用 Ruby 中的本地函数。鉴于我们已经探索了 Java 实现这一点的方式，而 Ruby 的方式也没有太大的不同，我想使用 Ruby 来关注一种不同的、更方便的调用本机代码的方式。

## ffi

`ffi`代表“外来函数接口”，它是一个 Ruby gem，允许我们通过很少的设置调用现有共享对象文件中的函数。首先，我们安装 ffi gem:

```
$ gem install ffi 
```

然后我们需要将原始的`inc.c`文件编译成一个共享对象:

```
$ gcc -fPIC -shared -o libinc.so inc.c 
```

然后，我们可以编写以下简短的 Ruby 代码片段，并调用我们的`inc`函数:

```
require 'ffi'

module Native
  extend FFI::Library
  ffi_lib './libinc.so'
  attach_function :inc, [:int], :int
end

puts Native.inc(2) 
```

这种方法比 Java 的 JNI 少得多，并且允许我们调用现有的共享对象而不用修改它们。例如，您可以直接呼叫`libc.so` :

```
require 'ffi'

module Libc
  extend FFI::Library
  ffi_lib 'c'
  attach_function :puts, [:string], :int
end

Libc.puts "Hello, libc!" 
```

注意，我们只需指定`'c'`作为库名。这是因为类 Unix 系统有标准化的路径来查找库，通常包括`/lib`和`/usr/lib`。默认情况下，`ffi`会寻找带有命名模式`lib<name>.so`的库。如果你这样做了`ls /usr/lib/libc.so`，你应该会发现在那个路径中存在一个文件。

# Java 为什么没有 FFI？

确实如此。有一个名为 [JNA](https://github.com/java-native-access/jna/blob/master/www/GettingStarted.md) 的库做着和 Ruby 的 FFI 库一样的工作。

```
import com.sun.jna.Library;
import com.sun.jna.Native;

class JNA {
  public interface Libc extends Library {
    Libc INSTANCE = (Libc)Native.loadLibrary("c", Libc.class);
    public int puts(String s);
  }

  public static void main(String... args) {
    Libc.INSTANCE.puts("Hello, libc!");
  }
} 
```

我们必须下载 JNA 图书馆，你可以从这里下载。然后我们编译并运行 Java 代码，包括 JNA 库:

```
$ javac -classpath jna-4.1.0.jar JNA.java
$ java -classpath jna-4.1.0.jar:. JNA
Hello, libc! 
```

## 如果 FFI 如此方便，为什么还会有人使用一种语言的本地接口？

许多语言都有某种形式的 FFI 库，它们非常方便调用现有的本地库。然而，问题在于这是单向交流。例如，你正在调用的库不能直接修改 Java 对象。它不能调用 Java 代码。唯一的方法是使用 Java 的 JNI 或者 Ruby 的本地扩展，因为它们向你展示了一个 API。

但是，如果您不需要语言之间的双向通信，并且您只想调用现有的本机代码，那么 FFI 是一个不错的选择。

# Hold 住了，那么 Kotlin 如何调用 Java 写的函数呢？

Kotlin 是一种运行在 JVM 上的相对较新的语言。

*“等等，Java 不是在 JVM 上运行的吗？”*

确实如此！但是 JVM 不仅仅是 Java 的 T1，它是一个通用的虚拟机，编译器可以像本地机器一样生成“机器码”。JVM 机器码通常被称为“字节码”，因为每条指令的长度都是一个字节。是的，这将指令集限制为 256 条指令。远少于 x86 中的[2034](https://stefanheule.com/blog/how-many-x86-64-instructions-are-there-anyway/)条指令。

在这方面，您可以认为 JVM 是一台“本地”机器。这是它旨在呈现给编译器和用户的抽象。唯一的区别是，运行代码的本机是由一个叫做 JVM 的软件模拟的。

让我们看一些 Java 和 Kotlin 代码的例子。

`Hello.java` :

```
public class Hello {
  public static void world() {
    System.out.println("Hello, world!");
  }
} 
```

`Main.kt` :

```
fun main(args: Array<String>) {
  Hello.world()
} 
```

让我们编译并运行这段代码。

```
$ javac Hello.java
$ kotlinc -classpath . Main.kt
$ kotlin MainKt
Hello, world! 
```

现在我们反汇编代码，看看到底发生了什么:

```
$ javap -c MainKt.class
Compiled from "Main.kt"
public final class MainKt {
  public static final void main(java.lang.String[]);
    Code:
      0: aload_0
      1: ldc #9 // String args
      3: invokestatic #15 // Method kotlin/jvm/internal/Intrinsics.checkParameterIsNotNull:(Ljava/lang/Object;Ljava/lang/String;)V
      6: invokestatic #21 // Method Hello.world:()V
      9: return
} 
```

这看起来与反汇编的本机代码非常不同。“Code:”标题下的位是实际运行的字节码。左边的数字是代码中的字节偏移量，下一列是“操作码”,操作码后面的是操作数。

*“但是你说 JVM 字节码的长度是一个字节，为什么左边的字节索引每条指令增加 1 个字节以上？”*

*操作码*的长度为一个字节。操作数构成了空间的其余部分。例如，`invokestatic`操作码在操作数中加入了两个额外的字节:一个字节引用被调用的类，另一个字节引用该类的方法。

*“数字 21 如何引用我们的 Hello.world()方法？”*

JVM 类文件中的一个基本概念是“常量池”`javap`默认不输出，但是我们可以索取:

```
$ javap -verbose MainKt.class
Classfile /tmp/MainKt.class
  Last modified May 7, 2018; size 735 bytes
  MD5 checksum f3ce23c2362512e852a5c91a1053c198
  Compiled from "Main.kt"
public final class MainKt
  minor version: 0
  major version: 50
  flags: ACC_PUBLIC, ACC_FINAL, ACC_SUPER
Constant pool:
  ...
  #16 = Utf8 Hello
  #17 = Class #16
  #18 = Utf8 world
  #19 = Utf8 ()V
  #20 = NameAndType #18:#19
  #21 = Methodref #17.#20
  ... 
```

我已经修剪了很多输出，但是保留了常量池中的相关条目。16、18 和 19 是原始的 UTF-8 字符串，它们给出了名称“Hello”、“world”和“()V”，也就是说它是一个返回 void 的函数。`invokestatic`操作码专门获取常量池中的索引操作数，常量池中的条目可以引用常量池中的其他条目。

如果我们试图单独编译`Main.kt`，而没有将 Hello.class 添加到类路径中，我们会得到一个错误，这个错误与我们试图链接一个没有所需的所有符号定义的目标文件时得到的错误非常相似:

```
$ kotlinc Main.kt
Main.kt:2:2: error: unresolved reference: Hello 
```

因为这些语言，Java 和 Kotlin，都使用相同的 ABI 和相同的指令集运行，所以我们能够使用 ABI 的调用约定来进行跨语言边界的函数调用。

# 结论

我们已经走了很长一段路。从原始的本机汇编代码到在同一虚拟机上运行的语言之间调用函数。

有了关于调用约定、指令集、目标文件和 FFI 库的知识，你现在应该已经准备好探索本文中没有提到的语言如何调用用其他语言编写的函数。

* * *

1.  如果这些话对你没有任何意义，不要太担心。它们只是你可能在野外看到的术语，当你看到时，你会知道这篇文章中的东西就是它们所指的。 [↩](#fnref1)

2.  除了在浮点单元或 FPU 上发生的浮点计算。 [↩](#fnref2)

3.  不是 100%无声。当程序完成运行时，检查其退出状态。为什么你认为它以现在的状态存在？ [↩](#fnref3)