# 为什么解释型语言的 IDE 是“多余的”

> 原文：<https://dev.to/hawkinjs/why-is-an-ide-for-interpreted-languages-overkill-7c3>

所以我经常听到这样的话“一个 IDE 对于 Python/Ruby/JavaScript 来说是大材小用，用 Vim/Emacs/Nano/IDLE 就好了。”

这是为什么呢？我写的解释语言和其他人一样多，但是根据我的经验，我

*   还需要调试器吗
*   仍然需要标签定义来揭开框架的神秘面纱
*   仍然想要类型
*   鄙视“打印语句调试”

好了，您的 vim 设置有 ctags/cscope 用于标记定义，您“非常了解您的代码，所以不需要类型。”如果 6 个月后出现了一个 bug 呢？还记得`a`是什么吗？`i`？`j`？`temp`？

所以我的问题的根源是，解释型语言不需要 ide 这个咒语是从哪里来的？任何语言都不需要 IDE，但是是什么让编译语言如此受欢迎呢？