# 为什么在给变量命名的时候不应该吝啬！

> 原文：<https://dev.to/tschaka1904/why-you-shouldnt-be-stingy-when-naming-variables-lb7>

最近，我接手了另一个项目，像往常一样，有一段阅读期，以了解实际发生了什么。这不是一个大项目，但是它的一个类有大约 1.5K 行包含 X 方法的代码，这些代码有时非常非常长。在这种时刻，我总是问自己:“是我还是这个课程的布局让它如此难以理解？”。我确信我并不总是最快的，能立刻理解所有事情的人。但是我认为每个开发人员都有义务至少让他或她的代码易于阅读和理解。其中一部分不是写 looooooooong 类，甚至是 looooooooooooonger 方法，而是我们如何命名事物。

我遇到了很多变量，比如:`p`、`c`、`pc`、`pkAdd`等等。我认为这不是一个好的方式。当然，如果你需要一个循环计数器，使用`c`。我想这是普遍现象，每个人都明白。基于此，我最近发了一条推文([https://twitter.com/tschaka1904/status/984409479444824064](https://twitter.com/tschaka1904/status/984409479444824064))，你能看吗？:D

重点是，有时候值得把事情写出来，用`project`、`category`、`projectCategory`和`addProjectCategory`。令人惊讶的是:你的 IDE 几乎为你完成了所有的魔法，🧙‍♂️！

保持你的代码 r！