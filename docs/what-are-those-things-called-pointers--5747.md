# 那些叫做“指针”的东西是什么？

> 原文：<https://dev.to/superfola/what-are-those-things-called-pointers--5747>

我为什么要写这篇文章？在我的工程学校，我们有 C++课程，很多时候，学生不明白什么是*指针*，它们有什么用。我试图写一些尽可能简单的东西来解决这个问题，如果有任何遗漏/错误/无论什么，请不要犹豫告诉我！

# 那些叫“指针”的东西是什么？

当用我们称之为“低级”编程语言编程时，你有时会遇到所谓的*指针*。在这篇文章中，我们将回顾一下历史，描述一下计算机是如何工作的，然后解释一下这个奇怪的东西是什么:“指针”。

# 计算机如何存储变量？

在引擎盖下，计算机有我们称之为内存的东西来存储变量。基本上，它是一个小硬盘，专门用来存储二进制格式的数据。内存中的所有内容都存储为 0 和 1 的序列，以计算机可以理解的方式进行格式化。

创建程序时，可以创建不同类型的变量，如*数*(整数或浮点数)、*串*(字符序列)...而且都是以 0 和 1 的形式存储在内存中！

这意味着，当你创建一个类型为 *integer* 的新变量时，它将在内存中为它分配空间(通常为 4 个字节)，以便以二进制格式存储一个值，例如“10”。

# 一点历史

我们讨论了变量是如何存储在内存中的，让我们回顾一下历史来理解为什么要创建指针。

基本上，你可以想象我们可以在一台计算机上创建的变量的数量取决于你的计算机中可用内存的大小(以字节为单位)。回到过去，当它刚刚开始时，计算机的可用内存较少，正确地管理它是至关重要的。

程序中声明的每个变量都是在程序启动时创建的，分配给它的空间只有在程序关闭时才会释放。现在可能出现的问题是，如果你需要一个临时变量，比如一个方程，它就不是临时的了，因为它在程序的整个执行过程中都存在！

这里出现了“动态内存管理”。那些难听的话后面，有一个指针！“动态内存管理”的目标(我将把它写成 *DMM* ，因为它稍微短一点)是能够在*运行时*分配内存，例如为一个变量分配内存，并在不再需要时销毁它。我们现在可以在运行时创建一个临时变量来节省内存！

# *DMM*如何工作？

在伪代码中，我们可以这样写:

```
integer my_temporary_variable = allocate(integer); 
```

Enter fullscreen mode Exit fullscreen mode

这个伪函数 *allocate* 将不得不要求操作系统在内存中为一个*整数*搜索可用空间。如果 OS 找到了一些空间，它会在找到空闲空间的地方分配 *sizeof(integer)* (如前所述，通常是 4 个字节)，并返回分配到的地址。这个地址是一个整数上的*指针，因为它指向内存中的一个整数空间。*

然后，当我们需要使用存储在这个地址的值时，我们需要告诉我们的程序不要使用*指针*，而是在*指针*指向的地方分配什么。

作为比较，我们可以说:
当使用一个变量时，我们向操作系统发送一封信，请求这个变量的值。不需要知道这个变量在内存中的分配位置，操作系统知道。

当我们需要得到一个*指针*所指的值时，我们向操作系统发送一封带有*地址*的信，操作系统将转到这个地址，并把位于那里的任何东西发送给我们。

让我们用一些伪代码来说明这一点:

```
integer my_variable = 10;
print(my_variable);  // the OS knows where is 'my_variable'

// dynamically allocating memory
integer* pointer_to_int = allocate(integer);
// if the OS couldn't find space to allocate our integer, it returns the address 0
if (pointer_to_int == 0) {
    print("Could not find space for integer, aborting");
    abort();
} else {
    // otherwise, we can use our pointer
    // we use the notation '*variable' to tell the computer to get what is pointed by the pointer,
    //    instead of manipulating the pointer itself (which is basically an address, represented by an integer)
    *pointer_to_int = 12;
    print(pointer_to_int);  // will print something like '0x12ab45' (the value of the pointer: the address
                            //     of the variable allocated before)
    print(*pointer_to_int);  // will print '12', the value pointed by our pointer
    // do not forget to deallocate the space allocated before
    deallocate(pointer_to_int);
    // now, pointer_to_int points to nothing, the value was destroyed and space used was fred up
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望现在你能更好地理解指针的黑魔法！